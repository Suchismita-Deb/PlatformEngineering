### Replication Controller.

Auto Healing and High Availability - Standalone Pods are vulnerable to failure if a single Pod crashes user will get no reply. A replication controller monitors pod health and spins up replacement pods to ensure the desired number of instances remains active.

Scaling & Load Distribution - It supports manual scaling by adjusting the desired replica count and can span Pods across multiple worker nodes to handle increased application traffic.

Configuration Structure - Uses `apiVersion: v1` and `kind: ReplicationController`. The Pod configuration is specified inside the `spec.template` field alongside `spec.replicas`.

Limitation - Can only manage Pods directly created by itself.

The yaml manifest.
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    env: demo
spec:
  replicas: 3
  template:
    metadata:
      labels:
        env: demo
    spec:
      containers:
      - name: nginx
        image: nginx
```
### ReplicaSet.

ReplicaSet is the newer and preferred controller over the legacy Replication Controller.

Flexible Label Selectors - Unlike Replication Controllers, ReplicaSets introduce selector. The matchLabels, allowing them to manage existing Pods with matching labels even if they were created separately. Uses `spec.selector.matchLabels` to adopt and manage existing Pods with matching labels, even if those Pods were created independently outside the ReplicaSet.

The yaml manifest.
```yaml

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    env: demo
spec:
  replicas: 3
  selector:
    matchLabels:
      env: demo
  template:
    metadata:
      labels:
        env: demo
    spec:
      containers:
      - name: nginx
        image: nginx
```

API group - Use `apiVersion: app/v1`

Three Ways to Scale - Declarative Manifest Update (Update replicas in the YAML file and run `kubectl apply -f file.yaml`), Live Object Editing (Edit running cluster objects directly with `kubectl edit rs/<name>`), Imperative Command (Instantly scale using `kubectl scale --replicas=<number> rs/<name>`)


###  Kubernetes Deployment.
A Deployment sits at a higher abstraction level, managing ReplicaSets, which in turn manage Pods and all are done by the labels and selectors (`Deployment -> ReplicaSet -> Pods`). 

Zero-Downtime Rolling Updates - While a ReplicaSet recreates Pods all at once (causing downtime during updates), a Deployment performs rolling updates. It incrementally updates Pods while remaining Pods continue to serve live user traffic.

Rollbacks - If an update breaks the application, you can view deployment history (`kubectl rollout history`) and instantly revert changes using `kubectl rollout undo`.

The yaml file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    env: demo
spec:
  replicas: 3
  selector:
    matchLabels:
      env: demo
  template:
    metadata:
      labels:
        env: demo
    spec:
      containers:
      - name: nginx
        image: nginx
```

<div class="quiz-box">
<b>Deployment ReplicaSet Pod is connected through labels and selectors</b>
<details class="quiz-toggle">
<summary>In depth</summary>
The connection is not done as they are nested objects. The connection is done be the selectors, labels.  
The pod template command.
```yaml
template:
  metadata:
    labels:
      app: nginx
      version: v1
```
Label is a key-value metadata attached to the objects. The resulting pod gets <code>Labels: app: nginx version: v1</code>

The multi pod with the name and version and Kubernetes will Get the pod where app=nginx.  
The <code>selector.matchLabels</code> will get the pod with the name.

```text 
Pod A
app=nginx
version=v1

Pod B
app=nginx
version=v1

Pod C
app=redis
version=v1
```
A Deployment's selector primarily identifies the Pods managed by its ReplicaSets.

The yaml of the deployment.
```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:1.25
```
The main part like the `spec.selector.matchLabel` and the `spec.template.metadata.label` are the same and it is intentional.

The main part is one deployment - one replicaset - one pod(the pod can be iof 3 replica like nginx replica 3 but it will not contain nginx and java)    
One deployment file can have one replicaset (multiple version but one type).

Say there is a need to make the nginx and the java then the deployment file will be different.
```yaml
# nginx-deployment.yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.25
```

```yaml
# payment-deployment.yaml
spec:
  replicas: 5
  selector:
    matchLabels:
      app: payment
  template:
    metadata:
      labels:
        app: payment
    spec:
      containers:
        - name: payment
          image: eclipse-temurin:21
```

<b> When the matchLabel and the Label name is same then why they are needed?</b>

```yaml
spec.selector.matchLabels.app: nginx
spec.template.metadata.labels.app: nginx
```
Think like matchLabel - Which pod belongs to me?  
label - What labels should the pods I create have?

The replicaset says I manage Pods where app=nginx and the template says When I create a pod give it a name app=nginx 

The replicaset selector finds the pod labels nginx. The matchLabel alone will not work as it will not create or label the pod.<br><br>

<b> Why the rs when there was rc?</b><br>
ReplicaSet has teh selectro and the matchExpression meaning the selection of pod configured and RC is basic equality. Depoyment supports rs.

The ReplicaSet supports matchExpression.
```yaml
selector:
  matchExpressions:
    - key: environment
      operator: In
      values:
        - production
        - staging
```
It means the env selection and more configuration to pod selection.
</details>
</div>

| Operation | Command | Purpose |
|---|---|---|
| **Apply Deployment** | `kubectl apply -f deploy.yaml` | Creates or updates the Kubernetes resources defined in the YAML file. |
| **View Deployments** | `kubectl get deploy` | Lists all Deployments in the current namespace. |
| **View All Cluster Objects** | `kubectl get all` | Displays common resources such as Pods, Services, Deployments, and ReplicaSets. |
| **Perform Live Image Update** | `kubectl set image deploy/nginx-deploy nginx=nginx:1.9.1` | Updates the container image of an existing Deployment and triggers a rollout. |
| **Verify Deployment Update** | `kubectl describe deploy nginx-deploy` | Shows detailed information about the Deployment, including replicas, image, conditions, and events. |
| **Check Rollout Revision History** | `kubectl rollout history deploy/nginx-deploy` | Displays the revision history of the Deployment. |
| **Undo / Rollback Deployment** | `kubectl rollout undo deploy/nginx-deploy` | Rolls the Deployment back to the previous revision. |
| **Check Rollout Status** | `kubectl rollout status deploy/nginx-deploy` | Shows whether the latest Deployment rollout has completed successfully. |
| **Restart Deployment** | `kubectl rollout restart deploy/nginx-deploy` | Performs a rolling restart of all Pods managed by the Deployment. |

### Service
A Service acts as an abstraction layer with a static IP address (ClusterIP) and a persistent DNS host name. It sits in front of a set of Pods, decoupled from their life cycle, and automatically load-balances incoming traffic across all healthy Pod instances.  

Every Pod in Kubernetes receives its own internal IP address. However, Pods are ephemeral—if a Pod crashes, restarts, or scales, Kubernetes terminates it and spins up a new one with a different IP address.

**The port configuration**.

In Kubernetes Service there are 3 distinct port parameters - NodePort, Port and TargetPort.

```text
User - (Nodeport 30001) - Worker Node IP (Port 80) - Service IP - TargetPort (80) - Container.
```

TargetPort - The actual port on which the application container inside the Pod is listening (e.g., Port 80 for Nginx).

Port - The internal Service port used by other Pods or internal resources within the cluster to talk to this Service.

NodePort - The static port exposed on every Worker Node's IP address to route external traffic into the Service(range - 30000 to 32767).

**4 Service Types**

ClusterIP (Default) - Exposes the Service on an internal cluster-only IP. Reachable only from within the cluster (used for inter-pod communication, e.g., Frontend -> Backend or Backend -> Database)

NodePort - Exposes the Service on each Node's IP at a static port between(30000–32767). Automatically creates an underlying ClusterIP Service to route external traffic (NodeIP:NodePort) to internal Pods.

LoadBalancer - Integrates with cloud providers (AWS, Azure, GCP) or bare-metal load balancers.  
Automatically provisions an external cloud load balancer that routes traffic to a NodePort and ClusterIP behind the scenes.

ExternalName - Maps a Kubernetes Service directly to an external CNAME record/DNS domain (e.g., an external database at db.example.com) without using selectors.




