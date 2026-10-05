# Senior Backend → Platform Engineer Roadmap

## Mission

Become a Senior Software Engineer capable of owning production systems end-to-end (Google/Amazon standard).

## Ground Rules

-   Learn only skills that solve real production problems.
-   Follow the 80/20 rule: master the critical 20% used daily.
-   Every topic ends with: Build → Debug → Explain.
-   Do not move to the next phase until comfortable.

## Skill Matrix

Domain           |     Priority |    Current| Target 
  ---------------------| ---------- |--------- |--------
Linux         |        Critical   |     5/10   |   9/10 
Networking     |       Critical   |     4/10   |   9/10 
Docker       |         Critical   |     5/10  |        |  9/10
Kubernetes    |        Critical   |     3/10  |  9/10
Cloud        |         Critical   |     3/10 | 8.5/10
Observability  |       Critical   |     3/10  | 8.5/10
CI/CD          |       High       |     4/10  | 8.5/10
Databases       |      High       |     7/10  |   9/10
Performance     |      High       |     5/10  | 8.5/10
Distributed Systems |  High       |   6.5/10  |   9/10
System Design     |    High    |        6/10  | 9.5/10

------------------------------------------------------------------------

# Phase 1 -- Linux (Critical)

## Know by heart

-   Filesystem layout
-   Permissions
-   Processes & signals
-   CPU, memory, disk
-   Logs (`journalctl`)
-   `ps`, `top`, `htop`, `ss`, `lsof`, `grep`, `find`, `tail`

### Exit Criteria

-   Diagnose high CPU
-   Diagnose memory leak symptoms
-   Find why a service isn't starting

------------------------------------------------------------------------

# Phase 2 -- Networking (Critical)

## Know by heart

-   TCP vs UDP
-   DNS
-   HTTP/HTTPS
-   TLS basics
-   Load balancers
-   Timeouts, retries, keep-alive

### Exit Criteria

Trace a request end-to-end and explain every hop.

------------------------------------------------------------------------

# Phase 3 -- Docker (Critical)

## Know by heart

-   Images
-   Containers
-   Layers
-   Volumes
-   Networks
-   Multi-stage builds
-   ENTRYPOINT vs CMD

### Exit Criteria

Containerize and troubleshoot a Spring Boot app.

------------------------------------------------------------------------

# Phase 4 -- Databases & Performance (High)

## Know by heart

-   Indexes
-   Transactions
-   Isolation
-   EXPLAIN plans
-   Connection pools
-   JVM heap, GC, thread pools

### Exit Criteria

Optimize one slow query and one slow API.

------------------------------------------------------------------------

# Phase 5 -- CI/CD (High)

## Know by heart

-   Build
-   Test
-   Artifact
-   Deploy
-   Rollback
-   Blue/Green
-   Canary

### Exit Criteria

Create a pipeline with automated deployment.

------------------------------------------------------------------------

# Phase 6 -- Kubernetes (Critical)

## Know by heart

-   Pods
-   Deployments
-   Services
-   ConfigMaps
-   Secrets
-   Ingress
-   Requests/Limits
-   Liveness & Readiness
-   Autoscaling

### Exit Criteria

Deploy, scale, update, rollback, debug CrashLoopBackOff/OOMKilled.

------------------------------------------------------------------------

# Phase 7 -- Observability (Critical)

## Know by heart

-   Logs
-   Metrics
-   Traces
-   Dashboards
-   Alerts

### Exit Criteria

Find the root cause of an injected production issue.

------------------------------------------------------------------------

# Phase 8 -- Cloud (Critical)
## Know by heart

-   Compute
-   IAM
-   Networking
-   Object Storage
-   Managed DB
-   Kubernetes Service
-   Monitoring
-   Secrets  

- The Devops engineering part ask for the work like the **kernel datapath, packet flow, state management, traffic governance**.

### Expectations.
**Linux Isolation Primaries and Container Networking Datapath basics**.  

**Linux Namespace** - How net(network interface, routing tables, sockets), pid, mnt, ipc, uts, user, cgroup namespaces work and isolates the workloads.  
**Virtual Ethernet (veth) pair** - How veth pair works and how virtual cables bridge a containers isolated network namespace(netns) to the host network namespace.  
**Docker Networking Drivers** - Difference between bridge(single-host local scope with port mapping), Overlay(multi-host cluster scope with VXLAN encapsulation), Macvlan(directly attach container to host network, lightweight L2 attachments with direct routable IPs not using NAT) and Host(no isolation, container shares host network namespace) networking drivers.

**Kubernetes Network Model**.<br>
Fundamental Const


Kubernetes networking and security.

The tools like Calico, Cilium managing traffic, load balancing and eBPF based technology.  
They outline how packet routing, namespaces, and virtual Ethernet pairs facilitate communication between pods and nodes, while addressing the underlying role of CNI plugins.


Kubernetes - Replicaset and Replica Controller.
They are part of the deployment to make th deployment rolling update.
Services - ClusterIP, NodePort, LoadBalancer, ExternalName.




JD.

Hands on experience with monitoring and logging tools (e.g., Prometheus, Splunk, Grafana, CloudWatch).
Proficient in Linux, Networking concepts (TLS/SSL, DNS, Load Balancers, etc..) and troubleshooting skills in large scale environments.
Source control management such as Git / Understanding of CI/CD, Release Engineering and DevOps.
Understanding of security standards, policies, and cryptography.
Experience with Incident / Problem management and RCA.
Strong Network, Load Balancing (Nginx, Envoy, NetScaler) experience is a huge plus.
Good solid understanding using Kubernetes concepts such as networking, Storage, Secrets, Deployments & Containerization.
Hands-on experience with AliCloud, AWS, or GCP is preferred.
Strong analytical skills
