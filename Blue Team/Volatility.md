
https://github.com/volatilityfoundation/volatility3
<span style="color:rgb(112, 48, 160)"><b>Volatility</b></span>: Tools used for memory image forensics `(vol3 -f example.mem)`
  some common commands to include after the .mem file:- 
    - windows.pstree.PsTree
    - windows.pslist.PsList
    - windows.cmdline.CmdLine
    - windows.filescan.FileScan
    - windows.dlllist.DllList
    - windows.malfind.Malfind
    - windows.psscan.PsScan
    -<span style="color:rgb(255, 255, 255)">for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree .. ; do vol3 -q -f wcry.mem $plugin > wcry.$plugin.txt; done</span>


