Shell Scripting Commands.

The commonly used shell commands.
ls, mv, cp, mkdir, touch, vim, grep.
route, trace - debug.

### Write a shell script to list all processes.

ps -ef

Sample output.
```bash
501 25134 25116 0 10:59AM ttys002 0:48.12 /var/folders/v6/8dw9y3q57x1247_vdg4812hm0000gn/T/go-
build1291420351/b001/exe/main

0 99646 25310 0 25Nov22 ttys002 0:00.04 login -fp aveerama
501 99647 99646 0 25Nov22 ttys002 0:00.05 -bash
501 99727 99647 0 25Nov22 ttys002 0:04.10 zsh

0 25311 25310 0 21Nov22 ttys003 0:00.05 login -fp aveerama
501 25313 25311 0 21Nov22 ttys003 0:00.02 -bash
501 25450 25313 0 21Nov22 ttys003 0:03.68 zsh
501 34123 25450 0 6:08PM ttys003 0:00.66 bash
0 36422 34123 0 6:58PM ttys003 0:00.00 ps -ef
```

It will give all the details and in case need the process id.

ps -ef | awk -F" " '{print $2}'
```bash
25311 
25313 
25450 
34123 
```
awk - filter the output in the specific line.

-F - the field separator here the column are separated with " " space.

$2 print the column 2 the pid.

### Write a script to print only the error from a remote log.

Get the logs in remote server using the curl command.
Print the error message.

curl google.com | grep "Error"

The google.com is the url meaning we can see the link in S3 bucket or gcs the log file.

The curl is returning the output and the grep is searching and the pipe is combining the output.

### Write the script to print the numbers divided by 3 or 5 and not 15.

vim samplescript.sh

#!/bin/bash
for i in {1..100}; dp
if ( [`expr $i % 3` == 0] || `expr $i % 5` == 0] ) && [`expr $i & 15` != 0] ;
then
echo $i
fi;
done

chmod 777 samplescript.sh

### Write the script to print number of "S" in Mississippi

#!/bin/bash
x=Mississipi
grep -o "s" <<< "$x" | wc -l

-o to get teh char
wc word count.

### How will you debug the shell script.
set -x
It will run in debug mode.


### The file to open in read mode.
vim -r test.txt

### What is the difference between soft link and gard link.

Create a file and save a file. It will save in the file and then copy of the file and modify the file and all then create the hard link to reuse the file and it will be copy of the file.

### What is the difference between  $* and $@ ?

```bash
#!/bin/bash

for arg in "$*"
do
  echo "$arg"
done

for arg in "$@"
do
  echo "$arg"
done
```
When we execute the file `./test.sh hello world` then `$*` will print hello world and `$@` will print
```
hello
world
```
The `$*` treat all arguments as a single string and `$@` treat each arguments separately.

### Comparison operator.

```bash
name = "John"
if ["$name"=="John"]
then  echo "Matched"
fi

### What is the exit status and $?
0 = Success
Non-zero = Failure

ls file.txt
echo $?

O/p - 0 or 2.
The output 2 when file not exists.


### What is the difference between > and >> ?
`echo "Hello"> output.txt`
The file will be `Hello`
`echo "World">output.txt`
The file content will be "World" and it replace the content.
`echo "World">>output.txt`
The file will be "Hello World"

### What is use of command grep, awk, sed.

grep - finds the text - `grep ERROR app.log`
awk - column processing - `awk '{print $1}` - print first column.  
sed - modify text - `sed 's/old/new/g` - modify old with new.

### How to read the file line by line?

```bash
while read line 
do 
	echo "$line"
done < file.txt
```

It is used in log processing, batch processing, data migration scripts.

### How to find a process and kill it?

`ps -ef | grep java` or `pgrep java`  
`kill PID` gracefully and `kill 9 PID` forcefully (it will not do clean up not use unless needed).

### What is the difference between $ and (`backticks`)?
The $ is the new command.

### Write a script to find the files larger than 100 MB?
```bash
#!/bin/bash
find /data -type f -size +100M
```

The advanced version.
```bash
#!/bin/bash
for file in $(find /data -type f -size +100M)
do
    echo "$file"
done
```

The most commonly used command - find, name, type, size, mtime, exec.

### Explain $0, $1, $2, $#.

### Write a retry script.
```bash
#!/bin/bash
MAX_RETRY=5
count=0

while [ $count -lt $MAX_RETRY ]
do
    curl -f https://my-api.com
    if [ $? -eq 0 ]
    then
        echo "Success"
        exit 0
    fi
    count=$((count+1))
    echo "Retry $count"
    sleep 5
done

echo "Failed"
exit 1
```

### Write a script to find the largest file in the server.
```bash
find / -type f -exec du -h {} + 2>/dev/null | sort -hr | head -10
```
The better version.
```bash
#!/bin/bash
find /data -type f -exec ls -lh {} + | sort -k5 -hr | head -10
```
The command are find, sort, head, du.


### Script to see the error.
`cat app.log | grep ERROR | wc l`  
`grep -c ERROR app.log`

### Write the script to process the highest consuming CPU.

`ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head`

See the logs live - `tail -f application.log`   
See the last 100 lines - `tail -100 app.log`

### Script to retry untill success In kubernetes use case like wait for the kafka or service.

```bash
while true
do
  curl http://service/health

  if [ $? -eq 0 ]
  then
     echo "Healthy"
     break
  fi

  sleep 5
done
```

### Script to see the health is up.

```bash
#!/bin/bash

status=$(curl -o /dev/null -s -w "%{http_code}" http://localhost:8080)

if [ "$status" -eq 200 ]
then
   echo "UP"
else
   echo "DOWN"
fi
```

### Read all files.
In production read all files, upload all files to GCS, move backup files.
```bash
for file in *.txt
	do 
		echo "$file"
	done
```


### Parse the CSV.
data.csv
```
name,age
John, 20
Alex, 30
```
```bash
while IFS=',' read name age
do
   echo "$name -> $age"
done < data.csv
```
**Get the name** - `awk '{print $1}'`
### Read the env variable.
```bash
echo $HOME
echo $JAVA_HOME
echo $PATH
```

### See in case a file exists.
```bash
if [ -f app.properties ]
then
   echo "Found"
else
   echo "Missing"
fi
```

The command - `-f file, -d directory, -r read, -w wite, - -x execute`.
### Script to get the files older than 30 days.
`find /backup -mtime +30`   
`find /back up -mtime +30 -delete` - To delete the old backups.

### The frequency of the word.
`apple, banana, apple, orange, banana, apple`  
`sort fruits.txt | uniq -c`   
The output - 3 apple, 2 banana, 1 orange.

Debug shellscript - `set -x` - Enable debug.  
Exit on error - `set -e`   
See the undedfined variable - `set -u`

The best way - `set -eux`

### Script to do the log monitoring.
```bash
tail -f app.log | while read line
do
   echo "$line" | grep ERROR

   if [ $? -eq 0 ]
   then
      echo "ALERT"
   fi
done
```
When error then send alert.


### Script that calls a heakth endpoint, retry 5 times, log timestamps, saves outputto file, exits with 1 on failure.

```bash
#!/bin/bash
LOG=health.log
for i in {1..5}
do
   echo "$(date) Attempt $i" >> $LOG
   curl -f http://localhost:8080/actuator/health >> $LOG
   if [ $? -eq 0 ]
   then
      echo "SUCCESS"
      exit 0
   fi
   sleep 5
done
echo "FAILED"
exit 1
```



