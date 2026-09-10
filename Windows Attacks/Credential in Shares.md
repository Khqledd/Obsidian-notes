- Credentials exposed in network shares within scripts and configuration files (batch, cmd, PowerShell, conf, ini, config)
------------------
1) First step is identifying what shares exist in a domain, such as PowerView's Invoke-ShareFinder
```
Invoke-ShareFinder -domain eagle.local -ExcludeStandard -CheckShareAccess
```