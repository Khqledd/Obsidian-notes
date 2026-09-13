- Network security monitoring framework, it passively inspects network traffic and generates detailed, structured logs describing what happened at the protocol level, and lets you write scripts

- Some Zeek logs:
    - `conn.log`: This log provides details about IP, TCP, UDP, and ICMP connections.
    - `dns.log`: Here, you'll find the details of DNS queries and responses.
    - `http.log`: This log captures the details of HTTP requests and responses.
    - `ftp.log`: Details of FTP requests and responses are logged here.
    - `smtp.log`: This log covers SMTP transactions, such as sender and recipient details.


- Process the entire `psempire.pcap` file and generate Zeek logs:
```
/usr/local/zeek/bin/zeek -C -r /home/htb-student/pcaps/psempire.pcap
```

- Filter DNS using zeek-cut (For DNS Exfiltration):
```
cat dns.log | /usr/local/zeek/bin/zeek-cut query | cut -d . -f1-7
```