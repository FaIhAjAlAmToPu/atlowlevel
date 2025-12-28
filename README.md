## Assembly in emu8086
EMU-8086 is an emulator that simulates an Intel 8086 CPU with 1MB real mode memory, BIOS and DOS interrupts
### DOS Interrupts
DOS was a foundational and old operating system. DOS provides interrupts that program can use to do some special things. The most important one is **INT 21h**.
#### INT 21h
So think of **INT 21h** as a helper. You will say "Hey do this". The thing you want done is placed as a number at "ah" register.
| AH (hex) | Function                      | Input Register(s)      | Output Register(s) |
| -------- | ----------------------------- | ---------------------- | ------------------ |
| 01h      | Read character (with echo)    | —                      | **AL**             |
| 02h      | Display character             | **DL**                 | —                  |
| 06h      | Direct console I/O            | **DL** (FFh = input)   | **AL**, ZF         |
| 07h      | Read character (no echo)      | —                      | **AL**             |
| 08h      | Read character (no echo)      | —                      | **AL**             |
| 09h      | Display `$`-terminated string | **DS:DX**              | —                  |
| 0Ah      | Buffered string input         | **DS:DX**              | Memory buffer      |
| 0Bh      | Check keyboard status         | —                      | **AL**             |
| 0Ch      | Clear input buffer + input    | **AL** (new fn), DS:DX | **AL**             |
| 4Ch      | Terminate program             | **AL**                 | —                  |
