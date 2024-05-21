# Project Name

A Simple Notes Application (i.e) containerized and build and push to dockerhub.It is then accessed through Kubernetes Cluster by exposing through service.yaml+deployment.yaml

## Prerequisites

Before running this project, ensure you have the following installed:

- Minikube 
- [Docker](https://docs.docker.com/get-docker/)
- [Git](https://git-scm.com/downloads)

## Getting Started

### Cloning the Repository 

###  Step 1: Test the sample application locally
Clone the repository to your local machine using the following command:

```bash
git clone https://github.com/your-username/Kubernetes+Docker-App.git
```

Navigate to the project directory:

```bash
cd Kubernetes+Docker-App
```

Install dependencies with the following command

```bash
npm install 
```

Run the application locally to see if the application works perfectly well

```bash
npm start
```

### Step 2: Containerize the application

```bash
FROM node:14-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "npm", "start" ]
```

For Building the docker Image

```bash
docker build -t $urdockerhubname/$dockerimagename .
```

Push to DokcerHub
Before Pushing to dockerhub u need to login first to dockerhub

```bash
ocker push $urdockerhubname/$dockerimagename
```

### Step 4:Start Minikube 

### Step 5:Write Deployement+Service.yaml files and apply them

Below is deployment.yaml file.

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: notes-app-deployment
  labels:
    app: notes-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: notes-app
  template:
    metadata:
      labels:
        app: notes-app
    spec:
      containers:
      - name: notes-app-deployment
        image: pavansa/notes-app
        resources:
          requests:
            cpu: "100m"
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
```

Below is service.yaml

```bash
apiVersion: v1
# Indicates this as a service
kind: Service
metadata:
 # Service name
 name: notes-app-deployment
spec:
 selector:
   # Selector for Pods
   app: notes-app
 ports:
   # Port Map
 - port: 80
   targetPort: 3000
   protocol: TCP
 type: LoadBalancer
```

Apply the manifest files with the following commands. Starting with deployment and then service yaml file.

```bash
kubectl apply -f deployment.yaml
```

```bash
kubectl apply -f service.yaml
```

Verify the pods are running properly as expected after applying the kubectl apply commands.

```bash
kubectl get pods
```
Now you need to Expose your kubernetes service

```bash
kubectl port-forward svc/notes-app-deployment 8080:80
```
Now you can see app running on the port
Forwarding from 127.0.0.1:8080 -> 3000
Forwarding from [::1]:8080 -> 3000
