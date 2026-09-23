- A program writes **more data into a stack buffer than it can hold**, overwriting nearby stack data.
```C
char buffer[64];
gets(buffer);
```
If I send > 64 bytes ---> overflow

Typical stack layout: If I overwrite the return address I can control where the function returns
`[ buffer ]`
`[ padding ]`
`[ saved RBP ]`
`[ return address ]`  ← target

------------------------------------
## <span style="color:rgb(146, 208, 80)">The Goal:</span>
Find overflow
     ↓
Find offset to RIP
     ↓
Control RIP
     ↓
Redirect execution
     ↓
Get flag

## <span style="color:rgb(146, 208, 80)">Finding Offset:</span>
