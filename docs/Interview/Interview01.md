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

Linux knowledge - top, ps, grep, awk, sed, curl, netstat, ss, lsof, df, du, free, tail, systemctl

The networking part Request - Lb - Ingress - Service - Pod - Application.

### Writing clean code to parse logs, handle files, and implement timestamp-based processing logic.

### AWS Integration: Scenario tasks involving S3 bucket event notifications routed to an SQS queue, paired with object size or metadata validation.

### How do you design and troubleshoot a failing CI/CD pipeline?

### Explain your experience with Infrastructure as Code (IaC) tools like Terraform or cloud templates.

### How do you manage secrets securely in deployment pipelines (e.g., using HashiCorp Vault or native cloud secret managers)?

### How do you implement zero-downtime deployment strategies (e.g., Rolling Updates or Blue-Green deployments)?

### What is the difference between monitoring and observability in distributed cloud environments?

### How do you apply chaos engineering principles to test system resilience?

### Update the secret manager and make sure that the application not impacted. In case of password rotation how to make sure the application will not be down.

### Lifecycle of the terraform.
init then plan then apply.

### create before destroy the lifecycle.

### I have an instance EC2 and I want th epython to be installed automatically and not manually. How will you do it?

### What do you understand by NACL and the Security groups.

Security group works at instance level and the nacl we have to update the incoming and the outgoing traffic.

### I have 6 vpc and to make the communication between all the 6 VPCs what should be used like the peering or the transmit gateway and why?

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