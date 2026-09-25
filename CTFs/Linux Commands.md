---
color: "linear-gradient(45deg, #23d4fd 0%, #3a98f0 50%, #b721ff 100%)"
sticker: emoji//1f44b
cssclasses:
---
<span style="color:rgb(231, 116, 8)"><b>ls</b></span> list directory contents (directory = folder) ,ls -alps: list files with detailed information: 
 • -a → Show all files, including hidden ones (those starting with .). 
 • -l → Show a long listing (permissions, owner, size, date). 
 • -p → Mark directories with a / at the end. 
 • -s → Show the file size in blocks.

<span style="color:rgb(231, 116, 8)">cd</span>: change the working directory (cd alone takes us to home directory)
Common directories:
    /etc: stores files used by the operating system
    /var: stores data that is frequently accessed by services or applications
    /root: home directory for the "root"
    /tmp: temporary, files in tmp are deleted after restart

<span style="color:rgb(231, 116, 8)">mkdir</span>: create a new directory

<span style="color:rgb(231, 116, 8)">pwd</span>: print working directory

<span style="color:rgb(231, 116, 8)">cat</span>: concatenate files and print on the standard output 
(cat file.txt -> prints file content)

<span style="color:rgb(231, 116, 8)">file</span>: determine file type (text, image, binary) (file ./* : type of every file in the current directory, * is all wildcard) 
-The command file */{.,}* would return the file type of every file in the folders. 
We use */* to print all files in all directories. However, this does not include hidden files. Therefore we use {.,} to include files starting with a . and , indicates files starting with anything else 
    -(find / -type f ! - executable: finding non executable files) 
note: -type f = files , -size 1033c = 1033 bytes

<span style="color:rgb(231, 116, 8)">du</span>: estimate file space usage
<span style="color:rgb(255, 149, 0)">wc</span>: count the number of lines `wc -l`
<span style="color:rgb(255, 149, 0)">cut</span>: seperate the file into columns `cut -d',' -f3,4,5` (cut after every coma, show columns number 3 4 5)

<span style="color:rgb(231, 116, 8)">find</span>: search for files in a directory (find "hello" file.txt) 
-(ls -l: lists all files and directories in long format, 3rd column is the user and 4th column is group) 
    -find / -type f -user bandit7 -group bandit6: searched for the file that is owned by user bandit7 in group bandit 6, / means start searching from the root, we can replace it with . to start searching from the current directory. 
    -(2>/dev/null) hides error messages or permission denied
    -find ~ -name (STAR).txt (all files with the .txt extension)
    -find / -type f -user root -perm -u=s 2>/dev/null (files with SUID perms)
    -for i in $(cat filenames); do find / -name "$i" -exec sha1sum '{}' \; 2>/dev/null; done

<span style="color:rgb(231, 116, 8)">ssh</span>: connecting to ssh (ssh user@host-name -p port)

<span style="color:rgb(231, 116, 8)">grep</span>: search for text patterns (we can use a command before grep and connect with ( | ) which takes output of the first command and uses it as input for the grep)
    `grep -v "CRON": exclude "CRON" from results`
    `grep -E "Accepted|Failed": include results containing either`

<span style="color:rgb(231, 116, 8)">chmod</span>: to change the mode of a file
-to make a script executable we use (chmod +x script.sh) then we can run it with (./script.sh)
     r=read, w=write, x=execute
     after doing (ls -l) we can see letters like rwxrwx...
     first 3: owner, next 3: group, last 3: others
     r=4 w=2 x=1  ----->  rw-r--r-- = 644 (owner can read+write, group and others can only read) 
        -so we can do (chmod 644 file.txt)

<span style="color:rgb(231, 116, 8)">netcat/nc</span>: connect to a server (nc example.com 80) 
    -opens a connection on example.com port 80 (HTTPS) (TCP or UDP and no default port)

<span style="color:rgb(231, 116, 8)">wget</span>: download a file from a url (wget https://example.com/file.zip)
    -saves file.zip in th

<span style="color:rgb(231, 116, 8)">unzip</span>: extract files from .zip archive in linux 
    -(unzip -l archive.zip: list contents without extracting)

<span style="color:rgb(231, 116, 8)">strings</span>: prints the strings of printable characters in a file that are at least 4 characters long (mainly used for non-text files, useful with grep)

<span style="color:rgb(231, 116, 8)">sort</span>: sort a file in alphabetical order (sort file.txt | uniq -u: show text that occurs only once)

<span style="color:rgb(231, 116, 8)">uniq</span>: only removes consecutive duplicates

<span style="color:rgb(231, 116, 8)">base64</span>: turns the file contents into base64 text(encode): (hello -> aGVsbG8=) or decode a file and turn it to normal text using the command: base64 -d file.txt
     echo "jhkgfhgfhkjgfgh=" | base64 -d

<span style="color:rgb(231, 116, 8)">tr</span>: translate (transform) or delete characters 
    -example: to turn lowercase into uppercase:(echo "hello" | tr 'a-z' 'A-Z') so, tr [string1] [string2] turns string1 to string2 
    -example: to delete characters(echo"hello123" | tr -d '0-9' --> "hello)

<span style="color:rgb(231, 116, 8)">xxd</span>: hex dump tool It shows the contents of a file (or stdin) in hexadecimal + ASCII, and can also convert hex back into binary (create a hex dump or reverse it) -[xxd ]: creates a hex dump 
-[xxd -r ]: reverts the hex dump 
running (xxd file.data) alone doesnt change anything it just prints out the hex view of the file, we can then find out what type of decompressing it needs from https://en.wikipedia.org/wiki/List_of_file_signatures

<span style="color:rgb(231, 116, 8)">gzip</span>: compresses or decompresses (-d) a file to change it's size, usually ends with .gz (file.txt.gz after compressing)

<span style="color:rgb(231, 116, 8)">bzip2</span>: like gzip but compressed files get .bz2 extension

<span style="color:rgb(231, 116, 8)">tar</span>: packs multiple files/folders in one archive without compressing unless combined with gzip or bzip2, default output ends with .tar 
-cf: create archive file 
-xf: extract archive fil

<span style="color:rgb(231, 116, 8)">cp</span>: copy files or directories 
    -(cp file1.txt file2.txt: make copy of file1 called file2) 
    -(cp file1.txt /tmp/ :places file1 inside /tmp/) 
    -(cp file1.txt file2.txt /tmp/ : copy multiple files into directory) 
    - ~/data.txt = data.txt in the home directory 
    -(cp ~/data.txt . :copy data.txt from home to current directory (.) )

<span style="color:rgb(231, 116, 8)">mv</span>: move or rename files or directories 
    -mv old.txt new.txt 
    -mv file.txt /tmp/ --> moves file.txt to tmp directory

<span style="color:rgb(231, 116, 8)">telnet</span>: used to connect to remove systems over the telnet protocol (TCP Port 23 by default), it is used to communicate with another host 
    -(telnet example.com): connect to remote host on port 23 
    -(telnet example.com 80): connect to a specific port

<span style="color:rgb(231, 116, 8)">nmap</span>: network scanning, host discovery and security auditing. it can find out what devices are on a network, what ports are open and what services are running. 
    (-sV for service and version) 
    -(nmap ): Scan a single host 
    -(nmap -p 20,80,443 example.com): Scan specific ports 
    -(nmap 10.10.10.0-255): range 
    -(nmap -sL -n 255.10.255.25): list of hosts nmap will scan, 
    -n so DNS wont try to find host name
    <span style="color:rgb(0, 176, 240)">best: nmap -sC -sV -oA scan 10.10.10.10</span>

<span style="color:rgb(231, 116, 8)">netstat</span>: display information about network connections, routing tables, interface statistics. common usage is 
    -netstat -tuln → List all listening ports with protocol, IP, and port number. 
    -netstat -anp → Show all connections with the process ID/program using them. 
    -netstat -rn → Display routing table in numeric form. 
    - netstat -i → Show interface statistics

<span style="color:rgb(231, 116, 8)">rustscan</span>: fast port scanner to find open ports
     -rustscan -a 10.48.189.88
     -rustscan -a 10.48.189.88 -- -A (everything after -- is passed to nmap)

<span style="color:rgb(231, 116, 8)">man</span>: show the manual to a command
     man ls (shows what can come after ls (-p -a -s... etc)
     https://linux.die.net/man/

<span style="color:rgb(231, 116, 8)">--help</span>: after a command, like manual but shorter 

<span style="color:rgb(231, 116, 8)">touch</span>: create a new file
     -touch note (create a file with the name "note")

<span style="color:rgb(231, 116, 8)">rm</span>: remove files and folders
     -rm -R my directory (must put -R before directory)

<span style="color:rgb(231, 116, 8)">su</span>: switch to another user account inside the terminal
    -su -l user2 (new session has dropped us into the home directory of "user" automatically because of the -l)

<span style="color:rgb(231, 116, 8)">echo</span>: store text in a file
     -echo "test" > file.txt (Overwrite file)
     -echo "test" >> file.txt (Append to file)
     -name=Khaled
        echo $name (Printing a variable)

<span style="color:rgb(231, 116, 8)">nano</span>: editing a file
     nano filename (using ctrl+(key) to control the menu)
     -nano /etc/hosts ---> and then type (10.10.20.40 site.thm) if the site wasn't loading
         to close, CTRL+O -> ENTER -> CTRL+X

<span style="color:rgb(231, 116, 8)">scp</span>:  secure transfer of files or directories between users
     scp SOURCE DESTINATION
     -scp important.txt ubuntu@192.168.1.30:/home/ubuntu/transferred.txt
         (copying important.txt from our local machine to transferred.txt on the remote machine)

<span style="color:rgb(231, 116, 8)">ps</span>: list of running processes of the user
    -ps aux: shows all running processes on the system

<span style="color:rgb(231, 116, 8)">top</span>: gives you real-time statistics about the processes running on your system instead of a one-time view

<span style="color:rgb(231, 116, 8)">systemctl</span>: getting processes/services to start on boot, used to manage systemd services
     -systemctl OPTION SERVICE
         systemctl start apache2

<span style="color:rgb(231, 116, 8)">gobuster</span>: directory and file brute-forcing tool used to find hidden folders and pages on websites (replacing dir with dns enumerates for subdomains like fuzz)
       gobuster dir -u http://TARGET -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 64
       gobuster dns -d example.thm -w /usr/share/wordlists....
       gobuster vhost -u "http://TARGET" --domain example.thm -w /usr/share/wordlists/dirb/common.txt --append-domain --exclude-length 250-320

<span style="color:rgb(231, 116, 8)">searchsploit</span>: tool to search for known exploits

<span style="color:rgb(231, 116, 8)">hydra</span>: brute forcing passwords
    - hydra -l root -P passlist.txt MACHINE_IP -t 4 ssh
    - hydra -l user -P passlist.txt ftp://MACHINE_IP
    - hydra -l user -P passlist.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -s port -V
 

<span style="color:rgb(231, 116, 8)">sudo</span>: It lets you run a command with administrator (root) privileges
     -sudo -l (lists all commands you can run without password)

<span style="color:rgb(231, 116, 8)">dirsearch</span>: web directory and file brute forcing tool (like gobuster but doesnt need a wordlist)
     dirsearch -u http://example.com

<span style="color:rgb(231, 116, 8)">bash scripts</span>:
    -Script ends with .sh extension
    -Script starts with #!/bin/bash
    -echo: prints output     read: reads input
    -read name ---> $name= variable
    -giving script execution perms: chmod +x script.sh
    -executing the script: ./script.sh

<span style="color:rgb(231, 116, 8)">nslookup</span>: look up the IP address of a domain
     `nslookup example.com`

<span style="color:rgb(231, 116, 8)">whois</span>: look up information about who owns and manages a domain name or IP address (useful in OSINT).
     `whois example.com`

<span style="color:rgb(231, 116, 8)">tcpdump</span>: capturing network traffic (must be root or use sudo)
     `-i`: specify interface (or -i any)
     `-w`: saved captured packets into a pcap file
     `-r`: read packets from a file (-r FILE)
     `-c`: limit the number of captured packets (-c COUNT)
     `-n`: don't resolve ip addresses, no DNS lookups (numeric format output)
     `-l`: line-buffered output which helps when using grep
     we can filter with these while using logical operators instead of slash (and or not): host example.com / port 53 / protocol / dst host IP / src host IP
     -more filters in (man pcap-filter)

<span style="color:rgb(231, 116, 8)">steghide</span>: tool used for steganography
     steghide info image.jpg: show hidden files in an image
     steghide extract -sf image.jpg: extract hidden data from image
    `stegseek image.jpg rockyou.txt (brute force password)`

<span style="color:rgb(231, 116, 8)">cewl</span>: generate a custom wordlist from a website (d = depth)
     cewl -d 2 -w $(pwd)/example.txt https://example.org

<span style="color:rgb(231, 116, 8)">sqlmap</span>: tool used for sql injection
    `--wizard`: guides with each step by asking questions
    `--dbs`: extract databases names 
    `-D database_name --tables`: extract information about tables of that db
    `-D database_name -T table_name --dump`:enumerate records from a table
    `--cookie="SESSIONID=abcdef123456"`: when testing if a website is vulnerable to sql injection attack when the url doesnt look like: /search?cat=1. we use the -u flag to test for web pages with a URL that uses GET parameter like the example /search?cat=1
- sqlmap -r intercepted_request.txt: POST-based testing used in login forms, intercept the post request and paste it into a txt file

<span style="color:rgb(231, 116, 8)">ffuf</span>: subdomain enumeration
    `ffuf -w /usr/share/wordlists/... -H "Host: FUZZ.tryhackme.com" -u  http://10.112.173.80 -fs {size}
    
- username enumeration
    `ffuf -w /usr/share/wordlists/.. -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.112.134.214/customers/signup -mr "username already exists"`

- brute forcing (easier with hydra)
    `ffuf -w usernames.txt:W1,/usr/share/wordlists:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.112.134.214/customers/login -fc 200`

<span style="color:rgb(240, 121, 10)">xfreerdp</span>: Connect to remote desktop
     `xfreerdp /v:10.10.10.10 /u:admin /p:password /dynamic-resolution`

<span style="color:rgb(247, 145, 29)">curl</span>: send a basic HTTP request to any URL
     `-O`: download a page or a file and output the content into a file (`curl -O http://.../index.html `)
     `-k`: skip the certificate check for HTTPS websites
     `-v`: see the full HTTP response and request
     `-I`: sends a HEAD request and only display response header
     `-A`: to set our User-Agent
     `-u`: provide crede