- One of the most critical outputs is (`EVE.json`), a JSON formatted log that records a wide range of event types including alerts, HTTP, DNS, TLS metadata, drop, SMTP metadata, flow, netflow, and more

- <span style="color:rgb(0, 176, 240)">To view Suricata rules</span>: `ls -lah /etc/suricata/rules`
- Rules can contain <span style="color:rgb(0, 176, 240)">variables</span> like `$HOME_NET` which are defined in: `/etc/suricata/suricata.yaml` , where you can add rule files under rule-files: by specifying their path