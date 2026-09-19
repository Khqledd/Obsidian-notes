Connecting to the Dojo through SSH: `ssh -i key hacker@dojo.pwn.college`

- <span style="color:rgb(0, 176, 240)">ILSpy</span>: **.NET** decompiler, it takes compiled .NET assemblies (.exe /.dll) and converts it back into readable C# source code
    `run it by typing ilspy in the terminal in linux`
    `it only works on .NET, make sure by typing (file thing.exe)`

--------------------
Create a file with a specific header (<MAG for example): `printf "<MAG" > solve.cimg`
     Endiannes matters in cases like this `assert int.from_bytes(header[:4], "little") == 0x47414D3C, "ERROR: Invalid magic number!"`
     0X47414D3C = `GAM<`   but the endiannes is little so header must be `<MAG`