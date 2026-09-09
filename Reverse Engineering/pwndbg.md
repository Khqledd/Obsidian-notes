## <span style="color:rgb(218, 121, 43)">Basic commands:</span> 
- <span style="color:rgb(255, 255, 0)">pwndbg</span>: launch the debugger
- <span style="color:rgb(255, 255, 0)">break</span>: set a breakpoint for the debugger to stop at
    `break main: Stop as soon as the main function starts executing`
- <span style="color:rgb(255, 255, 0)">si</span>: step one assembly instruction
- <span style="color:rgb(255, 255, 0)">p</span>: print value, use $ before registers / & for address
    `p $rax: prints value of rax / p &first: prints address of variable called 'first'`
- <span style="color:rgb(255, 255, 0)">x/</span> : examine
    `x/x 0x7fffffffdb98: examine hex value at this address`
    `x/c 0x7fffffffdb98: examine character at this address`
- <span style="color:rgb(255, 255, 0)">context</span>: refresh the page, show everything again