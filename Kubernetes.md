### Kubernetes concepts
https://kubernetes.io/docs/concepts/overview/

Kubernetes provides you with:
* Service discovery and load balancing
* Storage orchestration 
* Automated rollouts and rollbacks
* Secret and configuration management 

#### Kubernetes cluster components:
* Control Plane. It makes global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (for example, starting up a new pod when a deployment's replicas field is unsatisfied).
* Worker Node. A unit of workload. Runs pods.

#### Kubectl commands
* `kubectl config view` to view cluster config at ` ~/.kube/config`
* `kubectl cluster-info`
* `kubectl proxy --port=8080` to expose the cluster's API on the `localhost` without the need to authenticate. Otherwise, need to generate an API token and call the API server's IP.
