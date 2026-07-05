<span style="color:rgb(228, 73, 223)"><b>Logs in linux</b></span>: can be found in `/var/log/...` (depends on the type of log)
<span style="color:rgb(228, 73, 223)"><b>Snort Logs</b>:</span> `/var/log/snort`
<span style="color:rgb(228, 73, 223)"><b>Logs in windows</b></span>:`C:\Windows\System32\winevt\Logs`
<span style="color:rgb(228, 73, 223)"><b>Logs in powershell</b></span>: `C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

![[Pasted image 20260703190728.png]]

---------------------------

**Viewing Logs:**
- <span style="color:rgb(0, 176, 240)">Windows</span>: `Win+R -> "eventvwr"` (we can apply filters to log list through Find.., Security log Event ID 4625 = failed logon attempt, 4624 = successful logon) 
    Event ID meaning: https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/
    Logon ID can be used for correlating events together (keep it noted)
![[Pasted image 20260703195002.png]]

Persistence indicators in windows security logs:
(Subject is the account doing action, New account/member is the target)
![[Pasted image 20260703200921.png]]

- <span style="color:rgb(0, 176, 240)">Sysmon logs in windows:</span> Contains additional info about processes and binary. In the event viewer, (`Applications & Services -> Microsoft -> Windows -> Sysmon -> Operational`)
     Event ID 1: Process creation / Event ID 15: check host URL
     Event ID 11: File creation / Event ID 13: registry value set
     Event ID 3: Network connection / Event ID 22: DNS query
     https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon


- <span style="color:rgb(0, 176, 240)">Linux logs:</span> /var/log/...
     `/var/log/auth.log`: stores user management events, format:
         Time - Host name - PID - Message from the process 
         -grep -E  "session opened|session closed": login/logout events, cron jobs and sudo
         -grep "sshd" | grep -E "Accepted|Failed": ssh daemon stores logs here too in different format
         -more examples![[Pasted image 20260705030416.png]]

     `var/log/kern.log`: kernel messages and errors
     `/home/ubuntu/.bash_history`: per user, commands history (not so useful)
