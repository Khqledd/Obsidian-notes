 
<span style="color:rgb(0, 176, 80)">Common Types of Attacks:</span>
- Packet sniffing: Capturing unencrypted data packets exchanged over a network, often on open Wi-Fi.
- Session hijacking: Stealing and using session tokens to impersonate users.
- SSL stripping: Downgrading HTTPS connections to insecure HTTP to steal or alter data transferred.
- DNS spoofing: Redirecting legitimate website traffic to fraudulent domains by manipulating DNS responses.
- IP spoofing: Crafting malicious IP packets that appear to come from trusted systems.
- Rogue Wi-Fi access point: Creating fake networks to intercept user traffic.


<span style="color:rgb(146, 208, 80)">ARP Spoofing:</span> 
<span style="color:rgb(146, 208, 80)">Indicators of attack:</span> 
- **Duplicate MAC-to-IP Mappings**: Multiple MAC addresses claiming the same IP address. Indicates impersonation.
- **Unsolicited ARP Replies**: High number of ARP replies without matching requests ("gratuitous ARP").
- **Abnormal ARP Traffic Volume:** A Large number of ARP packets in short intervals.
- **Unusual Traffic Routing**: Traffic rerouted through the attacker’s MAC.
- **Gateway Redirection Patterns:** Multiple destination MACs for the same gateway IP.
- **ARP Probe / Reply Loops**: Many ARP requests with `Who has 192.168.1.x? Tell 192.168.1.y` patterns.

*Wireshark:*
     -ARP Request (who has): `arp.opcode == 1`
     -ARP Response (is  at): `arp.opcode == 2`
     -Host sending many unsolicited gratuitous ARP replies: `arp.isgratuitous`
     -`arp.src.proto_ipv4 == 192.168.10.1`
     -`eth.src == 02:aa:bb:cc:00:01`
     -`_ws.col.info contains "192.168.10.1 is at"`: filter based on column (info) containing the " .... "
     -Check for duplicate IP-to-MAC mapping: `arp.duplicate-address-detected || arp.duplicate-address-frame`

------------------------
<span style="color:rgb(146, 208, 80)">DNS Spoofing:</span>
<span style="color:rgb(146, 208, 80)">Indicators of attack:</span> 
- **Multiple DNS responses for the same query**: A legitimate resolver and a forged responder reply to the same query. This is the single most reliable indicator.
- **DNS response from an unexpected source**: A DNS reply arrives from an IP address **that does not match any configured resolver** (like 8.8.8.8 or your DNS server).
- **Suspiciously short TTL (Time-To-Live) values**: Attackers use very low TTLs (1 - 30s) to keep poisoned entries short-lived and reassert control.
- **Unsolicited DNS responses**: A DNS reply appears without a corresponding DNS request from the victim.

We can filter out legitimate traffic like 8.8.8.8 which is the ip used by google.com

*Wireshark:*
    DNS queries only: `dns.flags.response == 0`
    DNS responses only: `dns.flags.response == 1`
    DNS domain search: `dns.qry.name == ".."`

----------------------------------------
<span style="color:rgb(146, 208, 80)">SSL Stripping:<br>Indicators of SSL Stripping:</span> 
- **Initial Request vs. Response:** The user's initial request may be for `HTTPS` (port 443), but the subsequent packets immediately shift to unencrypted `HTTP` (port 80) for the same domain.
- **Redirects/Link Rewriting**: Monitoring for redirects (HTTP Status Codes 301, 302) that persistently direct an HTTPS request to an resource.
- **Certificate Errors**: Although the attacker usually tries to hide this, the initial **TLS/SSL Handshake** may fail or display a self-signed certificate if the attacker uses a more direct proxying technique.