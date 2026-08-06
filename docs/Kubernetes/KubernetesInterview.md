### **What do you first verify if the pods looks like running but the users are seeing some errors ?**

There are mainly steps to follow in order.

**Pod Status**.

The first step is to separate the two ideas - Verify in case the container is up vs the Kubernetes is sending the traffic to the pod.

The first step is to see the readiness and a pod can be running but its not ready. The command like `kubectl get pods` and `kubectl describe pod <pod-name>` and look at the readiness probes in case failing then it should not receive traffic.

**Verify Service and Endpoints.**

Verify the service URL and it has healthy backend. Teh command like `kubectl get svc` and `kubectl get endpoints <service-name>`

When there is no endpoints then its mainly something like pod readiness failing, labels not matching the service selector or the workload deployed in different namespace.

**Inspect Events.**

The events explain thing in plain text like probe failure, failed mount or image pull issue. The command like `kubectl describe pod <pod-name>` and `kubectl get events --sort-by=.metadata.creationTimestamp` gives the idea of the issue.

**Pods logs.**

In case the endpoint exists and the error persist then verify the application logs like `kubectl logs <pod-name>` The common issue may be like dependency failures, Timeout errors, Misconfigured environment variables or Network policies blocking traffic.

**Application Level Debugging.**

When Kubernetes plumbing looks fine, investigate the app itself - Database connectivity, External API calls, Resource limits (CPU/memory), Network policies or firewalls.

<br>

### **What is the difference between readiness and liveliness of a Kubernetes pod ?**