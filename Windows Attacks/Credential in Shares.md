- Credentials exposed in network shares within scripts and configuration files (batch, cmd, PowerShell, conf, ini, config)
------------------
1) First step is identifying what shares exist in a domain, such as PowerView's Invoke-ShareFinder
```
PS C:\Users\bob\Downloads> Invoke-ShareFinder -domain eagle.local -ExcludeStandard -CheckShareAccess

\\DC2.eagle.local\NETLOGON      - Logon server share
\\DC2.eagle.local\SYSVOL        - Logon server share
\\WS001.eagle.local\Share       -
\\WS001.eagle.local\Users       -
\\Server01.eagle.local\dev$     -
\\DC1.eagle.local\NETLOGON      - Logon server share
\\DC1.eagle.local\SYSVOL     
```

2) 