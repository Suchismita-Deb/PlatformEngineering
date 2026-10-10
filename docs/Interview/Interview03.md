### Kubernetes workload controllers

Kubernetes controllers continuously compare the desired state you declare with the actual state of your cluster and take action to reconcile differences.

Deployment - Stateless applications - APIs, web applications, microservices. Pods are interchangeable, and rolling updates are supported.

StatefulSet - Stable identity and storage - Databases, Kafka brokers, ZooKeeper-like systems, and clustered applications that need stable identities or storage associations.

DaemonSet Node-level agents - Log collectors, monitoring agents, node-level security agents, and networking components that must run on eligible nodes.

Job / CronJob - Finite tasks - Database migrations, batch processing, cleanup tasks, and scheduled backups.


The most important distinction.

Deployment - manages interchangeable application replicas.

StatefulSet - manages replicas that may require stable identities and persistent storage.

DaemonSet - manages a node-oriented workload, typically one Pod per eligible node.


**What is a StatefulSet?**

A StatefulSet is a Kubernetes workload controller used for applications that need stable Pod identities, predictable naming, stable network identities, or persistent storage associated with individual replicas.
![img.png](docs/images/Interview.Kubernetes/img.png)
For example, when database-1 is recreated, the replacement Pod normally keeps the name database-1 and refers to the same claim, rather than receiving a random Pod name and a different claim.

Key StatefulSet features

Stable Pod names: database-0, database-1, database-2.

Stable ordinal identity: each replica has a predictable index.

Stable network identity: commonly provided through a governing headless Service.

Persistent storage: volumeClaimTemplates can create a separate PVC for each replica.

Ordered deployment and scaling: the default behavior starts and stops replicas in ordinal order.

Controlled updates: rolling updates can respect ordinal ordering and readiness.

Identity survives Pod recreation: the Pod object may be replaced, but its ordinal-based identity remains predictable.

Important: a StatefulSet does not automatically replicate database data, elect a leader, or guarantee application consistency. Those are responsibilities of the application or database operator.



```java
class Solution {

    public int solution(int[] A) {
        long total = 0;

        // dp[k] = maximum reduction using exactly k moves
        long[] dp = {0, Long.MIN_VALUE / 2, Long.MIN_VALUE / 2};

        for (int x : A) {
            total += x;

            int y = digitSum(x);
            int z = digitSum(y);

            int d1 = x - y;
            int d2 = y - z;

            // Work backwards so that this element is not reused incorrectly
            dp[2] = Math.max(
                dp[2],
                Math.max(
                    dp[1] + d1,
                    dp[0] + d1 + d2
                )
            );

            dp[1] = Math.max(
                dp[1],
                dp[0] + d1
            );
        }

        // At most 2 moves, so take 0, 1, or 2 moves
        long maxReduction = Math.max(0, Math.max(dp[1], dp[2]));

        return (int)(total - maxReduction);
    }

    private int digitSum(int x) {
        int sum = 0;

        while (x > 0) {
            sum += x % 10;
            x /= 10;
        }

        return sum;
    }
}
```