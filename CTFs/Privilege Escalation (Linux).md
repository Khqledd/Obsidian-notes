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
- <span style="color:rgb(166, 127, 173)">dpkg -l</span>: all installed packages and their version

**USER ENUMERATION:-**
- <span style="color:rgb(166, 127, 173)">id</span>: overview of the user privileges and group memberships, can specify other users too like `id khaled`
- <span style="color:rgb(166, 127, 173)">env</span>: show environmental varianbles (the PATH variable may have a compiler or a scripting language that could be used to run code on target system)
- <span style="color:rgb(166, 127, 173)">history</span>: check earlier commands, it is per-user
- <span style="color:rgb(166, 127, 173)">sudo -l</span>: the target system may be configured to allow users to run some (or all) commands with root privileges. this command can be used to list all commands your user can run using sudo, its used to check for possible bins to escalate using GTFObins (example in Useful tips)
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

<span style="color:rgb(172, 57, 163)"><b>Kernel exploits:</b></span>
- Find a CVE based on the kernel version found in `uname -a`
- Example: Linux kernel version 3.13.0-24-generic ---> CVE-2015-1328
- 