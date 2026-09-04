I have 5 yoe in designing, building, operating scalable and highly available cloud platform. The main expertise lies in implementing the key pillars of devops like automation, cicd, iaas, monitoring and security.

I have worked in designing cloud native platform, kubernetes based environment and end-to-end CICD ecosystem for enterprise application.

In the space of governance I have implemented secured multi-cloud architecture, centralized access management solution aligned with compliances like GDPR and HIPPA.

### The application will be hosted that expect to server 5 million concurrent users. How to design and what are the services and tools to use.
To design the cloud architecture I have to understand the application - stateful or stateless.    
To understand the expected traffic pattern, the db requirement, compliance and latency expectation.

In high level architecture - there will be Route 53 for the DNS and CloudFront for CDN and caching. We have to use web and shield for security. To distribute traffic we can create load balancer. You are going with the containerization then GKE.

ECS when architecture is not complex.

### Application is running. Yu notices sudden traffic spike in 10x. How to determine its good or ddos traffic.
In the debugging step first see the access log, source Ip and the request pattern, geo location and the request path. 


The traffic coming from diverse real users with normal behaviour and valid application request its a good traffic. Scale up the application using auto scaling.

Large repetitive request from suspicious IP or unusual user agent and targetting to single endpoint then its a attack.

To mitigate will use AWS shield and rate limiting.

### The Kubernetes has 3 namespace in the same cluster. How would you prevent the dev namespace from the prod.
In general we have the different cluster for different environment. In case we have all the env in single cluster then there we will Kubernetes network policy to restrict the cross - namespace communication. The first step to deny all the ingress and egress connection between the traffic and make the required communication between the env.

## Created any deployment, services, ingress, egress from the yaml file.

We are creating it starting from the scratch and we are working in the helm chart format.

### What happens internally when we do the kubectl apply -f?

Kubernetes read the yaml manifest and read the request and send to the kubeAPI and it will do the authorization and authentication and then send the data to the etcd. The scheduler assign the port to the worker node. The kubelet on teh specific node get the instruction and ask the container runtime to create the container. The port starts running and the controller ensure that the desired state is maintainer.

### What is teh etcd on the masterplane.

It is the database where it store the data of everything. 
### Why Kubernetes pick etcd instead of any other db?

### The application is hosted in GKE. How does the request reaches your pod when the user use the application.

When an user request in the website it reaches the DNC it can be route 53 or GoDaddy and it get the domain from there and reaches the application load balancer and forward the request to the kubernetes ingress controller. The ingress route the request to the service and the service forward it to the healthy pod using kube proxy.

### Example of some achievement like migrated 500Tb of data in S3 bucket from one AWS account to another- Its an AWS data sync.

The achievement part is how we verify that all data has been migrated without missing any object. To solve that issue the system used AWS inventory that generates the csv file format containing all the object in a bucket. Then loaded the inventory into the dynamodb and compare the source and the destination inventory to identify any missing data.

### Give an example of the CICD pipeline that you have build.

The approach is similar for all the tools and the technologies. The developer will push the code and the pipeline will be triggered. It will pull the latest code from the git and run the unit test cases and perform the code quality using the sonarqube. When creating the image will use some vulnerability scanning tool like Trivy and then it pushes the image to the ECR. The CD part we have different repositories and there we update the image tag in the github repository. 

The ArgoCD detect the change and synchronize with the Kubernetes cluster. The application deployed using the rolling update strategies and post deployment the health checks were performed before marking the deployment as successful.

### Why you used the rolling update and not canary deployment it the new trend.

### Developer commits the AWS access key. In making the CICD pipeline any config that will help. What immediate action you should take and how would you prevent from happening it further.

First step to rotate the key and it cannot be used anymore. In the cloud will see the cloud trail and see any suspicious activity happened using the key.

In the pipeline we can integrate Git secret or Git leak into the pipeline to detect any secret getting pushed or not before the code is getting merged.

The solution is to use Google secret manager.

### The application is deployed and the application showing green deployment and user mentioned that they are facing 502 gateway error.

Logs of pod and pods are running or not. The readiness and liveliness probe. The services are pointing to the healthy pods or not. In case that is correct see the ingress controller and the load balancer to see where the error is generated







































