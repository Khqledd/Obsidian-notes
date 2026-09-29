

Stuff that trips people up once basic shellcode doesn't just work out of the box.

### <span style="color:rgb(146, 208, 80)">1- Memory access width (be explicit about size)</span>

When writing an immediate value to memory, the assembler can't guess how many bytes you mean — say it explicitly:

```asm
mov BYTE PTR [rax], 5
mov WORD PTR [rax], 5
mov DWORD PTR [rax], 5
mov QWORD PTR [rax], 5
```

Getting this wrong is a common source of "my shellcode almost works but corrupts something nearby."

---

### <span style="color:rgb(146, 208, 80)">2- Forbidden bytes (why your shellcode gets cut short)</span>

Depending on how your shellcode is delivered into the program, certain bytes can break the transfer entirely:

|Byte (hex)|Breaks with|
|---|---|
|Null `\0` (0x00)|`strcpy`|
|Newline `\n` (0x0a)|`scanf`, `gets`, `getline`, `fgets`|
|Carriage return `\r` (0x0d)|`scanf`|
|Space (0x20)|`scanf`|
|Tab `\t` (0x09)|`scanf`|

Rule of thumb: **whatever function reads your input decides which bytes are safe.** Check how the program reads your payload (`gets`? `read`? `scanf`?) before assuming a byte is fine.

---

### <span style="color:rgb(146, 208, 80)">3- Fixing forbidden bytes with equivalent instructions</span>

Same result, different (safer) encoding — this is the main trick to know:

```asm
; instead of: mov rax, 0        (has null bytes)
xor rax, rax                    ; zero a register with no null bytes

; instead of: mov rax, 5        (has null bytes)
xor rax, rax
mov al, 5

; instead of: mov rbx, 0x67616c662f  "/flag"  (has null bytes)
mov ebx, 0x67616c66     ; "flag" (4 bytes, no leading null)
shl rbx, 8               ; shift left to make room
mov bl, 0x2f              ; add "/" in the low byte
```
