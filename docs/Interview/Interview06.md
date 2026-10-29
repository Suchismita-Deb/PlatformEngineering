### The kubernetes node is close to 90% CPU, the set up is using multi node cluster and stop the scheduling of the pods in that node. What are the steps to stop scheduling the pods in the node?

Kubernetes architecture is like it will schedule the pods and in case it finds the empty CPU then it will schedule based on the requests and memory.

It is mainly how will you cordon a node?
There are 2 type interactive and declarative.

### What is the helm chart and what problem it is solving?

In Kubernetes the issue is there are a lot of yaml files like the deployments, secrets, service, personal volume, personal volumes claims and a lot of yaml file.
In helm chart we will update the values and it will update the values and no need of the yaml files.
### In the namespace the implementation of Kubernetes based security set up what are the steps?

Secure the pods like the security context.

### What are the difference between the Canary and Blue Green deployment?


### What are the steps when the user hit the url and the request reaches to the pod?

### What is ingress, ingress controller and load balancer?

### Which is used to redirect the traffic to the backend pods?

DNS - load balancer - ingress - ingress will redirect to the pod. 
There are multiple pods and each pods will have multiple container and each container will have one main container. The main container hosts the traffic.


### You have used the command terraform apply and it throws a State Lock Error?

The StateLockError will give some Id and first the thing to know any internal work is going on with the id. The describe type of command to see any state is currently working in case no state working then use the state unlock command.











