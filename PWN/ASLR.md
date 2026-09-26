
- ASLR randomizes memory addresses each run (stack, heap, libc, binary).
- Top 2 bytes of an address are always `0x0000` — not random (64-bit hardware limit).
- Addresses are page-aligned too, so the last few digits are usually fixed.
- **Key idea:** offsets _between_ things in the same region never change — only the starting point (base) moves.
- So: leak **one** address in a region → subtract the known offset → you get the base → now you can find anything else in that region with math.
- If we overwrite the two least significant bytes of a pointer, we only have to brute-force one nibble (16 possible values) to successfully redirect the pointer to another location on the same page.
- Goal of an ASLR bypass = get **one leak**, then it's just addition/subtraction.