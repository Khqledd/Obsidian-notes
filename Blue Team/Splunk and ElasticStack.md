
## <span style="color:rgb(255, 0, 0)">Elastic Stack</span>

`_index:....`
Searching - (field_name : value),
(Logical operators AND/OR/NOT ("value") )
 -Also remember to change the date

Example:
```
_index:weblogs and client.ip=203.0.113.55 and http.request.method: POST
```

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

- <span style="color:rgb(255, 149, 0)">Web logs:</span> 
Brute force detection example:
```
index=* method=POST uri_path="/wp-login.php"  
| bin _time span=5m  
| stats values(referer_domain) as referer_domain values(status) as status values(useragent) as UserAgent values(uri_path) as uri_path count by clientip _time  
| where count > 25  
| table referer_domain clientip UserAgent uri_path count status
```
Web shell detection example:
```
index=*  
| search status=200 AND uri_path IN(*.php, *.phtm, *.asp, *.aspx, *.jsp, *.exe) AND (method=POST OR method=GET)  
| stats values(status) as status values(useragent) as UserAgent values(method) as method  
  values(uri) as uri values(clientip) as clientip count by referer_domain  
| where count > 2  
| table referer_domain count method status clientip UserAgent uri
```
DDoS detection example:
```
index=* status=503  
| bin _time span=10m  
| stats values(referer_domain) as referer_domain values(status) as status values(useragent) as UserAgent values(uri_path) as uri_path count by clientip _time  
| where count > 100000  
| table _time referer_domain clientip UserAgent uri_path count status
```
