
## <span style="color:rgb(255, 0, 0)">Elastic Stack</span>

Searching - (field_name : value),
(Logical operators AND/OR/NOT ("value") )
 -Also remember to change the date

----------------------------
## <span style="color:rgb(255, 0, 0)">Splunk</span> 

<span style="color:rgb(247, 156, 156)">Splunk search examples:</span>
- Windows sysmon logs:
```
index=winenv EventCode=1 *powershell* AND *EncodedCommand*  
| table _time ComputerName ParentUser ParentImage ParentCommandLine Image CommandLine
```
