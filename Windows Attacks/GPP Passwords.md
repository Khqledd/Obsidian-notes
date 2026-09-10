- <span style="color:rgb(146, 208, 80)">SYSVOL</span>: is a network share on all Domain Controllers, containing logon scripts, group policy data, and other required domain-wide data
- AD stores all group policies in `\\<DOMAIN>\SYSVOL\<DOMAIN>\Policies\`
- Windows let admins push passwords (local admin, service accounts, etc.) via **Group Policy Preferences (GPP)**, stored in SYSVOL XML files, encrypted with AES, in a field called `cpassword`
---------------------------
1) To abuse `GPP Passwords`, we will use the [Get-GPPPassword] function from `PowerSploit`, which automatically parses all XML files in the Policies folder in `SYSVOL`, picking up those with the `cpassword` property and decrypting them once detected
```
Import-Module .\Get-GPPPassword.ps1
Get-GPPPassword
```

-----------------------

## Detection:

- Any access to the XML file will generate and event with ID <span style="color:rgb(146, 208, 80)">4663</span> when auditing is enabled