# 1. Architecture
![[Pasted image 20251215010111.png]]

## Cluster:
Set of resources that work together as a single resource.

### Control plane:
Manages what happens in the cluster.

- kube-api-server: exposes a REST API so that components can communicate
- etcd: database for distributed systems, stores information as key-value    
- scheduler: defines where the container will be executed    
- controller manager: maintains the desired state of the cluster    

### Node:
Manages the workload and runs the containers.

- kube-proxy: manages networking-related issues within a node    
- kubelet: agent that informs the Control plane about the node's state, so the controller manager can make decisions    
- CRI (Container Runtime Interface): Engine (docker, podman, etc)    
    - In Kubernetes, the work is done on pods, which can contain one or more containers

# 2. Install
1. you need docker installed

2.  Installing kind from binaries (optional: minikube)
```bash
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-amd64

chmod +x ./kind

sudo mv ./kind /usr/local/bin/kind

kind --version
```

- Create a cluster.yml config file
```yaml
# cluster.yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
	- role: control-plane
	  image: kindest/node:v1.30.10
	- role: worker
	  image: kindest/node:v1.30.10  
	- role: worker
	  image: kindest/node:v1.30.10  	  
```

- create the cluster
```shell
kind create cluster --config cluster.yml

#output
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.30.10) 🖼 
 ✓ Preparing nodes 📦 📦 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-kind"
```

3. installing kubectl 
```shell
#downloading with curl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

kubectl version --client

kubectl get nodes

# output
# we can see the control-panel and 2 workers defined in the yml file
NAME                 STATUS   ROLES           AGE     VERSION
kind-control-plane   Ready    control-plane   2m45s   v1.30.10
kind-worker          Ready    <none>          2m25s   v1.30.10
kind-worker2         Ready    <none>          2m25s   v1.30.10
```

## Kubernetes objects:

### Pod:
It can contain one or more containers, but usually dedicated to the same application.
![[Pasted image 20251215020327.png]]

All of them shares the same network/namespace (ips, ports).
Pods themselves are inherently ephemeral, designed to be disposable and replaceable by controllers like Deployments, managed by the K8s control plane, and deleted when their node fails or they finish, ensuring high availability and resilience.

### Deployment:
Administra el despliegue y la actualización de Pods de forma declarativa.

### ReplicaSet:
Asegura un número específico de réplicas de un Pod.

### StatefulSet: 
Maneja Pods que requieren identidad y almacenamiento persistente.

### DaemonSet: 
Ejecuta un Pod en todos o algunos nodos del clúster.

### Job: 
Ejecuta una tarea una sola vez hasta su finalización.

### CronJob: 
Programa la ejecución de _Jobs_ en intervalos.

# 3. Lifecicle
![[Pasted image 20251215021158.png]]

- The client (user using the **CLI** or a **GUI**) uses the **API server** to send a request to create a pod.    
- A **Pod** specification is written **to** the **etcd** **database (DB)**.    
    - As you can see, every response is **sent back** to the **API server**, and        
    - creates an entry in the **etcd DB**.        
- The **API server** tells the scheduler to create a new **Pod**. The scheduler **assigns** the **Pod** to an available node.    
- The **kubelet** **receives** an instruction to manage the new **Pod**.    
    - The **kubelet** uses **Docker** (or the **Container Runtime Interface/CRI**) to launch the new container.        
- Once the **API server** receives the confirmation **regarding** the pod's status, and writes **it** in **etcd**, **it** sends a new message to the **kubelet** with the health status.

# 4. Declarative and imperative declaration

- Imperative: You define the steps to achieve a status, "what to do".
	- Example: Commands using the CLI 
- Declarative: You define the desired status.
	- Example: Using a json or yaml file

```yaml
#pod.yml

apiVersion: v1 #defines the directives we will use on this configuration
kind: Pod #the object what we are configuring
metadata: 
  name: nginx-test
spec:
  containers:
    - name: nginx-container
	  image: nginx
		ports:
          - containerPort: 80
```
To run:
```shell
#-f to define the filepath
kubectl apply -f ./pod.yml
```

