## <span style="color:rgb(247, 145, 29)">Common targets for stack overflow:</span> 
- **ret2win:** `RIP -> win()`
- **ret2libc:** `RIP -> libc function`
- **ROP:** `RIP -> gadget -> gadget -> gadget`

## <span style="color:rgb(247, 145, 29)">Protections to check:</span> 
- COMMAND: `checksec ./binary`
![[Pasted image 20260923192050.png|563]]