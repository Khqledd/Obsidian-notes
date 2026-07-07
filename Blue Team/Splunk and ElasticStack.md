
## <span style="color:rgb(255, 0, 0)">Elastic Stack</span>

Searching - (field_name : value),
(Logical operators AND/OR/NOT ("value") )
 -Also remember to change the date

----------------------------
## <span style="color:rgb(255, 0, 0)">Splunk</span> 

<span style="color:rgb(247, 156, 156)">Splunk search examples:</span>
- <span style="color:rgb(255, 149, 0)">Windows sysmon logs:</span>
```
index=winenv EventCode=1 *powershell* AND *EncodedCommand*  
| table _time ComputerName ParentUser ParentImage ParentCommandLine Image CommandLine
```

- <span style="color:rgb(255, 149, 0)">Linux logs:</span>
```
index=linux source="auth.log" *ubuntu* process=sshd   
| search "Accepted password" OR "Failed password"
```
 
(can replace auth.log with syslog to search for persistence through services or cron jobs)
```
index=linux sourcetype=syslog ("CRON" OR "cron")  
|  search ("python" OR "perl" OR "ruby" OR ".sh" OR "bash" OR "nc")
```