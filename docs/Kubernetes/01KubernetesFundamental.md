Kubernetes.

Why?

Without kubernetes there the system included a scrappy script or manual effort to see teh processes. Kubernetes replaces the control loop - declare the state (3 replicas of the app, the image, the resource limit) and a controller continuously reconciles the reality to match. Kubernetes object in this way.  

### The core chain

**Pod** → smallest deployable unit. One or more containers sharing an IP and storage. In practice, 1 container per pod (sidecars are the exception, not the rule). Pods are disposable — they don't self-heal, and they get a new IP every time they're recreated.

**Deployment** → manages a ReplicaSet, which manages Pods. You declare "keep 3 replicas of this spec alive" and it handles creation, replacement of dead pods, rolling updates, and rollbacks.

**Service** → a stable network identity in front of a set of pods (matched by label selector), since pod IPs are throwaway. This is how one pod talks to another reliably.

So: `Deployment → ReplicaSet → Pods`, and separately `Service → (label selector) → Pods`.



Minimal Manifest.
```jsx
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels: { app: api }
  template:
    metadata:
      labels: { app: api }
    spec:
      containers:
      - name: api
        image: myorg/api:1.4
        resources:
          requests: { cpu: "200m", memory: "256Mi" }
          limits:   { cpu: "500m", memory: "512Mi" }
        readinessProbe:
          httpGet: { path: /healthz, port: 8080 }
          initialDelaySeconds: 5
        livenessProbe:
          httpGet: { path: /healthz, port: 8080 }
          initialDelaySeconds: 15
```

`requests` = what the scheduler reserves for the pod. `limits` = the hard ceiling. Get the gap wrong and you get evictions or OOMKills — this is where most junior mistakes live.

`kubectl apply -f deploy.yaml` → `kubectl get pods` → `kubectl describe pod <name>` → `kubectl logs <name>`. That four-command sequence is 80% of daily K8s work.

### Debugging — this is what actually gets tested in interviews

**CrashLoopBackOff**: container starts, dies, K8s restarts it, backoff timer increases each time.

- Check `kubectl logs <pod> --previous` (the crashed container's logs, not the new one).
- Common causes: missing env var/config, app throws on startup, wrong `command`/`ENTRYPOINT`.

**OOMKilled** (exit code 137): container hit its memory `limit`, kernel's cgroup killer took it out.

- `kubectl describe pod` → look for `Last State: Terminated, Reason: OOMKilled`.
- Cause is either a real leak, or `limits.memory` set too tight for actual usage.

**ImagePullBackOff**: bad image name/tag, or missing registry auth (`imagePullSecrets`).

**Pending** (never scheduled): no node has enough free CPU/memory, or a taint/nodeSelector mismatch, or an unbound PersistentVolumeClaim.

### A pod shows Running but the Service never routes traffic to it. What's the first thing you check, and why?

**The key insight you're missing: `Running` ≠ `Ready`.**

`Running` just means the container process started. `Ready` means the **readiness probe** is passing. A Service only sends traffic to pods that are `Ready` — it never even looks at whether the container is "Running." So a pod can sit there Running forever, healthy-looking, and get zero traffic because its `/healthz` endpoint is returning 500, or the app takes 30s to warm up but your `initialDelaySeconds` is 5.

**The actual debug sequence for "Running but no traffic":**

1. **`kubectl get pods -o wide`** — check the `READY` column. It'll show `0/1` instead of `1/1` if the readiness probe is failing. This is the single fastest signal.
2. **`kubectl get endpoints <service-name>`** — a Service is backed by an `Endpoints` object listing the IPs of pods it'll actually route to. If your pod isn't `Ready`, its IP won't be in this list, period. Empty endpoints = your first hypothesis confirmed.
3. **`kubectl describe pod <name>`** — now check *why* readiness is failing (probe path wrong, port wrong, app not actually up yet), or check the `Labels` section.
4. **Label selector mismatch** — the other classic cause. `Service.spec.selector` must exactly match `Pod.metadata.labels`. Typo an env-specific label (`app: api-v2` vs `app: api`) and the Service silently matches zero pods — nothing crashes, nothing logs an error, traffic just goes nowhere.

`logs` and `describe` are still useful, but only *after* step 1 and 2 tell you *where* to look — Service/endpoint layer vs. app layer.


Kubernetes.png

The full request path plus the two side-inputs that matter for real production work: config/secrets going in, and autoscaling watching from the side. Let's fill in the depth behind each box.

### Services — the three types, and when each is real

**ClusterIP** (default): internal-only virtual IP, routable inside the cluster. This is what 90% of your services should be — databases, internal APIs, anything that shouldn't be internet-facing.

**NodePort**: opens a static port (30000–32767) on *every* node's IP. Mostly a dev/testing shortcut — nobody runs this in production because you'd be bypassing your load balancer and hitting raw node IPs.

**LoadBalancer**: tells your cloud provider (AWS/GCP/Azure) to provision an actual external load balancer that forwards to the NodePort under the hood. This is the real production entry point *if you're not using Ingress*.

**The senior nuance**: a Service isn't a process or a proxy sitting somewhere — it's just an IP + a set of iptables/IPVS rules that `kube-proxy` maintains on every node. When you `curl` a Service IP, the kernel on that node rewrites the destination to one of the backing pod IPs before the packet ever leaves. There's no "service pod" to `kubectl exec` into and debug — that trips people up in interviews constantly.

DNS ties in here too: every Service gets `<name>.<namespace>.svc.cluster.local` for free, via CoreDNS. That's why microservices call each other by name (`http://payments-service`) instead of hardcoded IPs.

### ConfigMaps & Secrets

Both externalize config from your image, so the same image can run in dev/staging/prod with different values — never bake environment-specific config into a Docker image.

yaml

```yaml
envFrom:-configMapRef:{name: app-config}-secretRef:{name: app-secrets}
```

or mount as files via a volume, useful when your app expects a config file on disk rather than env vars.

**The nuance that separates senior from junior here: Secrets are base64-encoded, not encrypted.** `kubectl get secret x -o jsonpath='{.data.password}' | base64 -d` gets you the plaintext in two seconds. Anyone with `get secrets` RBAC access can read them. Real production setups add etcd encryption-at-rest, tight RBAC on the `secrets` resource, and often an external secrets manager (Vault, AWS Secrets Manager) synced in via an operator — Kubernetes Secrets alone are not a security boundary, just a distribution mechanism.

### Ingress

One important thing the diagram simplifies: **the Ingress resource does nothing by itself.** It's just a set of routing rules (host/path → service). You need an **Ingress Controller** (nginx-ingress, AWS ALB controller, Traefik) actually running as pods in your cluster to read those rules and act as the real reverse proxy. Create an Ingress object with no controller installed, and it just sits there — no traffic ever moves. This is a very common "why isn't this working" interview trap.

yaml

```yaml
rules:-host: api.myapp.comhttp:paths:-path: /usersbackend:{service:{name: users-svc,port:{number:80}}}-path: /ordersbackend:{service:{name: orders-svc,port:{number:80}}}
```

One Ingress can route to many Services by host or path — this is why you'd use Ingress over multiple LoadBalancer Services: one external IP/cost instead of one per service, plus centralized TLS termination.

### Rolling updates & rollback

This is where readiness probes actually pay off. When you run.

```bash
kubectlset image deployment/apiapi=myorg/api:1.5kubectl rollout status deployment/api
```

Kubernetes doesn't kill all old pods and start new ones — it brings up new pods *incrementally*, and only kills an old pod once a new one reports **Ready**. Two knobs control the pace.

```yaml
strategy:rollingUpdate:maxSurge:1# how many extra pods above desired count during rolloutmaxUnavailable:0# how many pods can be down during rollout
```

`maxUnavailable: 0` means zero-downtime deploys — always at least the full desired count available. If your new image is broken and fails readiness, the rollout just... stalls. It won't kill more old pods. This is a self-protecting mechanism, which is why broken readiness probes are dangerous in the other direction too (a rollout can look "stuck" when actually it's correctly refusing to proceed).

Rollback is one command.
```bash
kubectl rollout undo deployment/api# back to previous revisionkubectl rollouthistory deployment/api# see revision listkubectl rollout undo deployment/api --to-revision=3
```

### Autoscaling (HPA)

```bash
kubectl autoscale deployment api --min=2 --max=10 --cpu-percent=70
```

This creates an HPA object that polls pod CPU usage (via `metrics-server`, which must be installed — it's not built in) every 15s by default, and adjusts `.spec.replicas` on the Deployment up or down to keep average CPU near your target. It only changes replica *count* — it has no opinion on per-pod CPU/memory `limits`, which is a separate axis entirely.

Two things worth knowing exist, without needing depth yet: **VPA** (Vertical Pod Autoscaler — adjusts a pod's requests/limits instead of replica count) and **Cluster Autoscaler** (adds/removes actual *nodes* when pods can't be scheduled due to resource pressure). HPA scales pods, Cluster Autoscaler scales the nodes underneath them — different layers, often confused in interviews.

---

### Build exercise (do this before we move on)

Deploy a small app with: a Deployment (2 replicas, readiness + liveness probes), a ClusterIP Service, a ConfigMap for one env var, and an HPA targeting 70% CPU. Then:

1. `kubectl scale deployment/<name> --replicas=5` and watch new pods hit Ready.
2. Deliberately break the image tag (typo it) and run `kubectl set image` — watch the rollout stall, then `rollout undo`.
3. Delete a pod manually (`kubectl delete pod <name>`) and watch the ReplicaSet replace it within seconds.

### Debug question

Your HPA is configured with `--cpu-percent=70`, load is clearly high, but `kubectl get hpa` shows `<unknown>/70%` instead of an actual percentage, and it never scales. What's the most likely cause?


The most likely cause is one of two things, and the fix depends on which:

### 1. `metrics-server` isn't installed or is broken (the common one on fresh/self-managed clusters)

HPA doesn't invent CPU numbers itself — it queries the `metrics.k8s.io` API, which is backed by `metrics-server`, a separate component that has to be deployed and running. If it's missing, crashing, or can't reach the kubelets, HPA has nothing to read and falls back to `<unknown>`.

Check in this order:

bash

```bash
kubectltop pods# "error: Metrics API not available" → confirms metrics-server is the problemkubectl get apiservices|grep metrics# v1beta1.metrics.k8s.io should show Available=Truekubectl get pods -n kube-system|grep metrics-server# should be Running, not CrashLoopBackOff/Pendingkubectl logs -n kube-system deploy/metrics-server
```

That last one usually shows the real reason on kubeadm-style clusters: **TLS handshake failures against kubelets**, because metrics-server doesn't trust the kubelet's self-signed serving cert by default. The quick fix (dev/test) is adding `--kubelet-insecure-tls` to its args; the correct production fix is giving it a CA bundle that actually validates kubelet certs, since `--kubelet-insecure-tls` disables verification cluster-wide.

### 2. The pods have no `resources.requests.cpu` set

This one's sneakier because metrics-server can be perfectly healthy and you'll *still* see `<unknown>`. HPA's percentage is always `current usage ÷ requested amount`. No `requests.cpu` on the container spec means there's no denominator — it's not that the math comes out wrong, there's no math to do, so it shows unknown instead of a number.

bash

```bash
kubectl describe deployment api|grep -A2 Requests
```

If that's empty, that's your answer — add `resources.requests.cpu` to the container spec and the percentage appears immediately (assuming metrics-server is otherwise healthy).

**In practice, check #2 first** — it's a 5-second grep and it's the more common oversight, especially on manifests copy-pasted without resource sections. Only dig into metrics-server logs if requests are actually set and it's still `<unknown>`.

Explain rolling updates and rollbacks for a Deployment in depth

### Rolling updates & rollback — full mechanics

Quick reframe before the details: a rolling update isn't really a Deployment "action" — it's the same reconciliation loop you already know, just triggered twice in a row. When you change the pod template (new image, new env var, whatever), the Deployment controller creates a **brand new ReplicaSet** matching the new spec, and starts shifting replica count from the old ReplicaSet to the new one. The old ReplicaSet doesn't get deleted — it gets scaled down to 0 and kept around (this matters for rollback, below).

### The exact math behind maxSurge / maxUnavailable

Say `replicas: 3`, `maxSurge: 1`, `maxUnavailable: 0` (a common, safe default):

- **Surge budget** = `replicas × maxSurge` = 1 extra pod allowed above desired count → cluster can briefly run 4 pods total.
- **Unavailable budget** = `replicas × maxUnavailable` = 0 → at no point can fewer than 3 pods be Ready.

So the controller can *only* create 1 new pod at a time, and can *only* kill an old pod once a new one is Ready (since it can't drop below 3 Ready). That's why a 3-replica rollout with these settings does exactly this, one pod at a time:

V

visualize

The visual didn't render this time, so here's the same sequence as a table — same information either way:

| Step | Old pods | New pods | Total | Why |
| --- | --- | --- | --- | --- |
| Start | 3 (v1) | 0 | 3 | baseline |
| Surge | 3 (v1) | 1 (v2) | 4 | `maxSurge=1` allows 1 extra |
| New ready | 3 (v1) | 1 (v2, Ready) | 4 | waiting on readiness probe |
| Terminate old | 2 (v1) | 1 (v2) | 3 | `maxUnavailable=0` — only kill once replacement is Ready |
| Repeat ×2 | 0 (v1) | 3 (v2) | 3 | cycle continues until fully replaced |

That's the whole algorithm: **surge up by at most `maxSurge`, drain down by at most `maxUnavailable`, gated at every step by the readiness probe.** If you set `maxSurge: 0, maxUnavailable: 1` instead, you get the inverse pattern — kill an old pod first, then create the replacement. That's slower and briefly reduces capacity, but uses zero extra resources — useful when your cluster has no headroom for surge pods.

### minReadySeconds — the probe isn't always enough

`readinessProbe` passing just means "one HTTP 200." `minReadySeconds` adds a mandatory minimum time a pod must stay Ready *continuously* before it counts toward availability:

yaml

```yaml
spec:minReadySeconds:15
```

This catches apps that pass a health check immediately but then die under real traffic a few seconds later (e.g., connection pool exhaustion, slow memory leak). Without it, a flaky new version could look "successfully rolled out" for a few seconds before falling over — this setting buys you a stability window before the controller moves to the next pod.

### progressDeadlineSeconds — how a stuck rollout gets flagged.

```yaml
spec:progressDeadlineSeconds:600 # default
```

If the Deployment controller makes zero progress (no new pod becoming Ready) for this long, it marks the Deployment condition `Progressing: False` with reason `ProgressDeadlineExceeded`. **This does not auto-rollback** — Kubernetes just stops and flags it. Your CI/CD pipeline (or a human) has to notice that condition and decide to run `kubectl rollout undo`. This is a common trip-up: people assume K8s auto-heals a bad rollout — it doesn't, it just refuses to make things worse.

### Revision history — what `rollout undo` actually does

Every rollout creates a new ReplicaSet, but old ones aren't deleted — they're kept at `replicas: 0`, tagged with a revision number (visible via `kubectl rollout history deployment/api`). `revisionHistoryLimit` (default 10) controls how many are retained.

`kubectl rollout undo` is just... the same rolling-update dance in reverse: scale the old ReplicaSet back up, scale the current one down to 0, gated by the same readiness/surge/unavailable rules. There's no separate "rollback mechanism" — it's the identical reconciliation loop, pointed at a different target ReplicaSet. That's a good thing to say out loud in an interview; it shows you understand it's one mechanism, not two.

bash

```bash
kubectl rollout undo deployment/api # to previous revision
kubectl rollout undo deployment/api --to-revision=3 # to a specific one
kubectl rollout pause deployment/api # freeze mid-rollout to inspect
kubectl rollout resume deployment/api
```

`pause`/`resume` is genuinely useful in practice: pause after the first surge pod comes up, poke at it directly (`kubectl exec`, hit its metrics), confirm it's healthy, then resume — a manual canary check without any extra tooling.

### Recreate strategy — the other option

yaml

```yaml
spec:strategy:type: Recreate
```

Kills *all* old pods first, then creates all new ones. Zero surge, but a guaranteed downtime window. You'd use this when your app can't tolerate two versions running simultaneously — e.g., a schema migration that old-version pods can't handle, or a singleton process that can't have two instances touching the same resource at once.

### Debugging a stuck rollout — the sequence

1. `kubectl rollout status deployment/api` — tells you immediately if it's waiting on readiness.
2. `kubectl get replicasets -l app=api` — see old vs new RS pod counts side by side.
3. `kubectl describe deployment api` — check `Conditions` for `ProgressDeadlineExceeded`.
4. `kubectl get pods -l app=api` then `describe`/`logs` on whichever new pod isn't Ready — this drops you back into the CrashLoopBackOff/OOMKilled debugging from earlier.
5. If it's genuinely broken: `kubectl rollout undo deployment/api`.

---

**Build**: set `replicas: 4, maxSurge: 1, maxUnavailable: 0` on a real Deployment, then run `kubectl set image` while watching `kubectl get pods -w` in another terminal — you should see the exact surge/drain pattern from the table above, one pod at a time.


### you set `maxUnavailable: 0` and `maxSurge: 1` on a 3-replica Deployment, but your cluster only has exactly enough free CPU/memory for 3 pods, none spare. What happens when you trigger the rollout?

### What happens: the rollout gets stuck, but nothing goes down

**Step by step:**

1. Controller tries to honor `maxSurge: 1` and creates the 4th pod (the new version).
2. The scheduler tries to place it, but no node has enough allocatable CPU/memory left — the cluster is already at capacity with the 3 running pods.
3. The new pod goes into **`Pending`**, stuck at the scheduling stage. `kubectl describe pod <new-pod>` shows a `FailedScheduling` event: `0/N nodes are available: insufficient cpu`.
4. Because `maxUnavailable: 0`, the controller is forbidden from touching any old pod until a replacement is Ready — and this one can't even get scheduled, let alone become Ready.

**Net result:** 3 old pods keep running exactly as they were, serving traffic normally. 1 new pod sits `Pending` indefinitely. The rollout just... hangs. No outage, no degraded capacity — just stalled.

### What you'll observe

bash

```bash
kubectl rollout status deployment/api
# Waiting for deployment "api" rollout to finish: 1 out of 4 new replicas have been updated...
# (never completes)
kubectl get pods
# 3 old pods: Running.
# 1 new pod: Pending.
```

After `progressDeadlineSeconds` (default 600s), the Deployment's `Progressing` condition flips to `False` with `ProgressDeadlineExceeded` — but that's just a status flag surfacing in `kubectl describe deployment`, not an automatic fix. Nothing rolls back on its own.

### The recovery is almost free here

This is the payoff of the safety mechanism: since `maxUnavailable: 0` never let the controller touch the old pods, there's nothing to undo in terms of live traffic. `kubectl rollout undo` just scales the new ReplicaSet's stuck `Pending` pod back to 0 — a no-op from the scheduler's perspective since it was never running — and the old ReplicaSet, still at 3/3, doesn't need to change at all. Zero customer impact, one command.

**If Cluster Autoscaler is running**, it would separately notice the unschedulable pod and provision a new node, letting the rollout complete on its own instead of staying stuck — which is exactly why production clusters running rolling deployments pair `maxSurge` with either genuine headroom or an autoscaler; a cluster running at 100% steady-state capacity with no autoscaler will stall on every single rollout that needs surge capacity.

## Kubernetes Networking.

The smallest component is Pod. There should be atleast one main container inside pod.

**Why there is the need to Pod?**
Every pod has the unique IP address and the IP address is reachable from all other pods in the cluster.

When there are say many application like postgress and run in local port then there is no way to track how many ports are assigned and the port that are there to use. Pods are abstraction and contains containers.  
When teh pods runs on the node it gets its own network namespace, virtual ethernet connection. Pod is teh host which has the IP address and the range of port to run container.

To create a pod it should be yml file. The sample code for the postgres.yml file.
```sql
apiVersion: v1
kind: Pod
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  containers:
  - name: postgres
    image: postgres:9.6.17
    ports:
    - containerPort: 5432
    env:
    - name: POSTGRES_PASSWORD
      value: "pwd"

```
containerPort - port where the application inside the container runs.

There can be many containers inside teh pod. say the scheduler or the backup container like the replica and the side car container. 

**How do the container connect to each other?**
![RedisClusterSharding.png](..%2Fimages%PodsPort.png)

Pods are running inside the network interface and the container run in the localhost.

Example - Multi container Pod with the nginx-container and the sidecar runs the curlimages.curl image print the line and sleep for 300 seconds.
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
  - name: sidecar
    image: curlimages/curl
    command: ["/bin/sh"]
    args: ["-c", "echo Hello from the sidecar container; sleep 300"]
```

The command to run and verify the port.

```yml

kubectl apply -f nginx-sidecar-container.yaml
kubectl get pod

NAME    READY   STATUS    RESTARTS   AGE
nginx   2/2     Running   0          74s

# Get inside the sidecar container.
kubectl exec -it nginx -c sidecar -- /bin/sh
  # netstat -ln
Active Internet connections (only servers)
Proto  Recv-Q  Send-Q  Local Address    Foreign Address  State
tcp    0       0       0.0.0.0:80      0.0.0.0:*        LISTEN

curl localhost:80

# Output will show message.


```
## Kubernetes Services.

### What is Kubernetes Service?

There are mainly 4 types of services - ClusterIp, Headless, NodePort, LoadBalancer, and ExternalName.

Each pod has its own IP address and pods are ephemeral and when they are removed new ip address is assigned. It doesn't make sense to use the poor IP address because when it did get a new IP address then you have to change.

With services the IP address would be stable. We put the services ahead of the pods.

Services are also used as loadbalancer - when here are 3 pod of services or mysql application. The service will get teh request targeted to the application and forward it to the pods.
Services promote loose coupling to communicate within the cluster component.

Cluster Ip - The default services and when a microservice application is deployed then there is a pos with the microservice container inside the pod. The sidecar container is there with the pod to collect the logs and send to the log server. 

The microservice container running in port 3000 and the log server is running in port 8000.


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice-one
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: microservice-one
    spec:
      containers:
      - name: ms-one
        image: my-private-repo/ms-one
        ports:
        - containerPort: 3000
      - name: log-collector
        image: my-private-repo/log-collector
        ports:
        - containerPort: 9000
```
The pod will get the ip address based on the node. There are 3 nodes then the nodes will get a range of the IP address and pod will get the IP address in the range.


```yml
kubectl get pod -o wide
# The output - 
NAME                                      READY   STATUS    RESTARTS   AGE   IP
mongodb-deployment-55577c4cbc-gkz8b       1/1     Running   0          29s   10.2.2.2
mongodb-deployment-55577c4cbc-z828l       1/1     Running   0          29s   10.2.1.4
mongodb-deployment-55577c4cbc-zv9h8       1/1     Running   0          29s   10.2.2.3
```

Example node 1 range is 10.2.2.x and the pod within the node 1 Ip address range will be in the range of the node.

The replication set to 2 meaning there are 2 pods with the application running say Node 1 and Node 2. The request is coming from the browser meaning the Ingress connection is set up. 

The request coming from the ingress coming to service the clusterIp service and the service will have a port and ip adcress and it will act as abstraction to the IP address.

```yml
#The ingress yaml.
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ms-one-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: microservice-one.com
    http:
      paths:
      - path: /
        backend:
          serviceName: microservice-one-service
          servicePort: 3200
```

The service name and the port is set and the ingress will set the request to the service in the IP say `10.128.8.44`
```yml

# The service-clusterIp.yaml
apiVersion: v1
kind: Service
metadata:
  name: microservice-one-service
spec:
  selector:
    app: microservice-one
  ports:
    - protocol: TCP
      port: 3200
      targetPort: 3000
```

**How the service knows which pod to forward the request? The replica is 2 then which to take the request.**

The service understand the member pod using the selector attribute. The yaml file has the selector attribute.

**How the selector knows like which port to forward the request**

The target port is defined in the yml file. The selector will get the name and the targetPort.  

When there is the service Kubernetes will create the Endpoint object and it will track which pod are the members of the service.

### The type of services.




