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
-  