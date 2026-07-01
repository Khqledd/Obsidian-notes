<span style="color:rgb(232, 150, 150)">Data exfiltration through DNS Tunneling:<br>     Indicators of attack:</span>
- Many DNS queries are sent to a single external domain, especially with very high counts compared to the baseline.
- Long subdomain labels or unusually long full query names (> 60–100 characters). filter in splunk: `| where len(query) > 30`
- High entropy or Base32/Base64-like patterns in the query name (lots of mixed case letters, digits, `-`, `=` signs for base64).
- Rare record types (TXT, NULL) or many large TXT responses.
- Unusual response behavior: frequent NXDOMAIN (if attacker uses exfil-by-query without answering), or TCP/large UDP fragments for DNS.
- Queries at regular intervals (beaconing behaviour).

**Wireshark:**
    -Filter DNS queries with no response: `dns.flags.response == 0
    `
    -Find long DNS queries: `dns && frame.len > 70`
    
    -If the domain (khaled) has long suspicious subdomain (for example hksdfkhsdfh.khaled.net): `dns.qry.name contains "khaled"`

--------------------------------------

<span style="color:rgb(232, 150, 150)">Data exfiltration through FTP:<br>Indicators of attack:</span> 
- `USER` and `PASS` commands (cleartext credentials).
- `STOR` (upload) and `RETR` (download) commands: repeated or large transfers.
- Large data connections to unusual external IPs, especially outside business hours.
- Data channel openings on ephemeral ports (PASV) paired with large payloads.

**Wireshark:**
     -Look for credentials: `ftp.request.command == "USER" || ftp.request.command == "PASS"`
     -Look for anomalies in filenames or credentials: `ftp contains "STOR"`, follow TCP stream for more info about the user,pass,stor
     -Look for suspicious files: `ftp contains "csv"` (csv/pdf/txt etc..)
     -Look for traffic with large payload: `ftp && frame.len > 90`

---------------------------
<span style="color:rgb(232, 150, 150)">Data exflitration through HTTP:</span>
- Unusually large HTTP POST requests to external/unexpected hosts.
- GET requests with encoded data. (can filter in splunk using method="GET")
- HTTP requests to domains with low reputation / rarely seen in baseline traffic.
- Frequent small requests (beaconing) to the same host, followed by large uploads.
- Chunked or multipart transfers where multiple requests compose a larger file.

**Wireshark:**
     Large requests: `http.request.method == "POST" and frame.len > 500`
     Follow HTTP stream for more info

-------------------
<span style="color:rgb(232, 150, 150)">Data exfiltration throught ICMP:</span>
- 