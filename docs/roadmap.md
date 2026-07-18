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

### Exit Criteria

Deploy a production-ready service.

------------------------------------------------------------------------

# Phase 9 -- Distributed Systems (High)

## Know by heart

-   Idempotency
-   Retries
-   Circuit breakers
-   Caching
-   Async messaging
-   Eventual consistency

### Exit Criteria

Explain failure modes and recovery.

------------------------------------------------------------------------

# Phase 10 -- System Design (Continuous)

## Know by heart

-   Scalability
-   Availability
-   Partitioning
-   Caching
-   Rate limiting
-   Messaging
-   CAP trade-offs

### Exit Criteria

Design production systems with justified trade-offs.