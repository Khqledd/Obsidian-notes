
https://github.com/volatilityfoundation/volatility3
<span style="color:rgb(112, 48, 160)"><b>Volatility</b></span>: Tools used for memory image forensics `(vol -f <example.mem> windows.x)`
  some common commands to include after the .mem file:-  (can replace windows with linux/mac)
    - `windows.pstree`:  List all processes based on their PPID
    - `windows.pslist`:  List of all current and terminated processes
    - `windows.psscan`:  Another technique to list processes, some processes can evade detection in pslist
    - `windows.netstat`: Network connections present at the time of extraction from the host machine, utilize with other tools like bulk-extractor to               extract a pcap file from a memory file: https://www.kali.org/tools/bulk-extractor/
    - `windows.dlllist`: List all DLLs associated with processes at the time of extraction
    - `windows.malfind`: Scans process memory looking for signs of code injection
    - `windows.cmdline`: Shows the full command line each running process was launched with
    - `windows.ssdt`: Search for SSDT hooking (one of techniques used by malware), In this case SSDT hooking, windows uses SSDT to look up              system functions, an adversary can hook into this table and modify pointers. other hooking methods are (IAT/IRP/EAT/Inline)
    

`vol -f <file> windows.info`: Information about what the host is running from the memory dump
