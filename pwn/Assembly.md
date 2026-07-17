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
    <span style="color:rgb(146, 208, 80)"> push:</span> add item to the top of the stack (decrements rsp)
     <span style="color:rgb(146, 208, 80)">pop:</span> remove the top item (increments rsp)
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
     `[rax]`: Memory address in rax
     `[rax+8]`: Memory address in rax+8
     `[rbp-0x10]`: dereference an address (stack local) "take the value in rbp, subtract 0x10 from it, treat that result as a memory address, and access what's stored at that address"
     `[rdi]`: pointer dereference
     b
     Assembly directives: begin with a "." and are directions to the assembler.:
         `.text`: tells assembler that the information that follows is program text (assembly instructions), contains the executable machine code
         `.data`: holds initialized global and static variables (those given a nonzero/explicit value at compile time)
         `.bss`: holds uninitialized (or zero-initialized) global/static variables
         `.rodata:` read-only data, string literals and const globals



