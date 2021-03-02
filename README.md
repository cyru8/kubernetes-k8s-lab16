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
ubectl create deployment namespacedeg -n testns --image=katacoda/docker-http-server
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

