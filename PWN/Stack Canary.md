### <span style="color:rgb(255, 192, 0)">What is a Stack Canary?</span>

- In function prologue **A random value placed on the stack between the buffer and the return address**, checked before the function returns
- If it's been modified → program calls `__stack_chk_fail()` → **abort**
- Set at program start, stored in `gs:0x28` (thread-local storage)

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

### <span style="color:rgb(146, 208, 80)">Method 1 — Format String Leak</span>

If there's a `printf(buf)` vulnerability before the overflow:

python

```python
from pwn import *

p = process('./binary')

# Brute-force which argument index holds the canary
# Canary is on the stack, looks like 0x????????00 (ends in 00)
p.sendline(b"%p " * 20)           # leak 20 stack values
print(p.recvall())                 # find the one ending in \x00
```