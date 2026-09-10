## <span style="color:rgb(218, 121, 43)">Basic commands:</span> 
- <span style="color:rgb(255, 255, 0)">pwndbg</span>: launch the debugger
- <span style="color:rgb(255, 255, 0)">break</span>: set a breakpoint for the debugger to stop at
    `break main: Stop as soon as the main function starts executing`
    `break *0x555555555171: break at a specific address (* before the address)`
- <span style="color:rgb(255, 255, 0)">si</span>: step one assembly instruction (pwndbg highlights the registers that changed in red in the "registers" panel)
- <span style="color:rgb(255, 255, 0)">p</span>: print value, use $ before registers / & for address
    `p $rax: prints value of rax / p &first: prints address of variable called 'first'`
    `p 0x7fffffffda70 - 0x7fffffffd7c0: difference between two addresses`
- <span style="color:rgb(255, 255, 0)">x/</span> : examine
    `x/x 0x7fffffffdb98: examine hex value at this address (could specify the number of hex characters like x/10x / or the full 8 hex bytes with x/gx)`
    `x/c 0x7fffffffdb98: examine character at this address`
    `x/s 0x7fffffffdb98: examine string at this address`
- <span style="color:rgb(255, 255, 0)">context</span>: refresh the page, show everything again
- <span style="color:rgb(255, 255, 0)">finish</span>: runs the program until the current function returns
- <span style="color:rgb(255, 255, 0)">delete</span>: delete a breakpoint
    `delete 2: deletes breakpoint number 2`
