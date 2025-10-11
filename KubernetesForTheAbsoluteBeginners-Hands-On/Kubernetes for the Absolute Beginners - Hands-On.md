# 001. Overview
Kubernetes = containers  + orchestration
It is a container orchestration technology used to orchestrate the deployment and management of hundreds and thousands of containers in a clustered environment.

## Structure
A master controller and multiple nodes/workers, the containers runs inside the nodes
![[Pasted image 20241025002021.png]]

## The master node
The Master is another Node with Kubernetes installed in it and is configured as a Master.
The Master watches over the Nodes in the Cluster and is responsible for the actual orchestration of containers on the Worker Nodes. When you install Kubernetes on a system, you're actually installing the following components, an API server, etcd service, a kubelet service, a container runtime, controllers and schedulers:

1.  The **API server** acts as the front-end for Kubernetes. The users, management devices, command line interfaces all talk to the API server to interact with a Kubernetes Cluster.
2. The **etcd key store**  is a distributed reliable key value store used by Kubernetes to store all data used to manage the Cluster. Think of it this way, when you have multiple Nodes and multiple masters in your Cluster, etcd stores all that information on all the Nodes in the Cluster in a distributed manner. 
	- etcd is responsible for implementing logs within the Cluster to ensure that there are no conflicts between the Masters.
3. The **scheduler** is responsible for distributing work or containers across multiple Nodes. It looks for newly created containers and assign them to nodes. 
4. The **controllers** are the brain behind orchestration. They're responsible for noticing and responding when Nodes, containers, or endpoints goes down. The controllers make decisions to bring up new containers, in such cases. 
5. The **container runtime** is the underlying software that is used to run containers. In our case, it happens to be Docker, but there are other options as well
6. The **kubelet** is the agent that *runs on each Node* in the Cluster. The agent is responsible for making sure that the containers are running on the Nodes as expected.

![[Pasted image 20241025002228.png]]

![[Pasted image 20241025003113.png]]


## kubectl

The kubecontrol tool is used to deploy and manage applications on a Kubernetes Cluster, to get Cluster information, to get the status of other Nodes in the Cluster and to manage many other things.

The *kubectl run* command is used to deploy an application on the Cluster.
`kubectl run hello-minikube`

The *kubectl cluster-info* command is used to view information about the Cluster
`kubectl cluster-info`

 The *kubectl get nodes* command is used to list all the Nodes part of the Cluster.
`kubectl get nodes`

# 002. Kubernetes concepts

## PODs
Is the minimal structure in kubernetes
A pod contains only a container of the same type, in a relation one to one, but a Node can contain multiple pods.

![[Pasted image 20241025005930.png]]

For example a POD can contain different containers in the same POD, sharing info as localhost
![[Pasted image 20241025010304.png]]

When you use `kubectl run nginx --image nginx`, it create a pod to run nginx, and then download the image from docker. Then we can use `kubectl get pods` to obtain data about our new container

![[Pasted image 20241025010813.png]]

## quick example
go to kubernetes.io
find install instructions for kubectl
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

```bash
# Download the latest release with the command:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
# Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
# Test to ensure the version you installed is up-to-date:
kubectl version --client
kubectl version --client --output=yaml
```

then go to install minikube
https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download
minikube is local Kubernetes cluster, focusing on making it easy to learn and develop for Kubernetes.
All you need is Docker (or similarly compatible) container or a Virtual Machine environment to simulate the worker, and Kubernetes is a single command away: minikube start.

```bash
# To install the latest minikube stable release on x86-64 Linux using Debian package
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb

```

you also need to install an hypervisor, you can use vmware, docker, etc.

```shell

minikube start --driver docker

# output
😄  minikube v1.34.0 on Ubuntu 22.04 (vbox/amd64)
✨  Using the docker driver based on user configuration
📌  Using Docker driver with root privileges
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.45 ...
💾  Downloading Kubernetes v1.31.0 preload ...
    > preloaded-images-k8s-v18-v1...:  326.69 MiB / 326.69 MiB  100.00% 20.96 M
    > gcr.io/k8s-minikube/kicbase...:  487.89 MiB / 487.90 MiB  100.00% 17.75 M
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.31.0 on Docker 27.2.0 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

# add minikube to path
alias kubectl="minikube kubectl --"

# get info of our pod
kubectl get po -A
# output
NAMESPACE              NAME                                        READY   STATUS    RESTARTS      AGE
kube-system            coredns-6f6b679f8f-x48l5                    1/1     Running   0             11m
kube-system            etcd-minikube                               1/1     Running   0             11m
kube-system            kube-apiserver-minikube                     1/1     Running   0             11m
kube-system            kube-controller-manager-minikube            1/1     Running   0             11m
kube-system            kube-proxy-j5prg                            1/1     Running   0             11m
kube-system            kube-scheduler-minikube                     1/1     Running   0             11m
kube-system            storage-provisioner                         1/1     Running   1 (10m ago)   11m
kubernetes-dashboard   dashboard-metrics-scraper-c5db448b4-2f9mp   1/1     Running   0             2m4s
kubernetes-dashboard   kubernetes-dashboard-695b96c756-w6cbr       1/1     Running   0             2m4s

#comandos para ver el estado
minikube status
# output
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

kubectl get nodes
#outout
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   50m   v1.31.0




```