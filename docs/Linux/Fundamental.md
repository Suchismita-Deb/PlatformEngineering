Linux understanding will guide us - **The CPU issue** and **why the service wont start**.

The first part to **understand the process and signal**.

Real life cases - `CrashLoopBackOff` in the Kubernetes pod in an incident.

<details class="quiz-toggle">
<summary>CrashLoopBackOff</summary>
<b>CrashLoopBackOff</b> in Kubernetes means a container inside a Pod is repeatedly crashing and restarting with exponential delays. It’s not the root error itself but a symptom that something inside the container is failing — next step to check logs and events to debug.
<br><br>
<b>Crash</b> → It means the container exits unexpectedly (non‑zero exit code, OOMKilled, segfault, etc.)<br>
<b>Loop</b> → Kubernetes restarts it again and again.<br>
<b>BackOff</b> → Each restart is delayed longer (10s → 20s → 40s → … → max 5 minutes).<br>
<b>Pod Status</b> → Shows as CrashLoopBackOff in kubectl get pods.
<br><br>
The common cause like <b>Application error</b> - wrong config or env variables, <b>OOMKilled</b> - container exceeding memory limit, <b>Missing secrets or configs</b>, <b>Dependency missing</b> like db, api or service not reachable.
</details>

The fundamental to understand teh process and signal at the OS level to understand Kubernetes at depth.
## Process.
A process is a running instance of a program - It has a PID, its own virtual memory space, open file descriptors, a parent, and a state (running R, sleeping S (okay the issue when too many then it will be a bottleneck), Uninterruptible sleep D (danger — usually stuck disk I/O or NFS hang; can't even be killed with -9), zombie Z (child died, parent hasn't wait()ed — leaks PIDs), stopped T (someone sent SIGSTOP, or a debugger attached)).

- `ps aux` — snapshot of all processes
- `ps -ef --forest` — shows parent/child tree (useful for "who spawned this?")
## Signal.
A signal is a software interrupt sent to a process. The process can catch it, ignore it, or die from it — depending on the signal and whether it's catchable.

| Signal  | Number | Default action | Catchable? | Real use                                      |
|---------|--------|----------------|------------|-----------------------------------------------|
| SIGHUP  | 1      | terminate      | yes        | "reload config" convention (nginx, many daemons) |
| SIGINT  | 2      | terminate      | yes        | Ctrl+C                                        |
| SIGKILL | 9      | terminate      | no         | force kill — OS just erases it, no cleanup    |
| SIGTERM | 15     | terminate      | yes        | the "polite" shutdown request — default for kill, and what Docker/K8s send first |
| SIGSTOP | 19     | stop           | no         | pause process                                 |
| SIGCONT | 18     | continue       | yes        | resume it                                     |

When Kubernetes terminates a pod, it sends **SIGTERM**, waits `terminationGracePeriodSeconds` (default 30s), then sends **SIGKILL** if the process hasn't exited. 

If your app doesn't have a **SIGTERM** handler, it never gets a chance to drain connections, flush buffers, or close DB pools gracefully — it just gets hard-killed on a timer. This is one of the most common causes of "random" data corruption / dropped requests during rolling deploys, and most engineers never trace it back to this.

```shell

# 1. Start a background process (sleep 300) and capture its PID
sdeb@DESKTOP-PUEA9OO:~$ sleep 300 & PID=$!
[1] 21853

# 2. Check its state
sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID
    PID   PPID STAT CMD
  21853    458 S    sleep 300

# 3. Send SIGSTOP, check state, then SIGCONT
sdeb@DESKTOP-PUEA9OO:~$ kill -STOP $PID
[1]+  Stopped                 sleep 300

sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID   # should show T
    PID   PPID STAT CMD
  21853    458 T    sleep 300

sdeb@DESKTOP-PUEA9OO:~$ kill -CONT $PID
sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID   # back to S
    PID   PPID STAT CMD
  21853    458 S    sleep 300

# Finally, terminate the process
sdeb@DESKTOP-PUEA9OO:~$ kill $PID
[1]+  Terminated              sleep 300


# 4. Try SIGTERM vs SIGKILL — write a tiny script that traps SIGTERM
sdeb@DESKTOP-PUEA9OO:~$ cat > trap_test.sh <<'EOF'
#!/bin/bash

trap 'echo "Got SIGTERM, cleaning up..."; sleep 2; exit 0' SIGTERM

echo "PID: $$"

while true; do
    sleep 1
done
EOF

# Make it executable
sdeb@DESKTOP-PUEA9OO:~$ chmod +x trap_test.sh

# Run in background
sdeb@DESKTOP-PUEA9OO:~$ ./trap_test.sh &
[1] 22765
PID: 22765

# Start another instance
sdeb@DESKTOP-PUEA9OO:~$ ./trap_test.sh &
[2] 28143
PID: 28143

# Check jobs
sdeb@DESKTOP-PUEA9OO:~$ jobs -l
[1] 22765 Running                 ./trap_test.sh &
[2]+ 28143 Running                 ./trap_test.sh &

# Send SIGTERM to one instance (graceful shutdown)
sdeb@DESKTOP-PUEA9OO:~$ kill -TERM 22765
Got SIGTERM, cleaning up...
# (script sleeps 2 seconds, then exits cleanly)

# Send SIGKILL to another instance (force kill, no cleanup)
sdeb@DESKTOP-PUEA9OO:~$ kill -9 28143

# Verify job status
sdeb@DESKTOP-PUEA9OO:~$ jobs -l
[1] 22765 Terminated              ./trap_test.sh
[2]+ 28143 Killed                  ./trap_test.sh

```
SIGSTOP pauses a process (STAT=T).  
SIGCONT resumes it (STAT=S).  
SIGTERM allows cleanup (trap handlers run).  
SIGKILL kills instantly, no cleanup possible.  
jobs -l shows background jobs with PID and status.

The command `kill -9 $!` notice dies instantly with no clean up message. The difference is the whole reason graceful shutdown handlers exist in the production services.

<br>
### Scenario - You deploy a new version of a service to Kubernetes. During the rollout, you notice a spike in 502 errors from the load balancer for about 5 seconds per pod restart, even though the app has a SIGTERM handler that closes connections cleanly.

The 502s here happen on shutdown of the old pod — even though it has a clean SIGTERM handler - The point is the app shuts down cleanly, why does the load balancer still send it traffic during those 5 seconds?

The real mechanism - Kubernetes pod termination and load-balancer traffic routing are **two separate systems that don't talk to each other synchronously** -

Kubernetes decides to kill the pod → sends SIGTERM immediately.  
**In parallel**, it tells the Endpoints controller to remove the pod's IP from the Service's endpoint list.  
That endpoint removal has to propagate to kube-proxy (or your ingress/LB) on every node.  
This propagation is **not instant** — it can take a few seconds.

Those few seconds, the pod is shutting down (or already dead) but the load balancer doesn't know yet and keeps sending it traffic → connection refused → 502.

The SIGTERM handler closing connections gracefully doesn't help here — the problem isn't the app being rude, it's the LB having stale routing information. This is a race condition between two independent control loops, not an app-code bug.


Fix.

**`preStop` hook**: add `sleep 5` (or similar) in `preStop` — this delays the actual shutdown just long enough for the endpoint removal to propagate everywhere, while the app *keeps serving* during that window.

**Readiness probe**: confirm the pod is marked `NotReady` immediately on SIGTERM (some apps need to fail readiness checks proactively during shutdown, not just close connections).

**`terminationGracePeriodSeconds`**: make sure it's long enough to cover `preStop` sleep + actual graceful shutdown time.

SIGTERM handling is about the app being polite. The `preStop + readiness` is about the cluster being told in time. They solve different halves of the problem.