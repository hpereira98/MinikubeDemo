# MinikubeDemo
Local Kubernetes Deployment with Minikube

## Requirements
- Minikube
- Docker
- Kubectl

## Steps

### 1 - Setup
```bash
# create demo folder
mkdir my-nginx-app && cd my-nginx-app

# start minikube
minikube start

# point shell to minikube's docker-daemon
eval $(minikube -p minikube docker-env)

# create Dockerfile
touch Dockerfile
```

### 2 - Dockerfile
```
FROM nginx
MAINTAINER Henrique henrique.pereira@uphold.com    
```

### 3 - Build NGINX Docker container
```bash
docker build -t nginx:1.17.10 .

# check if correctly built
docker images
```

### 4 - Create Kubernetes deployment
```bash
kubectl create deployment my-nginx  --image=nginx:1.17.10

# check Deployment and Pods
kubectl get deployments,pods -l app=my-nginx
```

### 5 - Scale Deployment to 3 replicas
```bash
kubectl scale deployment my-nginx --replicas=3 

# check new replicated Pods
kubectl get pods -l app=my-nginx

# describe pod
kubectl describe pod <pod_name>
```

### 6 - Create service to access pods and container
```bash
kubectl create service nodeport my-nginx --tcp=80:80

# get minikube public ip
minikube ip

# get execution port
kubectl get services -l app=my-nginx 
```

### 7 - Access the application through `<minikube ip>:<mapped port>`

### 8 - Get the Deployment's Revision History
```bash
kubectl rollout history deployment my-nginx 

# get information on first revision
kubectl rollout history deployment my-nginx --revision=1
```

### 9 - Build a new NGINX image with version 1.18.0
```bash
docker build -t nginx:1.18.0 .

# check images
docker images
```

### 10 - Set Deployment to use new image
```bash
kubectl set image deployment my-nginx nginx=nginx:1.18.0 --record

# check if it was updated
kubectl describe deployment my-nginx
```

### 11 - Check Revision History again
```bash
kubectl rollout history deployment my-nginx 

# get details on revision 2
kubectl rollout history deployment my-nginx --revision=2 

# get deployment information
kubectl get deployments,replicaset,pods -l app=my-nginx
```

### 12 - Rollback Deployment to revision 1
```bash
kubectl rollout undo deployment my-nginx –to-revision=1

# check if it was rolled out successfully
kubectl rollout history deployment my-nginx

# get details on revision 3
kubectl rollout history deployment my-nginx --revision=3

# check deployment
kubectl get deployments,replicasets,pods -l app=my-nginx
```
