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
--------------------------------------------
## **Tshark:**
- <span style="color:rgb(247, 156, 156)">Tools used with Tshark to filter:</span>
     <span style="color:rgb(255, 149, 0)">capinfos</span>: provides details of a capture file (Hashes / size / no. of packets)
     <span style="color:rgb(255, 149, 0)">grep</span>: search plaintext data
     <span style="color:rgb(255, 149, 0)">cut</span>: cut parts of lines from  a specified data source
     <span style="color:rgb(255, 149, 0)">uniq</span>: filter repeated lines/values
     <span style="color:rgb(255, 149, 0)">| nl</span>: view the number of shown lines
     <span style="color:rgb(255, 149, 0)">sed</span>: a stream editor
     <span style="color:rgb(255, 149, 0)">awk</span>: scripting language that helps pattern search and processing

- <span style="color:rgb(247, 156, 156)">Main parameters:</span> 
     <span style="color:rgb(255, 149, 0)">-r:</span> read a capture file, `tshark -r demo.pcapng`
     <span style="color:rgb(255, 149, 0)">-c:</span> stop after capturing x packets, `tshark -c 10`
     <span style="color:rgb(255, 149, 0)">-x:</span> display packet bytes (details in hex and ASCII)
     <span style="color:rgb(255, 149, 0)">-w:</span> output file, `tshark -r demo.pcapng -c 1 -w write-demo.pcap`
     <span style="color:rgb(255, 149, 0)">-Y:</span> display filters like in wireshark, `tshark -r capture.pcap -Y "http.request.method==POST"`
     <span style="color:rgb(255, 149, 0)">-T fields -e <i>fieldname</i></span>: extract a field, `tshark -r dns.cap -Y "dns.qry.type == 1" -T fields -e dns.qry.name` (return the dns query name of the ones with record A)

Example to extract the subdomains of every dns packet and make them together into 1 string:
`tshark -r demo.pcap -T fields -e "dns.qry.name" | uniq | cut -d'.' -f1 | paste -sd' ' | tr -d ' '`
     `uniq`: remove duplicate lines
     `cut -d'.' -f1`: remove after the first dot and show the first column only
     `paste -sd' '`: connect all rows together in 1 line with space between them
     `tr -d ' '`: remove the space between the strings to form 1 connect string line (or: `tr -d '\\n'` without paste)