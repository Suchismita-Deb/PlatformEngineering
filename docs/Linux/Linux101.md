Shell Scripting.

Automation 

Linux on GCP or in the laptop. Things we can automate in the machine multiplecation 1 to 100 then 1000 need system and we will automate the system. The shell scripting is automating the day to day work in the linux system.

The GCP then the VM and there we will make the file and write the shell scripts. 
In linux create the file - `touch first-shell-script.sh`.  
To get the file permission - `ls -l first-shell-script.sh`.  
To get the file - `ls`.  
To get the file in the order of time - `ls -ltr`.  
In case forgot the command details then `man ls` or `ls --help` will show the manual of the command.

To write something in the file we need to open the file `vim` need to install the command `vi` is there in the linux system.
The next step to open the file `vim first-shell-script.sh` and then press `esc` then `i` to go to insert mode and write the script.
In case the file not present and write the `vim first-shell-script-1.sh` then it will create the file and open the file in insert mode. In that case why we need touch when the vim command is making the file and inserting the data.  


The touch command is used in the automation - there is a need to create 1000 files then in the command need to use touch and vim will open all and the system will crash.

To insert anything in the shell script the first is the shebang `#!/` the code like `#!/bin/` then `bash, dash, sh, ksh` the bash is the executables of the shell script.
They execute the script command.
Initially the bin/sh was internally link to bin/bash and in recent ubuntu started using the bin/sh to bin/dash. 

The script file when executed should print my name.
```bash
#!/usr/bin/bash
# Print the text.
echo "Hello world."
```
  
Then save the file `esc` then `:wq` to save and quit the file. In case not save the file content then `:q!` to quit the file without saving the content.  
To execute the file `bash first-shell-script.sh` or `sh first-shell-script.sh` or `./first-shell-script.sh`.

When executed the file it will show the access denied then it needs the access permission to execute the file. To give the permission `chmod +x first-shell-script.sh` then execute the file `./first-shell-script.sh`.    

The chmod grant permission to the file.   
The `+x` means give the execute permission to the file. The `-x` means remove the execute permission to the file.   
The `+r` means give the read permission to the file. The `-r` means remove the read permission to the file.   
The `+w` means give the write permission to the file. The `-w` means remove the write permission to the file.

The chmod gives the permission to the group, to the root (admin) and to the user.

To give permission to the user `chmod u+x first-shell-script.sh`.    
To give permission to the group `chmod g+x first-shell-script.sh`.  
To give permission to root `chmod 777 first-shell-script.sh`.
`777` - 7 for my permission, 7 for group permission, 7 for root permission.  
`777` - 4+2+1 = 7 (read + write + execute)  
`755` - 4+2+1 = 7 (read + write + execute) for my permission, 4+1 = 5 (read + execute) for group permission, 4+1 = 5 (read + execute) for root permission.  
`644` - 4+2 = 6 (read + write) for my permission, 4 = 4 (read) for group permission, 4 = 4 (read) for root permission.

The linux uses the 4 2 1 command meaning 4 to read, 2 to write and 1 to execute. The permission is given in the order of user, group and everyone root.

What is the purpose of Shell scripting?

The application has 10k virtual machines and there are times when the CPU, memory of the machine is down or used more than 80%. To login to the VMs and see the stat is not ideal so the solution is to make a shell script and when executed it will go inside the VM and see the CPU, memory and disk usage.

The script run every 5 hours and send the stat in email.