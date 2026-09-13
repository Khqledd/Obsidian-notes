
- <span style="color:rgb(0, 176, 240)">To view Suricata rules</span>: `ls -lah /etc/suricata/rules`
- Rules can contain <span style="color:rgb(0, 176, 240)">variables</span> like `$HOME_NET` which are defined in: `/etc/suricata/suricata.yaml` , where you can add rule files under rule-files: by specifying their path

- <span style="color:rgb(0, 176, 240)">Reading offline input</span>: `suricata -r /home/htb-student/file.pcap`, (This will create various logs).
- <span style="color:rgb(0, 176, 240)">Reading live input</span>: `sudo suricata --pcap=ens160 -vv`, (ens160 is the interface name from ifconfig).

- Suricata records data into <span style="color:rgb(0, 176, 240)">logs</span> stored in: `/var/log/suricata`
    - <span style="color:rgb(0, 112, 192)">eve.json</span>: One of the most critical outputs , a JSON formatted log that records a wide range of event types including alerts, HTTP, DNS, TLS metadata, drop, SMTP metadata, flow, netflow, and more. USE `jq` JSON PROCESSOR TO FILTER
    - Example on jq filter, Filter by alerts:  `cat /var/log/suricata/old_eve.json | jq -c 'select(.event_type == "alert")'`
    
    - <span style="color:rgb(0, 112, 192)">fast.log</span>: text-based log format that records alerts only and is enabled by default
    
    - <span style="color:rgb(0, 112, 192)">stats.log</span>: human-readable statistics log