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

3- Output of the `cracked.txt` file, password of the