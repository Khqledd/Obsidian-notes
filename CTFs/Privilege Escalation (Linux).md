## <span style="color:rgb(255, 0, 247)">Enumeration commands</span>

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
- env: show environmental varianbles (the PATH variable may have a compiler or a scripting language that could be used to run code on target system)
- 