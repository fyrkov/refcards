### Kubernetes concepts
https://kubernetes.io/docs/concepts/overview/

Kubernetes provides:
* Service discovery and load balancing
* Automated rollouts and rollbacks
* Secret and configuration management 
* Storage orchestration

#### Kubernetes cluster components:
* **Control Plane**. It makes global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (for example, starting up a new pod when a deployment's replicas field is unsatisfied). Sub-components:
  * kube-apiserver
  * etcd (backing store for all cluster data)
  * kube-scheduler
  * and others...
* **Worker Node**. Runs pods. Sub-components:
  * Container runtime. Can be different: containerd, docker engine, CRI-O...
  * kubelet. An agent watching for Pods.
  * kube-proxy. Maintains network rules on nodes

What is the difference between **pod** and **worker node**?\
Pods are simply the smallest unit of execution in Kubernetes, consisting of one or more containers.\
Nodes are the physical servers or VMs that comprise a Kubernetes Cluster.

#### Tools to deploy a cluster:
* Minikube. Supports isolation methods like virtualization or container runtimes 
* kubeadm
* others...

#### Minikube 
https://minikube.sigs.k8s.io/docs/start/

```
minikube start
minikube status
minikube profile list
minikube dashboard
minikube stop   
```

#### Kubectl commands
* `kubectl config view` to view cluster config at ` ~/.kube/config`
* `kubectl cluster-info`
* `kubectl proxy --port=8080` to expose the cluster's API on the `localhost` without the need to authenticate. Otherwise, need to generate an API token and call the API server's IP.

### Terms
**Namespaces** partition the cluster into virtual sub-clusters.\
The names of the objects inside a Namespace are unique, but not across Namespaces in the cluster.\ 
Namespaces provide a solution to multi-tenancy.\
**Resource quotas** help users limit the overall resources consumed within Namespaces.

```
kubectl get namespaces
```

**Pods** is the smallest Kubernetes workload object.\
It is the unit of deployment in Kubernetes, which represents a single instance of the application.\
A Pod is a logical collection of one or more containers having the same IP.
```
kubectl get pods -A
```
![Pods](Kubernetes_files/Single-_and_Multi-Container_Pods.png)

Example of a stand-alone Pod object's definition without an operator:
```
apiVersion: v1
kind: Pod
metadata:
name: nginx-pod
labels:
run: nginx-pod
spec:
containers:
- name: nginx
  image: nginx:1.22.1
  ports:
  - containerPort: 80
```