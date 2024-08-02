# Create-a-Docker-Image-and-Deploy-It-to-Kubernetes

### Description

You are working as a DevOps engineer in an IT firm. You have been asked to create a Redis-based Docker image and deploy it on a Kubernetes cluster.

### Background of the problem statement:

Your organization wants to use Redis in a Kubernetes cluster for the data storage and caching purpose. The development team has asked you to create a Redis-based Docker image using a Dockerfile and deploy this image on a Kubernetes cluster.

You have also been asked to publish this image on your organization's Docker Hub account so that other team members can also access this image.

### You must use the following:

- Docker CLI: To create the Docker image using a Dockerfile
- Docker Hub: To publish the image
- Kubectl: To deploy the image on a Kubernetes cluster
 

### Following requirements should be met:

- Follow the above-mentioned specifications
- Make sure you create an account on Docker Hub to push the Docker image
- Document the step-by-step process involved in completing this task

## Solution:

### On Manager Node:
- sudo su -
- kubeadm init --ignore-preflight-errors=all
- mkdir -p $HOME/.kube
- sudo cp -i /etc/kubernetes/admin.confg $HOME/.kube/config
- sudo chown $(id -u):$(id -g) $HOME/.kube/config
- kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yml
- kubectl get nodes
- kubeadm token create --print-join-command
- Copy join command and put it in worker nodes
- kubectl get nodes ( we will have 3 nodes, 1 master, 2 workers )
- mkdir myproject
- cd myproject
- vim dockerfile

FROM alpine

RUN apk add --update redis

EXPOSE 6379

CMD["redis-server"]

- :wq!
- docker build -t myredisproject:v1 .
- docker images
- docker login
- Give User and Pass
- docker tag myredisproject  ramezchreide/myredisproject
- docker push ramezchreide/myredisproject

Note: docker tag myredisproject <usernameDockerHub>/<imageName>

### Deployment using Kubernetes
- kubectl run pod1 --image ramezchreide/myredisproject
- kubectl run <podName> --image <usernameDockerHub>/<imageName>
- kubectl get pods
- kubectl create deployment myproject --image ramezchreide/myredisproject --replicas 4
- kubectl get pods

other way:
- vim project-deployment.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: myproject-deploy
spec:
  replicas: 4
  selector:
    matchLabels:
      app: webserver
  template: # this pod template using which replicas will be created
    metadata:
      labels: # every replica/pod will have a unique name given by K8s
        app: webserver
    spec:
      containers:
        - name: c1
          image: ramezchreide/myredisproject
          
- wq!
- kubectl create -f project-deployment.yml
- kubectl get all

kubectl get pods -n kube-system

This allows you to see if network available

--- END OF SOLUTION ---

## Kubernetes
Kubernetes (K8s) is an container orchestration tool. It manages containers and can scale up/down containers and ensure availablity.
- Works in Cluster Mode where it is installed on one VM and it manages various worker nodes
- main VM is called master in kubernetes unlike docker swarm (manager)
- By default no containers will be scheduled on the Master node
- Kubernetes can also create containers of docker as well as other container looks like CRI-O, containerD, Rkt container..
- Kubernetes should have a unique Container runtime interface(container tool)
- There can only be 1 Container tool per Cluster
- Once containers have been created, Kubernetes can auto scale the containers
- Kubernetes allows us to store the data on Volumes storage outside the cluster -> Persistent Volume
- Kubernetes supports ingress load balancers
- Kubernetes is also available as a serivce on various Cloud Platforms
- Kuberenetes services are: EKS, AKS, GKE
- We have many objects to control various workflows on Kubernetes, to create a replica > POD, scaleup > replicaSet, update image > Deployment, statefulset, service object, daemon sets
- We can create jobs and cron jobs in kubernetes
- It also supports various deployment strategies like: Rolling update, recreate, blue and green deployment, canary deployment
- Kubernetes also have a GUI called as Kubernetes Dashboard
- Kubernetes provides objects like secrets to store sensitive data and configmap to store configurations in Kubernetes cluster
- Kubernetes will not create any images. Images are always created by container tool like docker that are pushed to DockerHub

### PODs
kubelet is an agent process of Kubernetes. It runs on worker nodes and is responsible for creating pods.

Pods is the smallest object in kubernetes that you can create and manage they are like containers.
The main job of POD is to keep the container running and if it is in exited state, POD will try to restart the container. In Kubernetes we always work with POD and not containers. POD is what is connected to containers, we don't connect to containers.

A POD can have 1 container or many containers depending on what we want but 1 POD 1 Container is most preferable.

We can see the status of POD on Master VM. Kubernetes code is written in YAML and Master is only for giving instructions.


- Connect to Master Node
- sudo su -
- kubectl get nodes
- Every command of kubernetes starts with kubectl (Kubernetes Command-line interface)
- If you master or worker nodes are not ready. You will have to reset kubernetes on the lab
- ON THE MASTER NODE: kubeadm reset --force
- ON THE both the WORKER NODES: kubadm reset


#### We create a POD by:
- kubectl run pod1 --image sonal04/myredisproject
- sonal04/myredisproject is image name
- kubectl get pods

### Deploy multiple pods of the image:
- kubectl create deployment myproject --image ramezchreide/myredisproject --replicas 4
- kubectl get pods

### Delete a pod
- kubectl delete pod <podname>
- kubectl get pods

### Delete all the pods for the deployment:
- kubectl delete  deployment --all

### POD Specification file:
- vim pod1.yml

apiVersion: v1 (this value is coming from kubernetes)

kind: Pod (given by kubernetes)

metadata:

  name: mypod
  
  labels:
  
    app: webserver
    
    author: sonal
    
spec: # specify what you want on the POD - > 1 container or multiple?

containers:

    name: c1
    
    image: ramezchreide/myredisproject

    name: c2

    image: tomcat

- wq!
- kubectl create -f pod1.yml
- kubectl get pods
