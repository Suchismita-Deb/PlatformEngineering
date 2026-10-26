GCP database and storage.

Cloud SQL - Relational data, SQL transactions and joins.  
Firestore - Document-oriented application data.  
Spanner - Globally distributed relational workloads requiring strong consistency and horizontal scalability.  
BigQuery - Analytical queries, reporting and large-scale data analysis.


### Cloud run vs GKE.

Cloud run is a fully managed Google cloud service for running containerized applications. Google manages the infrastructure and user needs to take care of the application and container image.  
Example - We package the Spring boot application into the docker image and push it to the Artifact Registry and deploy it in cloud run.

Client (HTTP request) - Cloud Run (Https endpoint, routing and scaling) - Spring Boot container (controller, services) - Cloud SQL.

|Topic|Cloud Run|GKE|
|---|---|---|
Infrastructure Management| Google manages it.|User manage the configuration and workloads and Google manages the control plane in standard GKE.|
Deployment Unit|Container Image|Kubernetes workloads and manifest.|
Scaling|Manages instance scaling.|HPA, cluster autoscaling.|
Networking|Managed service endpoints and ingress settings.|Kubernetes service, Gateway/ingress and cluster networking.
Best execute|APIs, webhooks, event handler and containerized services.|Complex platform, specialized networking, workload orchestration and kubernetes specific requirements.

Cloud Run for stateless spring boot API with managed infrastructure and automatic scaling.  
GKE for the Kubernetes specific capabilities and more control over the orchestration or complex platform with cooperating workloads.

### Cloud run scaling.
Cloud run scales the number of instances based on incoming demand and configured limits.

Concurrency is the maximum number of request a single instance can process.   
Example - Maximum concurrency = 80 requests per instance.  
One instance is running.   
Incoming demand rises above what the instance can handle.

Cloud run may create additional instances based on the scaling configuration and available capacity.  
When the service needs to handle 250 concurrent requests and each instance can effectively handle 85 an estimate is 250/85 = 3

It is an example and it depends in CPU, request utilization, requests duration, startup time and configured limits.




Why the concurrency matter?

Too high - One instance can become CPU bound or exhaust the db connection.  
Too low - Cloud run may need more instance, increasing startup overhead. 
It balances latency, resource usage and instance count.

Cloud run concurrency is not the same as thread. A single request may use one or several threads depending on the application architecture.

### What happens when cloud run service receives more traffic than its maximum instance can handle?

Example the service has maximum instance = 10 and concurrency = 50 request per instance.

When the traffic exceeds the capacity cloud run cannot scale beyond the configured maximum. Request will experience increased latency, time out. Depending on the overload and request path, client may receive like 429 or 5xx response.

How to handle?

The first step see the request rate, latency, error rate and instance utilization.  
In case the maximum instance limit is too restrictive.  
The database or the downstream apis the bottleneck.   
The concurrency and the CPU or memory allocation based on the load tests.  
Apply the rate limiting or backpressure when needed.  
The client can use the bounded retries with exponential backoff and jitter.

Increasing the max instance count is not the right fix. In case every instance opens 50 db connections and scaling from 10 to 100 instances will overwhelm cloud sql. When scaling the limits the downstream capacity should also be taken care.

### how to reduce the cold start in clous run.

Cold start meaning occurs when the cloud run needs to start a new instance before it can serve the request. Starting a new java application can take longer because the JVM and spring application context must initialize.  
A spring boot application needs to start the JVM, initialize the spring beans and auto configuration, initialize the db connection pools. Load the libraries and application configuration.

How to reduce the cold start?

Configure minimum instances - Keep one or more instances available to reduce the likelihood of cold start. It will increase the baseline cost.  

spring boot startup - Avoid unnecessary dependencies and expensive initialization.  



