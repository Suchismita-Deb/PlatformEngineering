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



GKE standard cluster, GKE autopilot cluster

standard cluster - It will be both public and private cluster. When using the cluster we need the compute engine like the storage snapshots, storage image and the compute engine.

GKE public cluster then private cluster. In private cluster we have the VPC network peering, private connectivity and pull docker images.

GKE Cluster Mode - It is of two types - GKE standard and GKE autopilot.

The clusters will be in different cluster types.  
GKE zonal Cluster or GKE Regional Cluster.  
GKE Public Custer or GKE Private Cluster.  
GKE Alpha Cluster or GKE Cluster using Node Pools.

