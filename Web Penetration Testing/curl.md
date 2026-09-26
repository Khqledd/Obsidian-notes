<span style="color:rgb(247, 145, 29)">curl</span>: send a basic HTTP request to any URL
     `-O`: download a page or a file and output the content into a file (`curl -O http://.../index.html `)
     `-k`: skip the certificate check for HTTPS websites
     `-v`: see the full HTTP response and request
     `-I`: sends a HEAD request and only display response header
     `-A`: to set our User-Agent
     `-u`: provide credentials (`curl -u admin:admin http://<SERVER_IP>:<PORT>/`) or (`curl http://admin:admin@<SERVER_IP>:<PORT>/`)
     `-X`: specify the HTTP method (`-X POST`)
     `-d`: data you are sending in the request body (`-d 'username=admin&password=admin'`)
     `-b`: send cookie with the request (`curl -b 'PHPSESSID=abc123' http://example.com`)
     `-H`: set a request header (`curl -H 'Content-Type: application/json' http://example.com`)