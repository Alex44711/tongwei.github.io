# Node Commands
* mark Node unschedulable
    `kubectl cordon $NODENAME`
* node status
    `kubectl describe node <insert-node-name-here>`
* pods status
  `kubectl get pods` shows the status of the pods
  * individual node
      `kubectl describe <pod>`
* `kubectl convert` for migrate manifests to a non-deprecated api version with newer Kubernetes release.
* Create deployment
  ```console
  kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
  kubectl get deployments
  kubectl get pods
  kubectl get events
  kubectl config view
  # Get kubeconfig
  kubectl config view
  ```
* Create a Service
To make the container accessible from outside the Kubernetes virtual network, you have to expose the Pod as a Kubernetes Service.
  ```console
  kubectl expose deployment hello-node --type=LoadBalancer --port=8080
  kubectl get services
  ```
* Clean up
  ```console
  kubectl delete service hello-node
  kubectl delete deployment hello-node
  ```
* Shared resources in a Pod
  * Shared storage, as Volumes
  * Networking, as a unique cluster IP address
  * Information about how to run each container, such as the container image version or specific ports to use
* Pods are the atomic unit on the Kubernetes platform.
* Troubleshooting
  ```console
  kubectl get - list resources
  kubectl describe - show detailed information about a resource
  kubectl logs - print the logs from a container in a pod
  kubectl exec - execute a command on a container in a pod
  ```
* Each Pod in a Kubernetes cluster has a unique IP address, even Pods on the same Node, so there needs to be a way of automatically reconciling changes among Pods so that your applications continue to function.
* Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service.
* Services can be exposed in different ways by specifying a **type** in the ServiceSpec:
  * *ClusterIP* (default) - Exposes the Service on an internal IP in the cluster. This type makes the Service only reachable from within the cluster.
  * *NodePort* - Exposes the Service on the same port of each selected Node in the cluster using NAT. Makes a Service accessible from outside the cluster using <NodeIP>:<NodePort>. Superset of ClusterIP.
  * *LoadBalancer* - Creates an external load balancer in the current cloud (if supported) and assigns a fixed, external IP to the Service. Superset of NodePort.
    
## Services and Labels
* Example command:  
  `kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}'`

## Scaling apps
* To see the ReplicaSet created by the Deployment  
  `kubectl get rs`
* Scale the deployment to 4 replicas  
  `kubectl scale deployments/kubernetes-bootcamp --replicas=4`
* Number of pods with IP  
  `kubectl get pods -o wide`

## Performing a Rolling Update