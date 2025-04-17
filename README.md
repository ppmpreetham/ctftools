# CTF Tools

## Binary Exploitation

### GCC flags
- `-fno-stack-protector` -> disable canary
- `-no-pie` -> no PIE
- `-m32` -> compiles in 32 bit (i386)
- `-z execstack` -> disable NX and stack is executable now

### Checksec
- `checksec --file filename`
