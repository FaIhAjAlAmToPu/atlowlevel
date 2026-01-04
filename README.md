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

Code examples:
| AH (hex) | Code Example                                                   | Description                                                                                        |
| -------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| 01h      | `mov ah,01h`<br>`int 21h`<br>`; AL has char`                   | Read a character from keyboard **with echo**. Character is returned in `AL`.                       |
| 02h      | `mov ah,02h`<br>`mov dl,'A'`<br>`int 21h`                      | Display a single character in `DL` on screen.                                                      |
| 06h      | `mov ah,06h`<br>`mov dl,FFh`<br>`int 21h`                      | Direct console I/O. If `DL=FFh`, reads a character. Returns `AL` and `ZF`.                         |
| 07h      | `mov ah,07h`<br>`int 21h`<br>`; AL has char`                   | Read a character from keyboard **without echo**.                                                   |
| 08h      | `mov ah,08h`<br>`int 21h`<br>`; AL has char`                   | Another function to read a character **without echo**.                                             |
| 09h      | `mov ah,09h`<br>`mov dx,offset msg`<br>`int 21h`               | Display a `$`-terminated string at DS:DX. Example: `msg db 'Hello$',0`                             |
| 0Ah      | `mov ah,0Ah`<br>`mov dx,offset buf`<br>`int 21h`               | Buffered string input. The string is stored in the memory buffer. Example: `buf db 10,0,10 dup(?)` |
| 0Bh      | `mov ah,0Bh`<br>`int 21h`<br>`; AL=0/FF`                       | Check keyboard status. AL = 0 if no key pressed, FF if key is available.                           |
| 0Ch      | `mov ah,0Ch`<br>`mov dl,0`<br>`mov dx,offset buf`<br>`int 21h` | Clear input buffer and read new input. Returns AL.                                                 |
| 4Ch      | `mov ah,4Ch`<br>`mov al,00`<br>`int 21h`                       | Terminate program and return AL as exit code.                                                      |
