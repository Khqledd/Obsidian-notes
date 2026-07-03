<span style="color:rgb(255, 0, 0)">Useful shortcuts</span>:
- `Ctrl+F`: Find packets (strings/hex/regex)
- `Ctrl+G`: Go to packet number
- `Ctrl+D`: Mark packets
- `Ctrl+E`: Export object (HTTP,SMB,FTP,etc..)
- `Ctrl+Alt+Shift+T`: Follow TCP Stream

<span style="color:rgb(255, 0, 0)">Useful display filters</span>:
- http  (http traffic only)
- tcp.port == 80
- ip.addr == 192.168.1.1 (eth.addr for MAC)
- ip.src / ip.dst
- http.request.method == "POST"
- frame contains "flag"                  # Search for strings in packets
- !(arp || dns || icmp)                     # cut noise
- http.host matches "`\`.(php|html)"   # Using regular expressions to find pages
- dns.qry.name contains ".tk"       # Suspicious DNS

![[Pasted image 20260629183306.png]]

<span style="color:rgb(255, 0, 0)">Menu options</span>:
- Statistics -> Resolved addresses: IP Addresses and DNS names available in the capture file
- Statistics -> Endpoints: useful for filtering
- Edit -> Preferences: Enable IP address name resolution or GeoIP
- Tools -> Credentials: view clear text credentials of certain protocols


<span style="color:rgb(232, 150, 150)">About nmap scans:</span>
-`icmp.type==3 and icmp.code==3`: UDP close port

-`tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024`: TCP SYN scan patterns

-`tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`: TCP Connect scan pattern

<span style="color:rgb(232, 150, 150)">ARP:</span> 
- ARP Request: `arp.opcode == 1`
- ARP Response: `arp.opcode == 2`
- ARP Poisioning Detection: `arp.duplicate-address-detected`

<span style="color:rgb(232, 150, 150)">DHCP:</span>
- DHCP Request: `dhcp.option.dhcp == 3`
- DHCP ACK: `dhcp.option.dhcp == 5`
- DHCP NAK: `dhcp.option.dhcp == 6`
- Hostname in request, option 12: `dhcp.option.hostname contains "keyword"`
- Domain in ACK, option 15: `dhcp.option.domain_name contains "keyword"`

<span style="color:rgb(232, 150, 150)">NetBIOS (nbns):</span> 
- `nbns.name contains "keyword"`

<span style="color:rgb(232, 150, 150)">Kerberos:</span>
- User account search: `kerberos.CNameString contains "keyword"` 
(the values ending with $ are hostnames so could filter them out using:
`&& !(kerberos.CNameString contains "$")`

<span style="color:rgb(232, 150, 150)">ICMP:</span>
- Detect ICMP tunneling: `icmp && data.len > 64`

<span style="color:rgb(232, 150, 150)">DNS:</span>
- Detect tunneling and C2: `dns contains "dnscat"` 
   or (`dns.qry.name.len > 15 and !mdns`)

<span style="color:rgb(232, 150, 150)">FTP:</span>
- x1x/x2x/x3x options: `ftp.response.code == ...`
- Brute force signal to list failed attempts: `ftp.response.code == 530`
- Follow TCP stream very helpful to see commands used in ftp

<span style="color:rgb(232, 150, 150)">HTTP:</span>
- `http.request.method == POST/GET`
- `http.response.code == 302` (for successful login)
- `http.resonse.code == 200` (for successful request)
- `http.connection == "Keep-Alive"`
- `http.request.uri contains “.php”` (search for a web shell)
- `http.user_agent` (useful for detecting anomalies, could contain a value like wfuzz or sqlmap or nmap or nikto or misspelled mozilla)

<span style="color:rgb(232, 150, 150)">HTTPS:</span>
- TLS client request: `tls.handshake.type == 1`
- TLS server response: `tls.handshake.type == 2`
- `SSDP` is a network protocol that provides advertisement and discovery of network services, we use !(ssdp) with the first 2 commands to find the "Client hello" or "Server hello" to spot which ip addresses are involved in handshake
- `tls.handshake.extensions_server_name == accounts.google.com`: frames sent to accounts.google.com