<span style="color:rgb(232, 150, 150)">Data exfiltration through DNS Tunneling:<br>     Indicators of attack:</span>
- Many DNS queries are sent to a single external domain, especially with very high counts compared to the baseline.
- Long subdomain labels or unusually long full query names (> 60–100 characters). filter using `frame.len > 70`
- High entropy or Base32/Base64-like patterns in the query name (lots of mixed case letters, digits, `-`, `=` signs for base64).
- Rare record types (TXT, NULL) or many large TXT responses.
- Unusual response behavior: frequent NXDOMAIN (if attacker uses exfil-by-query without answering), or TCP/large UDP fragments for DNS.
- Queries at regular intervals (beaconing behaviour).

**Wireshark:**
    -Filter DNS queries with no response: `dns.flags.response == 0`
    -Find long DNS queries: `dns && frame.len > 70`
    -If the domain (khaled) has long suspicious subdomain (for example sdfkjhsdfhksdfkhsdfh.khaled.net): `dns.qry.name contains "khaled"`