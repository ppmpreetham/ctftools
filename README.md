# CTF Tools

## Binary Exploitation

### GCC flags
- `-fno-stack-protector` -> disable canary
- `-no-pie` -> no PIE
- `-m32` -> compiles in 32 bit (i386)
- `-z execstack` -> disable NX and stack is executable now

A nice way to use this is 
```bash
gcc file.c -o vuln -fno-stack-protector -z execstack -no-pie -m32
```

### tmux
- `tmux`
- `ctrl + %, v` -> vertical split
- `ctrl + %, <-/->` -> change to left of right screen

### Know your file
```bash
checksec --file <filename>` or `checksec file=filename
```

`file <filename>`

`rabin2 -I <filename>`

### Tracing
Checks what the program's doing with the input
`ltrace` and `strace`(for remote servers)
`ltrace ./filename`

### Reading the functions inside
- `info functions`
- `rabin2 -i <filename>`
- `objdump -D <filename> | grep <funcitonname>`
- `nm -u <filename>`

USER GENERATED FUNCTIONS:

```bash
rabin2 -qs <filename> | grep -ve imp -e ' 0 '
```

### Strings inside binary
```bash
rabin2 -z <filename>
```

### gdb-pwndbg
(much faster than loading ghidra and doing stuff from there)
- `file <filename>` -> to open the file
- `info functions` -> show all the function names along with their addresses. (you can use this with `objdump -D <filename> | grep <funcitonname>`, which is much better)
- `disassemble main` -> disassemble the main funciton
- `disassemble <functionname>` -> disassemble particular functions

- `cyclic <number>` -> useful for testing when the seg faults
- `r` -> runs the function
- `cyclic -l <EIP-data>` -> lookup the amount of the data that can be inputted where overflow won't occur in little endian
- `python2 -c 'print "A"*28 + "\x82\x91\x04\x08"' > payload` -> 28 is the eip data and then the other code.
- `./ret2win < payload` -> input payload into the file
- instead, you can just send using `run < payload` inside of gdb-pwndbg
- `break *addr` -> add a breakpoint at an addr
- `delete breakpoints` -> delete breakpoints

**IP/EIP/RIP** -> Data where you can go without seg fault (data that can be stored in the variable/line)
**IP/EIP/RIP** -> Stores the address of the next function that must be executed (overflow this)

### python2 (not python3)
```python
python2 -c 'code here'
```

### redare
`r2 filename`
`aa` -> analyze all flags
`afl` -> all functions list

### Overwriting Variables on the Stack
- Replace the 

### NX enabled
Try using ropper since it might be an ROP attack
