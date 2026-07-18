<span style="color:rgb(0, 176, 80)">Hex value conversion:</span>
![[Pasted image 20260718022143.png|384]]

**Registers:**
     <span style="color:rgb(146, 208, 80)">rax:</span> Return value, arithmetic
     <span style="color:rgb(146, 208, 80)">rbx:</span> General purpose
     <span style="color:rgb(146, 208, 80)">rcx:</span> Counter
     <span style="color:rgb(146, 208, 80)">rdx</span>: Arguments/arithmetic
     <span style="color:rgb(146, 208, 80)">r</span><span style="color:rgb(146, 208, 80)">di:</span> 1st function argument
     <span style="color:rgb(146, 208, 80)">rsi:</span> 2nd function argument
     <span style="color:rgb(146, 208, 80)">rbp:</span> Base pointer (stack frame)
     <span style="color:rgb(146, 208, 80)">rsp:</span> Stack pointer (top of stack)
     <span style="color:rgb(146, 208, 80)">rip:</span> Instruction pointer
     <span style="color:rgb(146, 208, 80)">r8-r15:</span> Additional registers
     **Function call convention slot orders:** rdi / rsi / rdx / rcx / r8 / r9
     **System call convention slot orders:** <span style="color:rgb(146, 208, 80)">rdi / rsi / rdx / r10 / r8 / r9</span>

**The Stack:**
     ![[Pasted image 20260718013323.png]]
    <span style="color:rgb(146, 208, 80)"> push:</span> add item to the top of the stack (decrements rsp and saves a value)
     <span style="color:rgb(146, 208, 80)">pop:</span> remove the top item (increments rsp and retrieves a value)
     Example:
         `push rbp`               //save old base pointer
         `mov rbp, rsp`       //establish new frame
         `sub rsp, 0x20`     //allocate local space
         . . .
         `leave`                   //mov rsp, rbp; pop rbp
         `ret`
         (Create stack frame, Reserve 32 bytes)
         (`call` pushes return address, `ret` pops it back into `rip`)

**Memory Addressing:**
     `[rax]`: Returns the value stored at that address
     `[rax+8]`: Value stored in address (rax + 8 bytes)
     `[rbp-0x10]`: dereference an address (stack local) "take the value in rbp, subtract 0x10 from it, treat that result as a memory address, and access what's stored at that address"
     `[rdi]`: pointer dereference
     `mov rbx, rax`: rbx now holds 0x4000 (the address)
     `mov rbx, [rax]`: rbx now holds the data stored at memory location 0x4000
     ===============================
     Assembly directives: begin with a "." and are directions to the assembler.:
         `.text`: tells assembler that the information that follows is program text (assembly instructions), contains the executable machine code
         `.data`: holds initialized global and static variables (those given a nonzero/explicit value at compile time)
         `.bss`: holds uninitialized (or zero-initialized) global/static variables
         `.rodata:` read-only data, string literals and const globals
         ``
    ``
    <span style="color:rgb(146, 208, 80)">Effective Address Calculation:</span> `[base + index*scale + displacement]`
    ![[Pasted image 20260718235150.png]]

**Control Flow:**
    <span style="color:rgb(146, 208, 80)">mov:</span> featches data from memory: `mov rax, [rbp+8]` (rax = the value stored AT address rbp+8) -memory is read (`mov dst, src --> dst = src`)
    <span style="color:rgb(146, 208, 80)">movsx</span>: move with sign-extend, copies a smaller value into a larger register while preserving its sign (two's complement value)
    <span style="color:rgb(146, 208, 80)">lea:</span> calculates the address: `lea rax, [rbp+8]` (rax = address of rbp+8, pure arithmetic) -no memory read (`lea dst, [addr] --> dst = computed address`)
    <span style="color:rgb(146, 208, 80)">cmp:</span> `cmp rax,rbx` basically does (rax-rbx) only to set CPU flags based on subtraction, no modification on both
    <span style="color:rgb(146, 208, 80)">test:</span> `test rax, rax` (basically does bitwise rax AND rax, mostly used to check if register is zero or non-zero). used right after a function call
    <span style="color:rgb(146, 208, 80)">xchg</span>: swap two values
    <span style="color:rgb(146, 208, 80)">syscall</span>: invokes a syscall, which is the number stored inside rax, `line1: mov rax, 42   line2: syscall`

**Conditional Jumps:**
    They always come after a cmp or test and interpret the flags that instruction set:
    <span style="color:rgb(146, 208, 80)">je / jz:</span> jump if equal / jump if zero
    <span style="color:rgb(146, 208, 80)">jne / jnz:</span> jump if not equal / not zero
    ``
    **Signed comparison (for int, signed types):**
         <span style="color:rgb(146, 208, 80)">jg / jnle:</span> jump if greater
         <span style="color:rgb(146, 208, 80)">jge:</span> jump if greater or equal
         <span style="color:rgb(146, 208, 80)">jl / jnge:</span> jump if less
        <span style="color:rgb(146, 208, 80)"> jle:</span> jump if less or equal
    ``
    **Unsigned comparison (for unsigned int, pointers,sizes):**
         <span style="color:rgb(146, 208, 80)">ja / jnbe:</span> jump if above
         <span style="color:rgb(146, 208, 80)">jae:</span> jump if above or equal
         <span style="color:rgb(146, 208, 80)">jb / jnae:</span> jump if below
         <span style="color:rgb(146, 208, 80)">jbe:</span> jump if below or equal

**Functions:**
    ![[Pasted image 20260718233207.png|484]]
    - The function has a stack that grows downwards
    - `push`: decrements rsp, stores value, pushes return value into the stack
    - `pop`: load value, increments rsp, return address into rip
    ![[Pasted image 20260718233653.png]]

**Arithmetic:**
     <span style="color:rgb(146, 208, 80)">add</span>: addition
     <span style="color:rgb(146, 208, 80)">sub</span>: subtraction, `sub dst, src ---> dst=dst-src`
     <span style="color:rgb(146, 208, 80)">inc</span>: increment +1      <span style="color:rgb(146, 208, 80)">dec</span>: decrement -1
     <span style="color:rgb(146, 208, 80)">neg</span>: negate register in terms of numerical value
     <span style="color:rgb(146, 208, 80)">not</span>: negate each bit of the register
     <span style="color:rgb(146, 208, 80)">and / or / not / xor</span>: logical values
     <span style="color:rgb(146, 208, 80)">shl</span>: `shl rax, 10`: shift rax's bits left by 10, filling 10 zeros to the right
     <span style="color:rgb(146, 208, 80)">shr</span>: `shr rax, 10`: shift rax's bits right by 10, filling 10 zeros to the left
     <span style="color:rgb(146, 208, 80)">sar</span>: `sar rax, 10`: shift rax's bits right by 10, with sign extension to fill the now missing 10 bits
     <span style="color:rgb(146, 208, 80)">ror</span>: rotate the bits of rax right by 10
     <span style="color:rgb(146, 208, 80)">rol</span>: rotate the bits of rax left by 10
    <span style="color:rgb(146, 208, 80)"> imul / mul:</span> multiplication, signed / unsigned
    <span style="color:rgb(146, 208, 80)"> idiv / div:</span> division, signed / unsigned
     **Note: cqo or cdq instructions immediately before a divison: signed divison. xor rdx,rdx before a divison: unsigned divison**

t