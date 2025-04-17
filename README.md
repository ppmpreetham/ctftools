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
### Checksec
- `checksec --file filename`

### Tracing
Checks what the program's doing with the input
`ltrace` and `strace`(for remote servers)
`ltrace ./filename`

### Overwriting Variables on the Stack
