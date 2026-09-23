- To <span style="color:rgb(0, 176, 80)">open the debugger</span>, hold and drag the binary into the blue bug
    ![[Pasted image 20260912023302.png]]

- To <span style="color:rgb(0, 176, 80)">launch gdb with ghidra</span>:
    1) Toolbar: Debugger -> Configure and launch "binary" -> gdb
    2) Don't change anything except `starti -> start`, to set a breakpoint at `main` and start there (ONLY WORKS IF main FUNCTION EXISTS), To pass an argument to the binary fill the `Arguments` section
     ![[Pasted image 20260912025016.png]]

--------------------

- In Ghidra variable names: `local_28._4_2_ == 1`:   4 ---> offset / 2 ---> bytes long
- In Ghidra variable names: `local_28;` means (rbp - 0x28)

**Same variable used as win and lose condition:**
![[Pasted image 20260924004943.png]]
     rbp - 0x48 → input_buffer ← you write from here (low address)
     rbp - 0x40 → local_40
     rbp - 0x38 → local_38 
     rbp - 0x30 → local_30 
     rbp - 0x28 → local_28 
     rbp - 0x20 → win_variable ← lower 4 bytes of local_20 
     rbp - 0x1c → lose_variable ← upper 4 bytes of local_20


- <span style="color:rgb(152, 226, 185)">undefined8 local_98[14];</span> means "14 elements, 8 bytes of unknown type"  so the TOTAL SPACE = 14 x 8 = 112 bytes
  unlike <span style="color:rgb(152, 226, 185)">char local_98[14];</span> which is "14 elements, char is 1 byte" so the TOTAL SPACE = 14 x 1 = 14 bytes