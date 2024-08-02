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

Note: docker tag myredisproject <usernameDockerHub>/<projectName>

### Deployment using Kubernetes
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
