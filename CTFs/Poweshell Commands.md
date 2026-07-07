
<span style="color:rgb(0, 176, 240)">Get-Command</span>: discover what commands one can use
     `Get-Command -CommandType "function"`: filter output to return functions

<span style="color:rgb(0, 176, 240)">Get-Help</span>: provide detailed information about cmdlets
     `Get-Help Get-Date`: information and syntax about Get-Date

<span style="color:rgb(0, 176, 240)">Get-Alias</span>: shortcuts or alternative names for cmdlets

<span style="color:rgb(0, 176, 240)">Find-Module</span>: find cmdlets we can download from online repositories
     `Find-Module -Name "PowerShell*"`: search for command that starts with the word "PowerShell"

<span style="color:rgb(0, 176, 240)">Install-Module</span>: install the command 
     `Install-Module -Name "PowerShellGet"`: installs the cmdlet with the name PowerShellGet

<span style="color:rgb(0, 176, 240)">Get-ChildItem (dir)</span>: list all files and directories in a location specified with -Path, if no path is specified then it works for the current working directory

<span style="color:rgb(0, 176, 240)">Set-Location (cd)</span>: change the current directory

<span style="color:rgb(0, 176, 240)">New-Item</span>: create a new item with the need to specify its path and type
     New-Item **-Path** ".\captain-cabin\captain-wardrobe" **-ItemType** "Directory"
     New-Item **-Path** ".\captain-cabin\captain-boots.txt" **-ItemType** "File"

<span style="color:rgb(0, 176, 240)">Remove-Item</span>: removes either a directory or a file
     `Remove-Item -Path ".\captain-cabin\captain-wardrobe"`

<span style="color:rgb(0, 176, 240)">Copy-Item (copy)</span>: copy files specifying path and destination
     `Copy-Item -Path .\captain-cabin\captain-hat.txt -Destination .\captain-cabin\captain-hat2.txt`
 
<span style="color:rgb(0, 176, 240)">Move-Item (move)</span>: just like (copy) syntax

<span style="color:rgb(0, 176, 240)">Get-Content</span>: display the contents of a file like type in cmd or cat in linux
    `Get-Content -Path ".\captain-hat.txt"`

<span style="color:rgb(0, 176, 240)">Sort-Object</span>: sorting objects, can be used with piping (|)
     `Get-ChildItem | Sort-Object Length`: retrieves the files as objects and sorts them based on size

<span style="color:rgb(0, 176, 240)">Where-Object</span>: filter output, used with piping (use output of another command as the input for the next command)
     `Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"`: retrieve files with the .txt extension (-eq = equals)
         other useful commands like -eq: (ne,gt,ge,lt,le,like (for specific pattern))

<span style="color:rgb(0, 176, 240)">Select-Object</span>: another filtering cmdlet to limit the objects returned (columns)

<span style="color:rgb(0, 176, 240)">Select-String</span>: similar to grep in linux and findstr in windows cmd
    `` Select-String -Path ".\captain-hat.txt" -Pattern "hat"`

<span style="color:rgb(0, 176, 240)">Get-ComputerInfo</span>: comprehensive system information (systeminfo cmdlet also retrieves information but less detailed)

<span style="color:rgb(0, 176, 240)">Get-LocalUser</span>: lists all the local user accounts on the system

<span style="color:rgb(0, 176, 240)">Get-NetIPConfiguration</span>: similar to ipconfig but more details including DNS servers

<span style="color:rgb(0, 176, 240)">Get-NetIPAddress</span>: details for all IP addresses configured on the system even the ones currently not active

<span style="color:rgb(0, 176, 240)">Get-Process</span>: provides a detailed view of all currently running processes, including CPU and memory usage

<span style="color:rgb(0, 176, 240)">Get-Service</span>: allows the retrieval of information about the status of services on the machine

<span style="color:rgb(0, 176, 240)">Get-NetTCPConnection</span>: to monitor active network connections, displays current TCP connections (useful in incident response and malware analysis because it can uncover hidden backdoors)

<span style="color:rgb(0, 176, 240)">Get-FileHash</span>: generate file hashes for file integrity
     `Get-Filehash -Algorithm SHA256 bl0gger.exe`

<span style="color:rgb(0, 176, 240)">-Stream *</span>: view alternate data stream (ADS) of a file
     `Get-Item -Path "C:\House\house_log.txt" -Stream *`

<span style="color:rgb(0, 176, 240)">Invoke-Command</span>: runs command on local and remote computers