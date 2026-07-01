Data exfiltration through DNS Tunneling:
     Indicators of attack:
     - Many DNS queries are sent to a single external domain, especially with very high counts compared to the baseline.
- Long subdomain labels or unusually long full query names (> 60–100 characters).
- High entropy or Base32/Base64-like patterns in the query name (lots of mixed case letters, digits, `-`, `=` signs for base64).
- Rare record types (TXT, NULL) or many large TXT responses.
- Unusual response behavior: frequent NXDOMAIN (if attacker uses exfil-by-query without answering), or TCP/large UDP fragments for DNS.
- Queries at regular intervals (beaconing behaviour).