
https://github.com/volatilityfoundation/volatility3
<span style="color:rgb(112, 48, 160)"><b>Volatility</b></span>: Tools used for memory image forensics `(vol -f example.mem)`
  some common commands to include after the .mem file:-  (can replace windows with linux/mac)
    - windows.pstree.PsTree
    - windows.pslist.PsList
    - windows.cmdline.CmdLine
    - windows.filescan.FileScan
    - windows.dlllist.DllList
    - windows.malfind.Malfind
    - windows.psscan.PsScan
    -<span style="color:rgb(255, 255, 255)">for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree .. ; do vol -q -f wcry.mem $plugin > wcry.$plugin.txt; done</span>

`vol -f <file> windows.info`: Information about what the host is running from the memory dump
