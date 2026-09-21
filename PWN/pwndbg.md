## <span style="color:rgb(218, 121, 43)">Basic commands:</span> 
- <span style="color:rgb(255, 255, 0)">pwndbg</span>: launch the debugger
- <span style="color:rgb(255, 255, 0)">si</span>: step one assembly instruction (pwndbg highlights the registers that changed in red in the "registers" panel)
- <span style="color:rgb(255, 255, 0)">ni</span>: step OVER the next instruction
- <span style="color:rgb(255, 255, 0)">p</span>: print value, use $ before registers / & for address
    `p $rax: prints value of rax / p &first: prints address of variable called 'first'`
    `p 0x7fffffffda70 - 0x7fffffffd7c0: difference between two addresses`
- <span style="color:rgb(255, 255, 0)">x/</span> : examine
    `x/x 0x7fffffffdb98: examine hex value at this address (could specify the number of hex characters like x/10x / or the full 8 hex bytes with x/gx)`
    `x/c 0x7fffffffdb98: examine character at this address`
    `x/s 0x7fffffffdb98: examine string at this address`
    `d for decimal, u for unsigned decimal`
- <span style="color:rgb(255, 255, 0)">context</span>: refresh the page, show everything again
- <span style="color:rgb(255, 255, 0)">finish</span>: runs the program until the current function returns
- <span style="color:rgb(255, 255, 0)">info</span>: inspect the state of the program / registers / breakpoints etc...
    ![[Pasted image 20260911024008.png]]
- <span style="color:rgb(255, 255, 0)">list</span>: list the source code of the compiled program if you have it
    `list: will show you source code surrounding the current location you are stopped at`
    `list <function name>: will show source code before and after the given function`
    `list <source file name>:<line number>: will show source code before and after the given line in the given file`
- <span style="color:rgb(255, 255, 0)">objdump</span>: Disassemble a binary to view assembly instructions: 
    `objdump -d -M intel /tmp/your-program` 
- <span style="color:rgb(255, 255, 0)">disassemble</span>: (short form **disas**) by itself will show you assembly surrounding the current location you are stopped at.
- <span style="color:rgb(255, 255, 0)">set</span>: modify a register
    `set $rax = 0xdeadbeeff00dface`
## <span style="color:rgb(240, 121, 10)">Breakpoints:</span> 
- <span style="color:rgb(255, 255, 0)">break</span>: set a breakpoint for the debugger to stop at
    `break main: Stop as soon as the main function starts executing`
    `break *0x555555555171: break at a specific address (* before the address)`
- <span style="color:rgb(255, 255, 0)">clear</span>: remove breakpoint at a location
    `clear main: remove breakpoint at main function`
    `clear *0x555555555149: remove breakpoint at that address`
- <span style="color:rgb(255, 255, 0)">delete</span>: delete a breakpoint by its number
    `delete 2: deletes breakpoint number 2`
- <span style="color:rgb(255, 255, 0)">disable</span>: disable a breakpoint by its number
- <span style="color:rgb(255, 255, 0)">enable</span>: enable a breakpoint by its number
- <span style="color:rgb(255, 255, 0)">info b</span>: list all breakpoints and their number

## <span style="color:rgb(247, 145, 29)">Tracing</span>:
(Used outside of pwndbg)
- <span style="color:rgb(255, 255, 0)">strace</span>: trace system calls
- <span style="color:rgb(255, 255, 0)">ltrace</span>: trace library calls
    running ltrace with different input changes things