
<span style="color:rgb(255, 255, 0)">Common commands</span>:
- <span style="color:rgb(255, 255, 0)">exiftool</span>: analyze image metadata
- <span style="color:rgb(255, 255, 0)">pdfinfo</span>: details of a pdf file (can be installed with sudo apt install poppler-utils)

**Random advice:**
- `when turning a gps location from metadata to searchable format we replace 'deg' with ° and remove spaces:`
  51 deg 30' 51.90" N, 0 deg 5' 38.73" W -----> 51°30'51.9"N 0°05'38.7"W

Logs in linux can be found in /var/log/... (depends on the type of log)
Logs can be viewed in the windows Event Viewer
![[Pasted image 20260227002428.png]]

<span style="color:rgb(255, 255, 0)">Firewall in Windows</span>: Windows Defender Firewall
<span style="color:rgb(255, 255, 0)">Firewall in Linux</span>: ufw (uncomplicated firewall)

<span style="color:rgb(231, 116, 8)">Defensive Solutions/Tools</span>:
- `SIEM`: Security Information and Event Management, it collets logs and correlates them for analysis

- `IDS`: Intrusion Detection System to detect malicious activity even after a packet passes the firewall like Snort (/etc/snort/...)

- `Vulnerability scanners`: Tools like Nessus, Qualys, Nexpose, OpenVAS

- `CAPA`: Malware analysis tool (in powershell: capa.exe .\cryptbot.bin)
  capa or capa.exe then point to the binary, output is txt

- `REMnux VM`: specialized linux distro that includes analysis tools and sandbox-like environment for dissecting malicious software,

- `oledump.py:` is a tool used to analyze OLE files (.doc, .xls, .ppt) where we can find VBA scripts and (M)acros, we can add -s 4 to check stream number 4, or add --vbadecompress the understand the hexdump

- `Volatility`: Tools used for memory image forensics (vol3 -f example.mem)
  some common commands to include after the .mem file:- 
    - windows.pstree.PsTree
    - windows.pslist.PsList
    - windows.cmdline.CmdLine
    - windows.filescan.FileScan
    - windows.dlllist.DllList
    - windows.malfind.Malfind
    - windows.psscan.PsScan
    -<span style="color:rgb(255, 255, 255)">for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree .. ; do vol3 -q -f wcry.mem $plugin > wcry.$plugin.txt; done</span>

- `FlareVM`: Powershell (windows) framework that installs hundreds of reverse engineering/malware analysis tools like the ones below:
    - `procmon`: Tracking system activity and process monitoring
    - `procexp`: Allows you to see the Process of the Parent-child relationship, DLLs loaded, and its path, and which program is accessing a folder
    - `HxD`: Hex editor for editing files, memory and drives. used in forensic investigation and data recovery (like fixing an extension) ,and manipulation
    - `CFF Explorer`:Can generate file hashes for integrity verification, authenticate the source of system files
    - `PEStudio`: Static analysis, or studying executable file properties without running the files
    - `FLOSS`: Extracts and de-obfuscates all strings from malware programs (floss .\file.exe ) in powershell