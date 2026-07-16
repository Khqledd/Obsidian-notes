 Also remember to change the date for both
## <span style="color:rgb(255, 0, 0)">Elastic Stack</span>

<span style="color:rgb(237, 115, 115)">Searching:</span>
    `_index:....`
    -field_name : value
    -Logical operators AND/OR/NOT ("value") )
    -We can use regular expressions by changing KQL to Lucene, wrap the expression in slashes `/EVenis/`
    -We can filter by ranges in values or timestamps: `@timestamp >= "2023-01-01" AND @timestamp < "2023-03-01"` :between Jan and March
    -In Lucene, `host_name: server01~1`: ~1 means that one character difference will return too like `serber01` (for misspelling purposes)
    -In Lucene, proximity searching like: `log_message: "server error"~1`: would also return if there is 1 word between server and error like in                   `Server: Detected Error connections`

<span style="color:rgb(237, 115, 115)">Tips:</span>
    - We can use wildcards * here instead of 'contains', like in the example below with uri.path
    - **View surrounding documents** is VERY helpful to see events before and after the one selected
    - Sorting from oldest to newest timestamp is also helpful
    - Special characters need a backslash like `\+` unless they are wrapped in quotation marks (`"User \"bob\" Created"`)

<span style="color:rgb(237, 115, 115)">Nested values:</span>
     Sometimes fields can contain structured data objects instead of simple text, 
     Example: `comments field -> [{author: Alice, text: Mitigated DDoS attack}, {author: Bob, text: Checked logs}]` (JSON format)
     - In this case we filter like: 
      1-   `comments.author : "Alice"`
      2-  `comments.author: ("Alice" AND "Bob") AND comments.text: "attack"`

Example:
```
_index:weblogs and client.ip=203.0.113.55 and http.request.method:POST and uri.path= *cmd=*
```

<span style="color:rgb(255, 149, 0)">For events after the given timestamp and this Windows log event ID:</span>
`@timestamp >= "2025-07-20T05:11:22" and winlog.event_id:1` (event.code to filter sysmon logs)

<span style="color:rgb(255, 149, 0)">Other useful fields:</span>
`process.command_line / process.name / process.parent.name`

<span style="color:rgb(255, 149, 0)">Powershell logs example:</span>
![[Pasted image 20260708175132.png]]

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

Username that is not Katrina or James or Moin
`index=win_eventlogs schtasks AND username NOT (Moin OR Katrina OR James)`