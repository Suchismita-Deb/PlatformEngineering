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


### What is the difference between git pull and git fetch?

git pull - It will fetch the changes and merge the changes to the current branch. The git pull command is a combination of git fetch and git merge. It retrieves the latest changes from the remote repository and automatically merges them into your current branch.
git fetch - It will fetch the changes and it will not merge the changes to the current branch

### what is the cgroup in linux? What OS or what flavor of linux you use?

### What is bash rc in ubuntu?

Bashrc is a script that is executed whenever a new terminal session is started in interactive mode. It is used to configure the shell environment, set environment variables, define aliases, and customize the behavior of the shell. The bashrc file is typically located in the user's home directory as ~/.bashrc.

When there is a single terminal and when I open a new terminal then the bashrc will be executed and it will set the environment variables and the aliases. The bashrc is used to customize the shell environment for the user.

### What are he steps to create highly available infrastructure using the Terraform ?







