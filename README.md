# kubernetes-k8s-lab16

Kubectl command sheet

#####
Enable kubernetes Auto-completion
######
$ source <(kubectl completion bash)

kubectl get nodes

kubectl get namespaces. 

kubectl get pods -n kube-system

kubectl get ns

kubectl create deployment examplehttpapp --image=katacoda/docker-http-server

kubectl get deployments

kubectl get pods

kubectl get pods -o wide

kubectl describe pod <pod-name>

kubectl get namespaces

kubectl get pods -n kube-system

kubectl get ns

kubectl create ns testns

kubectl create deployment namespacedeg -n testns --image=katacoda/docker-http-server

kubectl get pods -n testns

kubectl scale deployment examplehttpapp --replicas=5 deployment.extensions/examplehttpapp scaled

kubectl get deployments

kubectl get pods

kubectl edit deployment examplehttpapp.

kubectl --record=true set image deployment examplehttpapp docker-http-server=katacoda/docker-http-server:v2

kubectl rollout status deployment examplehttpapp

kubectl rollout undo deployment examplehttpapp


# Exposing and making applications available, using Services:

We've learned how to successfully deploy and manage applications, but we need to make them accessible. This is managed via Services.
The expose command will create a new service for a deployment. The port specifies the port of the application we want to available 

kubectl expose deployment examplehttpapp --port 80

kubectl get svc 

kubectl describe svc examplehttpapp


But how does Kubernetes know where to send traffic? That is managed by Labels. Each Object within Kubernetes can have a label attached, allowing Kubernetes to discover and use the configuration. With the previous example, the service knows which Pods to send traffic to based on the label called app. We can use this label to select all the pods for that deployment, within that namespace.

kubectl get services -l app=examplehttpapp -o go-template='{{(index .items 0).spec.clusterIP}}'

curl $(kubectl get services -l app=examplehttpapp -o go-template='{{(index .items 0).spec.clusterIP}}')
###

What is a ClusterIP? A ClusterIP is a Virtual IP that can receive traffic which is then forwarded onto a valid Pod. This has advantages over DNS by removing caching and re-resolving load.
The Go template is used to query and return properties of an object. Output the object as JSON to view the response schema and identify how to query the data

kubectl get services -l app=examplehttpapp -o json

###
The Kubernetes API defines different objects to solve day-to-day problems which can be extended using Custom Resource Definitions.
For example, Crontab will launch a Pod at particular times. A DaemonSet will ensure that a deployment is running on every node in the cluster, great for collecting and aggregating metrics.
###

A pod is a group of one or more Containers, with shared storage/network, and a specification for how to run the containers.
More details at https://kubernetes.io/docs/concepts/workloads/pods/pod/
###

_Pro tip:_Working with a namespace for a period of time? Change the context to make a different namespace the default.

kubectl config set-context $(kubectl config current-context) --namespace=testns; 

kubectl config get-contexts

kubectl get pods
###

Kubectl can help scale the number of Pods running for a deployment, referred to as replicas.

kubectl scale deployment examplehttpapp --replicas=5

View the state of the scale with kubectl get deployments as you scale up/down, the number of Pods will update to reflect the desired configuration 

kubectl get pods
###

Now the application is receiving data, if something goes wrong we need to be able to investigate. Using kubectl it's possible to view the logs for Pods.
The command combines label selectors and querying the Go Template to get the first Pod name.

kubectl logs $(kubectl get pods -l app=examplehttpapp -o go-template='{{(index .items 0).metadata.name}}')
###

To continue debugging, it's something required to view the CPU or Memory usage of a node or Pod.
View the Node CPU/Memory status:

kubectl top node

View the Pod CPU/Memory status:

kubectl top pod
###

Mark the Node as unschedulable to prevent new pods from being assigned to it:

kubectl cordon <node_ID>

kubectl drain <node_ID>

Remove the Kubernetes Node:

kubectl delete node cmp<node_ID>