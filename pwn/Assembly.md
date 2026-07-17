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

**Control Flow:**
    <span style="color:rgb(146, 208, 80)">mov:</span> featches data from memory: `mov rax, [rbp+8]` (rax = the value stored AT address rbp+8) -memory is read
    <span style="color:rgb(146, 208, 80)">lea:</span> calculates the address: `lea rax, [rbp+8]` (rax = address of rbp+8, pure arithmetic) -no memory read
    <span style="color:rgb(146, 208, 80)">cmp:</span> `cmp rax,rbx` basically does rax-rbx only to set CPU flags based on subtraction, no modification on both
    <span style="color:rgb(146, 208, 80)">test:</span> `test rax, rax` (basically does bitwise rax AND rax, mostly used to check if register is zero or non-zero). used right after a function call

**Conditional Jumps:**
    They always come after a cmp or test and interpret the flags that instruction set:
    je/jz: jump if equal / jump if zero
    jne/jnz: jump if not equal / not zero
    

