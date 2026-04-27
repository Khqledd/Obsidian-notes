<span style="color:rgb(146, 208, 80)">RSA variables</span>:
- p and q are large prime numbers
- n is the product of p and q
- The public key is n and e
- The private key is n and d
- m is used to represent the original message, i.e., plaintext
- c represents the encrypted text, i.e., ciphertext
- ϕ(n) = (p−1)(q−1)
- plaintext=ciphertext^d mod n (d = inverse(e,  ϕ(n))

-<span style="color:rgb(0, 176, 80)">Sites for cryptography</span>: 
- Cyberchef: encrypting / decrypting / encoding / decoding 
- RSActftool 
- WolframAlpha: calculate modulo and mathematic values
- CrackStation / Hashes.com: crack hash if its stored in rainbow table

-<span style="color:rgb(146, 208, 80)">You may need to use GPG to decrypt files in CTFs. With PGP/GPG, private keys can be protected with passphrases in a similar way that we protect SSH private keys. If the key is passphrase protected, you can attempt to crack it using John the Ripper and gpg2john</span>
     gpg --import backup.key
     gpg --decrypt message.gpg

-<span style="color:rgb(146, 208, 80)">Password hashes are stored in /etc/shadow ($ prefix $ options $ salt $ hash)</span>

<span style="color:rgb(0, 176, 80)">Common commands</span>:
- `md5sum`: Calculate MD5 hash
- `sha1sum`: Calculate SHA1 hash
- `sha256sum`: Calculate SHA256 hash
- `hexdump`: raw binary content of a hexadecimal format file

- `hashcat`: crack hashes (hashcat -m <hash_type (https://hashcat.net/wiki/doku.php?id=example_hashes)> -a 0 hashfile wordlist)

- `john`: crack hashes
     Automatic cracking: john  --wordlist=rockyou.txt  hash.txt
     
     Format specific: john  --format=raw-MD5  --wordlist=rockyou.txt  hash.txt
          (Check formats: john --list=formats or use the hash identifier tool, wget https://gitlab.com/kalilinux/packages/hash-identifier/-/tree/kali/master  (on kali), then: python3 hash-id.py, then: enter the hash)
        
     Adding --single and removing rockyou: single cracking mode (Mike,MiKe,MIKE,etc..) with editing the hash file to add the username before the hash: **1efee03cdc ----> mike:1efee03cdc**

- `unshadow`: john tool to crack /etc/shadow hashes with the help of /etc/passwd
     **unshadow (path to passwd) (path to shadow) > unshadow.txt**: we can either use the full passwd and shadow or the relevant line from each, and then we use unshadow.txt as the hash file in john above

- `zip2john`: crack the password on a zip file (or we use rar2john for RAR)
     -like unshadow we turn the zip file into a hash format that john can understand,
     -**zip2john zip_file.zip > zip_hash.txt**
     -**john --wordlist=...  zip_hash.txt**

- `ssh2john`: same thing as above for the password of the private key authentication in ssh:
     on kali: python /usr/share/john/ssh2john.py  id_rsa > id_rsa_hash.txt
     then we put the id_rsa_hash.txt in john


-<span style="color:rgb(146, 208, 80)">Useful tool for generating a wordlist from an exisiting wordlist</span>: Mentalist
-<span style="color:rgb(146, 208, 80)">Useful tool to search for an existing wordlist</span>: wordlistctl

