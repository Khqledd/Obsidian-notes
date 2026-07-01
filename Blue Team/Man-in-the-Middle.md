 
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