<span style="color:rgb(255, 0, 0)">Useful shortcuts</span>:
- `Ctrl+F`: Find packets (strings/hex/regex)
- `Ctrl+G`: Go to packet number
- `Ctrl+D`: Mark packets
- `Ctrl+E`: Export object (HTTP,SMB,FTP,etc..)
- `Ctrl+Alt+Shift+T`: Follow TCP Stream

<span style="color:rgb(255, 0, 0)">Useful display filters</span>:
- http  (http traffic only)
- tcp.port == 80
- ip.addr == 192.168.1.1
- ip.src / ip.dst
- http.request.method == "POST"
- frame contains "flag"                  # Search for strings in packets
- !(arp || dns || icmp)                     # cut noise
- dns.qry.name contains ".tk"       # Suspicious DNS

![[Pasted image 20260629183306.png]]

<span style="color:rgb(255, 0, 0)">Menu options</span>:
- Statistics -> Resolved addresses: IP Addresses and DNS names available in the capture file
- Statistics -> Endpoints: useful for filtering
- Edit -> Preferences: Enable IP address name resolution or GeoIP
