A Pod is a single instance of an application.   
A POD is the smallest object that you can create in Kubernetes.   
A Pod can contain one or more containers.   
The containers in a Pod share the same network namespace, which means they can communicate with each other using localhost.   
Pods are ephemeral, meaning they can be created and destroyed as needed.

ReplicaSet - A replicaSet will maintain a stable set of replica Pods running at any given time. It is used to guarantee the availability of a specified number of identical Pods. 

Deployment - A deployment runs multiple replicas of your application and automatically replaces any instances that fail or become unresponsive. It provides declarative updates to Pods and ReplicaSets.  
It is well-suited for stateless applications.

Service - A service is an abstraction for Pods providing a stable virtual IP VIP address. The service sits in front of a POD and acts as a load balancer.

Kubernetes - Imperative and Declarative.

Imperative - Meaning deploying using kubectl.

Declarative - Meaning deploying using yaml file and kubectl apply.

In Kubernetes the target is to deploy the application in the form of container on worker nodes in the cluster.  
The container image is needed. The container is encapsulated in Pods.  
A pod is a single instance of an application - Meaning in case the target to get 10 instance of the application then we need to create 10 pods.
Pod have one to one relationship with container. To scale up we create new pod and to scale down we delete the pod.  
There will be no 2 container in single pod with same purpose like there will be no 2 application instance inside pod of same purpose in single pod.  
There can be multiple container in single pod - they are not of same type.

In pod there will be the application instance and a helper container - Sidecar - They act as data pullers - Pull data needed by main container and data pusher - push data like logs to external system - Proxies - Writes static data to html files using helper container and read using main container.
The containers inside pods communication within the same network space and they share the same storage space.


Imperative Pod deployment.

Connect the kubectl to the GKE cluster. In the kubernetes there should be a cluster created. There connect get the code.
```shell
gcloud container clusters get-credentials <CLUSTER_NAME> --region <REGION> --project <PROJECT_ID>

gcloud container clusters get-credentials standard-public-cluster-1 --region us-central1 --project kdaida123

kubectl get nodes 

kubectl get pods 

kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0

kubectl get pods
#NAME            READY   STATUS              RESTARTS   AGE
#my-first-pod    1/1     ContainerCreating   0          98s

kubectl get po -o wide

#NAME           READY   STATUS    RESTARTS   AGE   IP          NODE                NOMINATED NODE   READINESS GATES
#my-first-pod   1/1     Running   0          69s   10.124.1.5  gke-standard-pub    <none>           <none>

```

`kubectl run my-first-pod --image stacksimplify/kubenginx:1.0.0` meaning create the pop name `my-first-pod` and pull the docker image and create the container in the pof and start the container.


To access the application we need see the worker node and to see 





