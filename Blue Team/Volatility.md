
https://github.com/volatilityfoundation/volatility3
<span style="color:rgb(112, 48, 160)"><b>Volatility</b></span>: Tools used for memory image forensics `(vol -f example.mem)`
  some common commands to include after the .mem file:-  (can replace windows with linux/mac)
    - `windows.pstree`:  List all processes based on their PPID
    - `windows.pslist`:  List of all current and terminated processes
    - `windows.psscan`:  Another technique to list processes, some processes can evade detection in pslist
    - `windows.netstat`: Network connections present at the time of extraction from the host machine, utilize with other tools like bulk-extractor to extract a pcap file from a memory file https://www.kali.org/tools/bulk-extractor/]
    - `windows.dlllist`
    - `windows.malfind`
    - `windows.cmdline`
    -<span style="color:rgb(255, 255, 255)">for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree .. ; do vol -q -f wcry.mem $plugin > wcry.$plugin.txt; done</span>

`vol -f <file> windows.info`: Information about what the host is running from the memory dump
