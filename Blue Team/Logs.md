<span style="color:rgb(228, 73, 223)"><b>Logs in linux</b></span>: can be found in `/var/log/...` (depends on the type of log)
<span style="color:rgb(228, 73, 223)"><b>Snort Logs</b>:</span> `/var/log/snort`
<span style="color:rgb(228, 73, 223)"><b>Logs in windows</b></span>:`C:\Windows\System32\winevt\Logs`

![[Pasted image 20260703190728.png]]

---------------------------

**Viewing Logs:**
- <span style="color:rgb(0, 176, 240)">Windows</span>: `Win+R -> "eventvwr"` (we can apply filters to log list through Find.., Security log Event ID 4625 = failed logon attempt, 4624 = successful logon) 
    Event ID meaning: https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/
![[Pasted image 20260703195002.png]]

Persistence indicators in windows security logs:
(Subject is the account doing action, New account/member is the target)
![[Pasted image 20260703200921.png]]