
https://github.com/volatilityfoundation/volatility3
<span style="color:rgb(112, 48, 160)"><b>Volatility</b></span>: Tools used for memory image forensics `(vol -f <example.mem> windows.x)`
  some common commands to include after the .mem file:-  (can replace windows with linux/mac)
    -`windows.info`: Information about what the host is running from the memory dump
    - `windows.pstree`:  List all processes based on their PPID
    - `windows.pslist`:  List of all current and terminated processes
    - `windows.psscan`:  Another technique to list processes, some processes can evade detection in pslist
    - `windows.netstat`: Network connections present at the time of extraction from the host machine, utilize with other tools like bulk-extractor to extract a pcap file from a memory file: https://www.kali.org/tools/bulk-extractor/
    - `windows.dlllist`: List all DLLs associated with processes at the time of extraction (Paths of processes)
    - `windows.malfind`: Scans process memory looking for signs of code injection (PAGE_EXECUTE_READWRITE and files starting with MZ in the hexdump are suspicious)
    - `windows.cmdline`: Shows the full command line each running process was launched with
    - `windows.ssdt`: Search for SSDT hooking (one of techniques used by malware), In this case SSDT hooking, windows uses SSDT to look up system functions, an adversary can hook into this table and modify pointers. other hooking methods are (IAT/IRP/EAT/Inline)
    


**windows.memmap is used in memory forensics to map out and dump the memory pages belonging to a specific process, we need to specify a directory to dump (in this case /tmp/) and the pid of the process, we will end up with a .dmp file in the /tmp/ directory:
    `vol -f Investigation-1.vmem -o /tmp/ windows.memmap --pid 1640 --dump` produces: {pid.1640.dmp}
Then use `strings` with `grep` to search

Using `strings file.mem` is also possible