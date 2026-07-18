In cmd project folder - mkdocs new project-name 
Push to git.

pip install mkdocs-material
pip install ghp-import
mkdocs gh-deploy

https://suchismita-deb.github.io/PlatformEngineering/

```shell
Remove any folder.
Add the idea in gitignore.
git rm -r --cached .idea
git commit -m "remove idea folder"
git push origin main
```

```shell
sdeb@DESKTOP-PUEA9OO:~$ sleep 300 & echo $!
[2] 973
973
sdeb@DESKTOP-PUEA9OO:~$ kill 893
sdeb@DESKTOP-PUEA9OO:~$ echo $!
973
[1]-  Terminated              sleep 300
sdeb@DESKTOP-PUEA9OO:~$ echo $!
973
[2]+  Done                    sleep 300
sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p 973
    PID    PPID STAT CMD
```
```shell

sdeb@DESKTOP-PUEA9OO:~$ sleep 300 & PID=$!
id,ppid,stat,cmd[4] 21853
 -p $Psdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID
    PID    PPID STAT CMD
  21853     458 S    sleep 300
sdeb@DESKTOP-PUEA9OO:~$ kill -STOP $PID
ps -o pi
[4]+  Stopped                 sleep 300
sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID     # should show STAT = T (stopped)
ONT $PID
ps -o pid,ppid,stat,cmd -p $PID     # back to S
kill $PID      PID    PPID STAT CMD
  21853     458 T    sleep 300
sdeb@DESKTOP-PUEA9OO:~$ kill -CONT $PID
sdeb@DESKTOP-PUEA9OO:~$ ps -o pid,ppid,stat,cmd -p $PID     # back to S
    PID    PPID STAT CMD
  21853     458 S    sleep 300
sdeb@DESKTOP-PUEA9OO:~$ kill $PID
sdeb@DESKTOP-PUEA9OO:~$
```
```shell
# 3. Send SIGSTOP, check state, then SIGCONT
kill -STOP <PID>
ps -o pid,ppid,stat,cmd -p <PID>   # should show T
kill -CONT <PID>
ps -o pid,ppid,stat,cmd -p <PID>   # back to S

# 4. Try SIGTERM vs SIGKILL — write a tiny script that traps SIGTERM
cat << 'EOF' > trap_test.sh
#!/bin/bash
trap 'echo "Got SIGTERM, cleaning up..."; sleep 2; exit 0' SIGTERM
echo "PID: $$"
while true; do sleep 1; done
EOF
chmod +x trap_test.sh
./trap_test.sh &
kill -TERM $!   # watch it print the cleanup message before exiting
```



```shell
sdeb@DESKTOP-PUEA9OO:~$ cat > trap_test.sh <<'EOF'
> #!/bin/bash
>
> trap 'echo "Got SIGTERM, cleaning up..."; sleep 2; exit 0' SIGTERM
>
> echo "PID: $$"
>
> while true; do
>     sleep 1
> done
> EOF
mod +x trap_test.sh
./trap_test.sh &[1]   Done                    sleep 300
[2]   Done                    sleep 300
sdeb@DESKTOP-PUEA9OO:~$
sdeb@DESKTOP-PUEA9OO:~$ chmod +x trap_test.sh
sdeb@DESKTOP-PUEA9OO:~$ ./trap_test.sh &
[5] 22765
sdeb@DESKTOP-PUEA9OO:~$ PID: 22765

sdeb@DESKTOP-PUEA9OO:~$
sdeb@DESKTOP-PUEA9OO:~$ ./trap_test.sh &
-l[1] 28143
sdeb@DESKTOP-PUEA9OO:~$ jobs -lPID: 28143

[1]+ 28143 Running                 ./trap_test.sh &
sdeb@DESKTOP-PUEA9OO:~$
sdeb@DESKTOP-PUEA9OO:~$ kill -9 28143
sdeb@DESKTOP-PUEA9OO:~$ jobs -l
[1]+ 28143 Killed                  ./trap_test.sh
sdeb@DESKTOP-PUEA9OO:~$
```