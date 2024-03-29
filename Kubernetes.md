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
  * kube-scheduler (assigns workloads to nodes)
  * Controller (monitor objects and react)
  * and others...
* **Worker Node**. Runs pods. Sub-components:
  * Container runtime. Can be different: containerd, docker engine, CRI-O...
  * kubelet. An agent watching for Pods.
  * kube-proxy. Maintains network rules on nodes.

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
minikube kubectl -- {kubectl command}
```

#### Kubectl commands
* `kubectl config view` to view cluster config at ` ~/.kube/config`
* `kubectl cluster-info`
* `kubectl proxy --port=8080` to expose the cluster's API on the `localhost` without the need to authenticate. Otherwise, need to generate an API token and call the API server's IP.
* `kubectl run hello-minikube --image ...` deploys an app onto cluster
* `kubectl get pods` list all pods
* `kubectl get all <opitonal:--all-namespaces>` list all resources
* `kubectl create -f definition.yaml` creates an object from a file
* `kubectl describe pod {name}` list pod info
* `kubectl exec -it {name} -- /bin/sh` exec into a pod interactively

### Terms
#### Namespaces 
Namespaces partition the cluster into virtual sub-clusters.\
The names of the objects inside a Namespace are unique, but not across Namespaces in the cluster.\
Namespaces provide a solution to multi-tenancy.\
**Resource quotas** help users limit the overall resources consumed within Namespaces.
```
kubectl get ns
```
How to switch namespaces?
```
kubectl config set-context --current --namespace=dev 
```
How to search in all namespaces?
```
kubectl get ... --all-namespaces 
```


#### Pods
Pods is the smallest Kubernetes workload object.\
It is the unit of deployment in Kubernetes, which represents a single instance of the application.\
A Pod is a logical collection of one or more **different** containers having the same IP.
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
    app: myapp
    type: front-end
spec:
  containers:
  - name: nginx
    image: nginx:1.22.1
    ports:
    - containerPort: 80
    command: [ "sleep" ]
    args: [ "5000" ]
    env:
      - name: APP_COLOR
        value: ... <option 1>
        valueFrom: ... <option 2>
```
Getting pod definition from a running pod:
```
kubectl get pod <pod-name> -o yaml > pod-definition.yaml
```

#### Service 
Service is an abstraction that makes a set of Pods available on the network so that clients can interact with it.\
Types of Service:
* ClusterIP - exposed internally within a cluster
  * `--cluster-ip=none` makes a headless Service which has no internal IP
* NodePort
* LoadBalancer
* ExternalName

How Pods find a service?\
With DNS server (add-on) enabled in a cluster each service gets a DNS name like `my-servce.my-namespace`.

#### Config Maps
A key value map. 
Pods can get env vars injected from config maps.
```
kubectl create -f {file}
```
Config map file:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: "blue"
```
Then in Pod pick all configMap
```
spec:
  containers:
  - envFrom:
    - configMapRef:
        name: app-config
```
or just some vars from it
```
spec:
  containers:
  - env:
    - name: APP_COLOR
      valueFrom: 
        configMapKeyRef: 
          name: webapp-config-map
          key: APP_COLOR
```
#### Secrets
A key value map. 
Pods can get env vars injected from secrets.\
Note 1: secrets are not encrypted by default in etcd. To add encryption at rest it is necessary to create a "encryption configuration" object in the cluster.\
Note 2: anyone who has access to create/run pods in a Namespace can view secrets.\
Note 3: it is possible to plug in 3rd party secrets provider instead of storing in etcd (e.g. AWS, GCP, Vault...)
```
kubectl create secret generic ...
```
Secret file:
```
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_Password: {base64 encoded text}
```
Then in Pod pick all secrets
```
spec:
  containers:
  - envFrom:
    - secretRef:
        name: app-secret
```
or just some vars from it
```
spec:
  containers:
  - env:
    - name: APP_COLOR
      valueFrom: 
        secretKeyRef: 
          name: app-secret
          key: DB_Password
```

<br> 

#### Resources
Pod definition can specify resources per each container with `requests` and `limits`.
Kubernetes has also objects `kind: LimitRanges` which a pod can refer to.
![pod_resources.png](Kubernetes_files%2Fpod_resources.png)

Kubernetes has objects `kind: ResourceQuota` at namespace level which limits total resources for all pods.

<br>

#### Taints and Tolerations
Can be used to restrict placing Pods on certain Nodes (e.g. with specific resources).\
Normally Kube Scheduler assigns pods to nodes in randomly balanced manner.\
Taints are set on Nodes (as a repellent) and Tolerations are set on Pods.\
Taints and Tolerations can not make a Pod go to a specific Node! For that use "Node Affinity" or "Node Selectors".\
Note: Master Node (Control Plane) is tainted by default to prevent regular workloads placement.

```
kubectl taint nodes ...
```

<br>

#### Node Selectors
Nodes can be labelled.\
Pods can pick Nodes with Node Selectors which refer to labels:
```
kubectl label nodes node1 <key>=<value>
...
spec:
  nodeSelectors:
    <key>: <value>
```
Node selectors do not support complex bool expressions like "small OR medium", "NOT large".\
For that use "Node Affinity".

<br>

#### Node Affinity
Affinity are based on Node labels and uses complex expressions.

<br>

#### Liveness and Readiness probes
Pod condition "READY" (= container has started) does not always mean traffic can be routed to it.\
For that add a Readiness probe in a Pod definition, e.g.
```
spec:
  readinessProbe:
    httpGet:
      path: /health
      port: 8080
    initialDelaySeconds: 5
    periodSeconds: 10
    failureThreshold: 1
```
Types of probes:
* HTTP
* TCP
* exec a command/script

Liveness probes is needed when a container is running but the app inside is down.
<br>

#### Logging
```
kubectl logs <pod> <optional:container>
```
<br>

#### Monitoring
Kube has limited monitoring but can plug in specialized tools like:
* Prometheus (free)
* Metrics Server (in-memory, free)
* OpenStack (free)
* Datadog (proprietary)

If metrics server is enabled (e.g. by default in Minikube) user can run
```
kubectl top node
kubectl top pod
```
If not first install it 
<br>

#### Labels and Selectors
Labels and Selectors is a standard way of grouping and filtering objects in K8s.\
Labels are key-value pairs.\
Then Selectors come into play:
```
kubectl get pods --selector app=app1
```
<br>

#### Ingress
An API object that manages external access to the services in a cluster, typically HTTP.\
Ingress may provide load balancing, SSL termination and name-based virtual hosting.\
Ingress Controller must be created for the Ingress object to work.\
K8s does not come with Ingress Controller by default.\
User needs to deploy something like a special NGINX-based Ingress Controller.

Ingress defines rules to route traffic to different services based on:
- hostnames (e.g. different subdomains)
- URL paths

To create an NGINX Ingress Controller:
1. Create a Config Map
2. Create 2 Service Accounts
3. Create a service from the ingress-nginx image - this is an actual Ingress Controller

<br>

#### Network policies
By default, K8s is configured with "All allow" rule that allows traffic between all Pods or Services within the Cluster. 
What if we need to hide a Database Pod?\
Create a Network Policy object and link it to the Pod via Selectors.\
Define rules in the Network Policy object.\
Rules can be of 2 types:
* Ingress - limits incoming traffic to a Pod 
* Egress - limits outgoing traffic from a Pod

Rules can refer to Pods and/or Namespaces or IP ranges.
<br>

#### Volumes
Volumes can be attached to Pods to persist data.\
Simplest example using FS on the Node:
```
kind: Pod
spec:
  containers:
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data
      type: Directory
```
Volumes can be mount using different mechanisms like local FS or specialized cloud provider solutions like AWS EBS.\
See https://kubernetes.io/docs/concepts/storage/ \
Volume has a lifetime of a Pod and can be configured in Pod definition files.\
Persistent Volumes (PV) exist beyond the lifetime of a pod and are created centrally for the whole cluster:
```
kind: PersistedVolume
```

```
kind: PersistedVolumeClaim
```
Persisted Volume Claim is another object that `binds` Persisted Volumes to Pods based properties such as:
* Capacity 
* Access mode
* Volume mode
* Storage class

There can be multiple matching Volumes to a single Claim.\
In this case Selectors can help to uniquely set a Volume.

If no matching Volume is found a Claim remains in `pending` state.

When a PVC is deleted a PV based on `persistentVolumeReclaimPolicy` can be:
- Retain
- Delete
- Recycle (data erased)

Specifying PVC in Pod (Deployment or ReplicaSet) definition:
```
kind: Pod
metadata:
spec:
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

:exclamation: When a PVC is deleted and a PV is persistentVolumeReclaimPolicy = Retain, it stays in the cluster but is not Available immediately (Released state instead).\
:exclamation: PVC can not be deleted when it is used by a Pod.
<br>

### Workloads
#### Deployment
Deployment describes a desired state of pods.\
Deployment Controller checks on the health of pods and restarts containers if necessary.\
A deployment can be created like:
```
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
```
or from a file:
```
kubectl apply -f nginx-deployment.yaml
```
Deployment encapsulates Replica Sets and additionally support deployment strategies (e.g. rolling updates). 

Deployment strategies:
* Recreate (with downtime)
* Rolling update (by default)

Deployment triggers a rollout under certain conditions, e.g. manually:
```
kubectl set image <deployment> <container_name>=<image>
```
During a rollout a Deployment creates new Replica Set and keeps the old one.\ 
How to check rollout status/progress?
```
kubectl rollout status <deployment> 
kubectl rollout history <deployment> <optional:--revision=1>
```
How to rollback manually?
```
kubectl rollout undo <deployment>
```

#### Replica Set
Replicas set is a controller that specified number of Pods are running.\
RS creates pods from a template definition.\
RS uses `spec.selector.matchLabels` in the definition to identify its pods (for a case when pods are not created automatically from a template).\
NB: RS is a lower abstraction than Deployment and in most cases a Deployment should be used.\
Pods are linked to a Replica Set via `metadata.ownerReferences`.

Scaling existing RS:
```
kubectl scale --replicas=6 replicaset {name}
```
Editing existing RS:
```
kubectl edit replicaset {name}
```

#### Jobs 
Jobs are designed to run a task and finish.\
A normal pod with a container finishing with 0 would restart again and again.\
For that a `restartPolicy` config property is responsible which is `Always` by default but can also be `Never` or `OnFailure`.\
Instead, use a job object like:
```
apiVersion: batch/v1
kind: Job 
...
spec:
  completions: 3 // To run a task until 3 succesfful completions
  parallelism: 3 // By default jobs are sequential, but this can be changed wit this prop 
```
A job is considered successful when the container finishes with `0` exit code.\
`.spec.backoffLimit` config property is responsible for a number of retries and =6 by default.

#### CronJobs
Jobs are designed to run a task on a scheduled basis and finish.\
```
apiVersion: batch/v1
kind: CronJob 
...
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec: // A spec of a job object. 
```

#### Service
Services expose groups of pods for external communication.\
A Service in K8s encapsulates the service discovery mechanism.\
Service types:
* NodePort - maps ip:port of a pod to ip:port of a node.\
A specific Pod inside the Node is linked to a Service via Selectors.\
I case of several Pods matching the Services acts as a LoadBalancer with random distribution.
![NodePort.png](Kubernetes_files%2FNodePort.png)
In case of several Pods spread across several Nodes it will be different Nodes IPs but the same port: 
![NodePort2.png](Kubernetes_files%2FNodePort2.png)
* ClusterIP - virtual IP inside a cluster for the whole service (default).\
All Pods are accessed by a **unique** `ClusterIP` or a `Service name`.\
Can get `ClusterIP` after Service creation by `kubectl get services`.
* LoadBalancer - supported by external cloud providers

<br><br>
#### Docker runtime vs containerd
Initially Kubernetes worked with Docker directly.\
Then an CRI interface was developed to support other runtimes.\
CRI was not supported by Docker and Kubernetes used Docker via temporary "dockershim".\
At the same time `containerd` was developed as a CRI compatible sub-component of Docker.\
Since v1.24 Kubernetes removed "dockershim".\
It is possible to install `containerd` only (without docker) and run containers (but not convenient).\
Now docker runtime works via `cri-dockerd` container runtime endpoint.
