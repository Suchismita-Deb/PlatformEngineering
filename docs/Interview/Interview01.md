### Explain your project experience.

The Terraform, GCP, Kubernetes are not separate. It is a complete system.
GCP has the resource like the VPC, subnet, Firewall, GKE, Load Balancer, Cloud SQL, Kafka, Storage.

Terraform manages it and the terraform apply command creates the network, servers, cluster, service account, IAM roles.

Application also has the linux and when we go SSH into the server like the lsec or ldev and then will see kafka connect, batch jobs, shell scripts, cron jobs, jenkins.
The command used are like ps -ef, top, df -h, free -m

The Kubernetes cluster and the GKE cluster contains nodes. Each node is the Linux VM and we dont login to it most of the times.
Inside the nodes there is pod and the pods are running the payment-service, customer-service and they are container.

The logs flow the path of cloud logging, dynatrace, splunk.

There is kubernetes, dynatrace then why the linux knowledge - In case there are things that break below the layer of the application like when application issue (java, pod,,node, linux) will go to the Kubernetes and when any issue like the disk full, node level then kubernetes logs will not help then the linux knowledge helps like `df -h`, `du -sh *`

There is Kafka connector right it will not be useful in the kubernetes and the login to the linux vm and then see the details.

Architecture.

Application runs in the GCP.  
Infrastructure is provisioned using the Terraform.  
Application are deployed in GKE.  
Each application runs in Kubernetes deployments and pods.  
Traffic comes through Apigee.   
Application pusblished and consumer Kafka events.  
Monitoring and alerting are handled through Dynatrace.  
Centralized logs are available through Kubernetes logging and Dynatrace.

There are some Kafka connect and legacy integration still runs on linux vms so we use the linux troubleshooting command when required.

My responsibilities includes - kubernetes deployments, troubleshooting pod/node issue, kafka integration support, monitoring through dynatrace and production incident resolution.

Terraform topic to know - State, module, wrkspaces, IAM , GKE creation.

Kubernetes topic to know - Pod, Deployments, Replicaset, Servcie, Ingress, ConfigMaps, Secret, HPA, Node, Namespace.

Control Plane Worker Node API Server Scheduler etcd Deployment StatefulSet Service Ingress HPA ConfigMap Secret Helm ArgoCD Self-healing Rolling Update

Linux knowledge - top, ps, grep, awk, sed, curl, netstat, ss, lsof, df, du, free, tail, systemctl

The networking part Request - Lb - Ingress - Service - Pod - Application.

### Writing clean code to parse logs, handle files, and implement timestamp-based processing logic.

### AWS Integration: Scenario tasks involving S3 bucket event notifications routed to an SQS queue, paired with object size or metadata validation.

### How do you design and troubleshoot a failing CI/CD pipeline?

### Explain your experience with Infrastructure as Code (IaC) tools like Terraform or cloud templates.

### How do you manage secrets securely in deployment pipelines (e.g., using HashiCorp Vault or native cloud secret managers)?

### How do you implement zero-downtime deployment strategies (e.g., Rolling Updates or Blue-Green deployments)?

### What is the difference between monitoring and observability in distributed cloud environments?

Monitoring and Observability are related, but they are not the same thing. I usually explain it this way - Monitoring tells me that something is wrong, whereas observability helps me understand why it is wrong.

In monolithic application, monitoring is enough as there are some moving part. In modern cloud environments we have microservices, Kubernetes, service meshes, APIs, message queues, databases, and third-party integrations. A single user request may travel through 10-20 services before completing. In such an environment, monitoring alone is not sufficient.

**Monitoring**

Monitoring is the process of collecting predefined metrics and generating alerts when thresholds are crossed.  

Examples:

CPU utilization > 80%
Memory utilization > 90%
Disk space < 20%
Pod restart count increasing
API latency > 1 second
Error rate > 5%

In Kubernetes, tools commonly used are:

Prometheus
Grafana
Datadog
CloudWatch
Azure Monitor
GCP Cloud Monitoring

For example, if a pod's CPU reaches 95%, Prometheus can trigger an alert and notify the team through Slack, PagerDuty, or email.

**Observability**

Observability goes beyond monitoring.

It is the ability to understand the internal state of a system by analyzing the telemetry data it generates.

Instead of just knowing that an issue exists, observability helps determine:

Why did it happen?
Which service is responsible?
Which request is failing?
What changed recently?
What is impacted?

Observability becomes critical in distributed systems where failures can occur across multiple services.

Monitoring may show:

CPU normal
Memory normal
Pods healthy

Everything looks green.

Observability tools may reveal:

Request starts at API Gateway
Passes through Order Service
Calls Inventory Service
Calls Payment Service
Payment service waits 3 seconds for database response

Now we know the root cause.

**Three Pillars of Observability**.

Metrics - Numeric values collected over time. Examples - CPU usage Memory usage Request count Error rate Latency. The question answered by the metric - How much ? How often?  
The tools used are - Prometheus, Grafana, CloudWatch, Datadog, New Relic.

**Logs** - Detailed event records generated by applications and infrastructure. Example - User authenticated successfully, Database connection timeout, Inventory service unavailable.

Question answered by logs - What happened? When did it happen? The tools used are - ELK Stack, Splunk, Cloud Logging, Datadog.

**Distributed Traces** - It is the place where the observability becomes powerful. Tracing follows the request across multiple services. The question answered - Where is the bottleneck? Which service is failing? How long did each service take to respond? 

Example - In application User request - API Gateway - Order Service - Inventory Service - Payment Service - Database. A trace shows which service handles the request, Time spend in each service, Exact point of failure.  

The tools used - Jaeger, Zipkins, Open telemetry.


**Log Vs Trace**.

In splunk when we search for the correlation is - `requestId=12345` and see the logs like

```
10:00:01 Order request received
10:00:02 Inventory check started
10:00:03 Inventory check completed
10:00:08 Payment request completed
``` 
Logs can be using for the troubleshooting and timing analysis.

The problem exists in scale. When there will be 200 microservice and thousands of requests per second and millions of log lines then finding all logs for one request becomes difficult. 

The tracing will build the request path.
```
Trace ID: abc123

API Gateway : 50ms
Order Service : 120ms
Inventory Service : 80ms
Payment Service : 5000ms  ← bottleneck
Database Query : 4900ms
```

We see the request path, parent-child service relationship, exact latency on each loop, bottleneck location. No manual log correlation needed.


Monitoring tells me that a request is slow. Tracing tells me which service is slow. Logs tell me why that service is slow. 

For example, a Jaeger or OpenTelemetry trace may show that Payment Service spent 5 seconds processing a request. I would then open Splunk logs for that service and trace ID to see whether the delay was caused by a database timeout, connection pool exhaustion, or an application exception.

Example in an application - 

Prometheus alerts that latency increased.
Grafana shows when it started.
Logs reveal exceptions.
Jaeger traces show which service caused the delay.

This significantly reduces MTTR (Mean Time To Resolution).


### How do you apply chaos engineering principles to test system resilience?

### Update the secret manager and make sure that the application not impacted. In case of password rotation how to make sure the application will not be down.

### Lifecycle of the terraform.
init then plan then apply.

### create before destroy the lifecycle.

### I have an instance EC2 and I want th epython to be installed automatically and not manually. How will you do it?

### What do you understand by NACL and the Security groups.


Security Group in AWS ≈ GCP VPC Firewall.  
The AWS Security Groups are stateful firewalls attached to resource. In GCP, the closest equivalent is VPC Firewall Rules, which control ingress and egress traffic to VM instances and other resources.   
NACL AWS Network Access Control List ≈ GCP VPC Firewall.

In AWS both SG and NACL  are security mechanism used to control network traffic.

Traffic passing through two security checkpoints.
```text
Internet
   ↓
NACL (Subnet Level)
   ↓
Subnet
   ↓
Security Group (Instance Level)
   ↓
EC2 / Pod / Application
```
SG acts like the virtual firewall attached directly to the resource. Example - EC2 instance, Lambda ENI, RDS, EKS Nodes.  
Example a web server should receive HTTP traffic - Inbound Port 80 Allow 0.0.0.0/0 and Port 443 Allow 0.0.0.0/0 User will only be able to access those two ports.


**Security Group**.
SG characteristics - Stateful, Instance Level, Allow Rules Only, Default Deny All, Supports Tags and References.

Stateful - `Inbound Allow port 443` when client sends the request to the server then the server will send the response back to the client no need to make outbound rule. AWS remembers the connection state.

Allow Rules Only - SG will not explicitly deny traffic. It will say Allow 443, Allow 80, Allow 22. Anything that is not allowed will be denied by default.

Attached to Resource - SG are associated iwth EC2, RDS, EKS worker nodes.

Example in Production.  
ALB - Application Server - Database.  
The SG group set up ALB SG (Allow 443 from Internet) - App SG (Allow 8080 from ALB SG) - DB SG (Allow 5432 from App SG)   
The SG to SG communication instead of IP addresses.

**Network Access Control List**.  
It operates at subnet level.   
Every subnet can have a NACL. It is the security point before traffic enters a subnet (Internet - NACL - Subnet - Instances).

NACL characteristics - Stateless, Subnet Level, Allow and Deny Rules, Default Allow All, Supports Rules with IP Addresses.  

Stateless - Application iInbound allow Port 443 the traffic enters and the response traffic must also be allowed explicitly like Outbound Allow Ephemeral ports. AWS does not remember the connection state.

Allow and Deny Rules - NACL supports `DENY 192.168.1.100`, `ALLOW everyone else` It means NACL clock IP ranges.

Evaluated by Rule Number - The rule `100 Allow 10.0.0.0/16`, `110 Deny 1.2.3.4`, `120 Allow All` and processed in order meaning the first match rule wins.

**Things in GCP to know**.

VPC Firewall Rules - It controls the ingress and egress traffic - source, destination, protocol, port, action.

Hierarchical Firewall Policy - Applied at Organization, Folder and Project level. It allows centralized management of firewall rules across multiple projects.

Clous Armor - Web application firewall. It protects against DDos, IP Filtering, GEO blocking and placed in front to LB.

Private Google Access - Allow private VMs to access Google APIs without public IPs.

VPC Service Controls - Protects against data exfiltration between projects and services.

Summary - Security Groups typically handle application access while NACLs provide an additional defense-in-depth layer. In GCP, the closest equivalent is VPC Firewall Rules, along with Hierarchical Firewall Policies and Cloud Armor for advanced network security.


Load Balancer - AWS ALB/NLB ≈ GCP Cloud Load Balancer.

AWS EC2 ≈ GCP Compute Engine.

AWS S3 ≈ GCP Cloud Storage.

AWS RDS ≈ GCP Cloud SQL.
AWS EKS ≈ GCP GKE.

AWS IAM ≈ GCP Security Account + IAM.

AWS CloudWatch ≈ GCP Cloud Monitoring + Logging.

AWS Route 53 ≈ GCP Cloud DNS.



### You have 6 VPCs and all of them need to communicate with each other. Would you use VPC Peering or Transit Gateway? Why?

When its 2 VPCs, VPC Peering is usually sufficient.

However, if I have 6 VPCs that all need to communicate with each other, I would prefer AWS Transit Gateway over VPC Peering.

**VPC Peering** - It creates direct connection between 2 VPCs. For 6 VPCs, I would need to create 15 peering connections (n(n-1)/2 = 6*5/2 = 15). This becomes complex to manage and scale. It is a mesh  topology.

Issue in VPC Peering.  

Route Management Complexity - Every VPC route table needs update. The VPC count increases means more routes, maintenance and chance of mistakes.

Scalability Issue - There are 6 VPc and next 20 VPC then the number of peerings explodes. Managing them becomes operationally difficult.

No transitive Routing - There is no connection between VPC A and VPC C through VPC B `VPC-A ←→ VPC-B ←→ VPC-C` The traffic is not forwarded. AWS peering is non-transitive. 

**Transit Gateway** - It acts as a central networking hub. All VPCs connect to the Transit Gateway. It simplifies routing and allows transitive communication between VPCs.

Benefits.

Hub-and-Spoke Architecture 0 Instead of 15 peering we need 6 connection (one per VPC).

Transitive Routing - AWS Transit Gateway supports VPC A - B - C and traffic can pass through the central gateway.

Easier Route Management and better enterprise scale.  
In GCP the Network Connectivity Center NCC act as Transit Gateway.


### There are 6 customer AWS account and one of the customer is moving away and the customer who is moving we store the data in shared account. How will you remove the data from the customer and store in the shared account.

It is the cross account communication between one account to another. In one account we will create the IAM role and in other account we define the bucket policy to define the IAM role.

### When you hit abs.com what happens from the browser to the cluster to the port.

### There is Kubernetes and I have to deploy a security agent to see what pods are running and monitoring what kind of deployment should I use daemon state, deployment state or the stateful state.

### Explain the architecture of Kubernetes.

### Jenkins pipeline and there is storage issue and how to troubleshoot.

### The server is like 8gb and jenkins running in it EC2. The utilization is 100 % then how to autoscale.

### Running jenkins in my EC2 machine and I have to see what are the working spaces has been create on my server. In which directory to see the worker created.

### What is multibranch pipeline.
### What are the different test cases you have written in jenkin pipelines.

test case sonarcube.  
docker push and docker build
nexus artifacts
git secret checks


### When you connect the jenkins to the github its a token wher to store the token?

### What is parameterized pipeline.
### The cutomer 5 infratructure created separately. How to make a jenkin pipeline to deploy the customer infratrsucture in a group or single.

### I have the EC2 machine and I have to install it like the git command. Automating EC2 server.



## Jenkins & CI/CD Pipelines
Jenkins Storage Troubleshooting: How would you troubleshoot and resolve a Jenkins pipeline failure caused by storage or out-of-memory issues, and how do you expand EBS storage for an EC2 instance running Jenkins when its volume is full
?
Jenkins Workspace Location: In which directory on an EC2 server can you find and inspect the workspaces created by Jenkins
?
Multibranch Pipelines & Stages: What is a multibranch pipeline in Jenkins, and what specific build and testing stages (e.g., SonarQube, Trivy, Docker) do you include in your pipeline
?
Credentials Management: Where and how do you securely store sensitive tokens or credentials (such as a GitHub personal access token) inside Jenkins
?
Parameterized Pipelines: What is a parameterized pipeline in Jenkins, and in what practical scenarios would you use parameterization
?
Multi-Account Deployment Pipeline: How can you design a single Jenkins pipeline to deploy infrastructure across multiple AWS accounts for different customers without creating separate pipelines for each
?
How did you build your Jenkins CI/CD pipeline to cut deployment time by 70%?
How and where do you store credentials securely in Jenkins?
How do you handle parallel deployments for 10 microservices at once in Jenkins?
What specific stages do you include in your Jenkins deployment pipeline?
How does your Jenkins pipeline authenticate and deploy to OpenShift?
What security best practices do you implement for Jenkins and AWS cloud infrastructure?

Jenkins CI/CD Pipeline Stages: "Can you walk through the key stages you configure in your Jenkins CI/CD pipeline to build and deploy applications?"
## AWS Infrastructure & Management

Automating EC2 Setup: How can you automate prerequisite setup tasks (like installing Git) on an EC2 instance so that tools are already installed when the instance launches, avoiding manual setup scripts after boot
?
S3 Temporary File Access: How do you grant temporary access (e.g., for 5 to 10 minutes) to a specific private file in an Amazon S3 bucket without making the bucket public
?
S3 Scope: Is Amazon S3 a global service or a regional service
?
Amazon Route 53: What is the primary purpose of Amazon Route 53, and what common DNS record types do you create within it
?




Handling Traffic Spikes on EC2: "Suppose your application is running on an EC2 instance and experiences a high traffic spike during business hours. How would you handle this situation?"
Troubleshooting EC2 Instance Crashes: "Suppose an EC2 instance hosting your application crashes unexpectedly. What systematic troubleshooting approach would you take to diagnose and resolve the issue?"
Outbound Internet Connectivity Issues: "If an application running on an EC2 instance needs to download files/images from the internet but fails to connect, what are the possible root causes and how would you fix them?"
Data Recovery in Amazon S3: "If data in an Amazon S3 bucket is accidentally deleted, how would you restore or recover that data?"
On-Premises to Cloud Connectivity: "How would you establish a secure connection between an on-premises data center and your AWS cloud network?"
Cross-VPC Communication Failure: "Suppose your front-end service is running on an EC2 instance in VPC A, and your back-end service is on an EC2 instance in VPC B. If the front-end cannot communicate with the back-end, what issues could be causing this, and how would you establish connectivity?"
EC2 Access to Amazon S3: "An application running on an EC2 instance needs to fetch objects stored in an S3 bucket, but access is denied. How would you enable secure communication between the EC2 instance and the S3 bucket?"

## Introduction & AWS Networking
What are your day-to-day roles and responsibilities in your current project?
What AWS services have you used in your projects?
How do you secure data stored inside an Amazon S3 bucket?
How can you automate EC2 setup so that installed software (like Python and Git) is not lost when instances are terminated daily?
What is the difference between a public and private subnet, and where do you place a NAT Gateway?
How do you enable communication between multiple VPCs (e.g., an EC2 instance in VPC A accessing a database in VPC B)?
How do you establish connection to a private EC2 instance from on-premises or external networks?
How would you troubleshoot an issue where a web application functions fine, but its product images stored in an S3 bucket fail to load?
What is the cool-down period in an AWS Auto Scaling Group (ASG)?






## Terraform.

Remote Execution via Terraform: How do you establish a connection to an existing, running EC2 instance and execute installation commands on it using Terraform
?
Fetching Existing Resources: How do you query and fetch the ID or configuration of an existing, manually created AWS resource (like a VPC) at runtime using Terraform

What kind of Terraform modules have you provisioned?
How do you conditionally provision resources in Terraform?
How do you handle sensitive credentials in Terraform best practices?
What is the difference between using count and for_each in Terraform?
What is the purpose of the data block in Terraform?
How do you manage separate configurations and state files for different environments or customers?
What happens when you run terraform init, and is the state file created before or after infrastructure provisioning?
Provisioning Multiple Identical Resources: "If you need to provision 10 EC2 instances with identical configurations using Terraform, how would you structure your code?"
Referencing Existing Infrastructure (data blocks): "Suppose a VPC was created manually in AWS and you need to provision an EC2 instance inside it using Terraform. How would you reference and use this existing VPC in your script?"
Handling Manually Deleted Resources: "If an AWS resource managed by Terraform is manually deleted from the AWS Console and you run terraform apply again, what will happen?"
Passing Outputs Between Modules: "How do you pass output values from one Terraform module (such as a VPC ID or Security Group ID) into another module (such as an EC2 module) when provisioning them together?"


## Docker, Kubernetes & OpenShift.

What best practices do you follow when writing a Dockerfile?
What is the difference and specific use cases between CMD and ENTRYPOINT in Docker?
How many worker nodes are in your cluster, and how do you utilize OpenShift vs. EKS?
What command is used to run a container from a local Docker image?
Is it better to deploy a web application on EC2 or containerized via Docker/EKS, and why?
What command do you use to create a directory inside a Dockerfile?


Kubernetes Core Architecture: "Can you briefly explain the core architecture and main components of Kubernetes?"
Upgrading Amazon EKS Clusters: "How would you upgrade an Amazon EKS cluster, including its worker nodes, while managing running application workloads?"
Pod Distribution Across Nodes (DaemonSets / Anti-Affinity): "Suppose you need to deploy a logging or monitoring agent (such as Prometheus or Filebeat) and ensure that exactly one pod runs on every worker node in the cluster. How would you configure this pod distribution?"
Ingress & Domain Routing: "Suppose an application is running inside a Kubernetes pod and you want users to access it via a custom domain (e.g., ring.com) in a browser. How would you set up the full networking and routing architecture for this?"
## Linux, Automation & Troubleshooting

What Ansible playbooks have you written, and what challenges did you face connecting across Core and DMZ servers?
How do you monitor CPU and memory utilization on your servers?
What Linux command lists all running processes on a server?
What shell scripting tasks have you implemented in your environment?
How do you implement cost optimization for development EC2 instances that are not needed at night?
Why and when do you use crontab in Linux?
How do you handle log rotation when log files consume high disk space?

Troubleshooting 'Permission Denied' Errors: "When attempting to execute a custom shell script in Linux, you receive a 'Permission denied' error. How would you troubleshoot and fix this issue?"
Linux Performance & Resource Usage Troubleshooting: "If a Linux server is performing very slowly or experiencing high CPU/memory utilization, how would you systematically troubleshoot the server, and what commands would you run?"







Interview total 50 videos - 10 mins.






































Shells are interactive. They take commands as input from the users and execute them on pressing enter. A shell has its own syntax just like any other programming language. A sheel script consists of shell keywords, shell commands, control flow, and functions.

1. What is a kernel?

Answer: The kernel is a computer program at the core of a computer’s operating system that manages operations of computer and hardware.
2. What is shell?

Answer: A shell is a command line interpreter. Shell acts between the kernel and the user. Shell is a complete environment designed in orser to run commands, shell scripts, and programs. The shell communicates with the kernel to execute the given commands whenever the user enters any input commands through the keyboard. The type of shell changes depending on the type of the operating system being used in the system. Shells issue the prompt, $, called a command prompt. The user can type into the prompt while it is displayed. When we press enter, the command given as input is read by the shell.
3. Why a shell script is needed?

Answer: A shell script is needed for the following reasons:

Keep repetitive tasks to a minimum.

We can monitor the system easily.
-
We can add any type of new function easily.

We can create our own tools.

System can automate various daily tasks.
4. What are the advantages and disadvantages of shell scripting?

Answer: The advantages of shell scripting are as follows:

Shell scripting is an interactive debugging tool.

Programmers do not need o change their syntax.

Shell scripting saves the time of the user as it helps to automate the tasks.

Shell scripts can run on Windows, Linux, or MacOS as they are written in an interpreted language.

We can develop our own custom operating system with our own features.

We can develop software applications according to the platforms.

Shell scripts are easy to use and are quicker.
The disadvantages of shell scripting are:

The speed of execution is quite slow.

Errors can alter any given command completely.

We will face difficulties in executing large and complex problems.

Shell scripting does not provide many data structures for use.

A new process is launched as soon as the shell command is executed.
5. What are control instructions?

Answer: Control instructions are different instructions which decide the execution of the script. Control instructions help us to control the flow of the execution of any program.
6. What will be the status of a process executed using the exec command in the shell?

Answer: All the new forked processes get overlays on executing exec. The command gets executed without any impact on the current process, and no new process is created in the scenario.
7. What are the different types of variables used in shell scripting?

Answer: Shell scripting has two types of variables. They are

1. System-defined variables
   They are built-in variables in the Linux kernel for each shell. They are also known as environmental variables. They are generally defined by capital letters.
   Example: SHELL

2. User-defined variables
   They are created and defined by users in order to store, access, read, and manipulate the data. Generally, they are defined in small letters.
   Example: $a=10


Sample day experience.

AWS Infrastructure & Terraform: Creating AWS infrastructure—including VPCs, EC2 instances, S3 buckets, AWS Secrets Manager, Certificate Manager, Load Balancers, and Auto Scaling Groups—using Terraform modules
.
Kafka & Database Support: Deploying Kafka schemas, creating Kafka users, managing MirrorMaker replication configurations, and handling production support
.
OpenShift & Kubernetes: Deploying and managing applications on OpenShift, as well as provisioning Amazon EKS clusters for Prometheus monitoring
.
CI/CD Pipelines (Jenkins): Designing and maintaining Jenkins CI/CD pipelines
. Created Jenkins Shared Libraries for 40–50 applications to eliminate code duplication, reducing deployment setup time by 70%
. Integrated security tools (SonarQube, Checkmarx, Black Duck) into pipelines
.
Ansible & Configuration Management: Writing Ansible playbooks, such as automating the installation of Active Directory clients and Cortex security agents across 40–45 servers in complex Core and DMZ network environments
.
Shell Scripting & Maintenance: Authoring Shell/Bash scripts for updating Confluence packages, updating configuration files, and managing log file rotations
.
System Monitoring: Utilizing Zabbix (installing agents and configuring templates) for server monitoring and alerting on high CPU and memory utilization
.

Sample Experience.

Docker & Kubernetes: Writes YAML deployment, ingress, and service files, and assists developers in writing Dockerfiles
.
AWS Cloud: Hands-on experience with VPC, EC2, RDS, CloudWatch alarms, and Route 53
.
CI/CD Tools: Currently using GitHub Actions; previously worked with Jenkins and GitLab
.
Infrastructure as Code (Terraform): Writes modules for EC2, RDS, and VPC networking setup
.