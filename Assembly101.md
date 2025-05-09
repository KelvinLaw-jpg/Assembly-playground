# Assembly 101

8086 chip
-
- 4 general purpose reg: AX, BX, CX, DX All with High and Low byte
- 3 special purpose reg (segments): SS, DS, ES
- you can only move data into special purpose reg through general purpose reg (usually specific to the chip, in this case the 8086 family chips)
- byte and word are keywords specifying how big are the bytes to read or to move word = 2 bytes
- DS controls the memory page. Memory segments to be specific, imagine a very long piece of paper roll, it will be very hard to read and write to it in the middle, so breaking it down to pages is what
  DS does, basically the memory page reg
- Little Endianness


### Littel Endianness Example
```
; Profram to demonstrate endianness

start:
mov word[0x00], 0xabcd          ; note the order of 0xabcd in the memory
mov AX, word [0x00]             ; note the order of 0xabcd when moved back into the register 
```

you will the byte in memories are cd ab instead but when it move back to AX reg, it will become ab cd again.


### Data Directive

- Data Directive tells the program to load in data on assembling, before the program even runs.
- set is the keyword to set DS
- byte1 is a label, like variables we define in high level languages

```
; Program to demonstrate data directives

start:
set 0x1000                  ; move data segment up to start at 0x10000
db 0xab              ; data directive with label to store 0xab
dw 0xabcd            ; data directive with label to store 0xabcd
string1: db "Hello World!"  ; data directive to store string (ascii chars in byte array)

start:
mov AL, byte [0x00]          ; use label to move data at memory address for label into AL
mov BX, word [0x01]          ; use label to move word of data at memory address for label into BX
```
 same as below
```
; Program to demonstrate data directives

start:
set 0x1000                  ; move data segment up to start at 0x10000
byte1: db 0xab              ; data directive with label to store 0xab
word1: dw 0xabcd            ; data directive with label to store 0xabcd
string1: db "Hello World!"  ; data directive to store string (ascii chars in byte array)

start:
mov AL, byte byte1          ; use label to move data at memory address for label into AL
mov BX, word word1          ; use label to move word of data at memory address for label into BX
```

### interupts - writing Hello World

```
; Program to output Hello World! on the 8086

string: db "Hello World!"           ; Storing string in data
start:
mov AH, 0x13                        ; setting up AH for int0x10 sub function 0x13 (print multiple chars)
mov CX, 12                          ; setting up CX with number of chars in "Hello World!"
mov BX, 0x0000                      ; zeroing out BX as temp to move into esi
mov ES, BX                          ; zeroing out ES from BX
mov BP, offset string               ; moving pointer to first char in string into BP
int 0x10                            ; interrupt to print out string
```

**Quesstions I had with this program**
1. if it is a condition to set AH to 0x13 for printing multiple characters, can it be 0x12 or 0x11, is it preset by the manufacturer?
2. is mov CX, 12 means move 12 in decimal to CX? I thought registers only work with hex, also what does it mean to move 12 to CX reg, also why 12, can it be 0xc, 0xc should be the same as 12 right?
3. why do we need to make sure ES is 0x0000, would it affect the output if ES is not 0x0000, and what does ES do?
4. if int 0x10 (print function) is basically going to print from where the BP is set?

**Answer**
1. AH=0x13 is specifically chosen because it tells BIOS interrupt 0x10 to execute subfunction 13h, which is defined by the BIOS developers (i.e., IBM PC BIOS and compatible standards). Each value of AH corresponds to a different sub-function of the int 0x10 interrupt. Some examples:
  - AH=0x0E – Teletype output (print a single character)
  - AH=0x13 – Print multiple characters using a string
  - AH=0x12 – Set VESA video mode (different function entirely)

    Q1.1. So basically when we use int 0x10, the assembler will look at when is AH=0x13 and start from there, and CX controls how many chars to print?
    
    A: Yes — exactly. It basically means "Hey BIOS! I'm calling interrupt 0x10, and I want you to run function 13h (write string)."

2. yes, 12 and 0x0C result in the same value in CX: 000C. It means: Store the value 12 into the 16-bit CX register. In the context of AH=0x13, CX tells BIOS how many characters to print from the memory location you pass in. So, since “Hello World!” is 12 characters long, you must load CX = 12.
3. This is the default setting of the chip for function 0x10 (print), normally:
   - DS = Default Data Segment:  Used by default for most data memory access (e.g., for mov AX, [myVar]).
   - ES = Extra Segment:  Used explicitly by some instructions or BIOS functions — not used by default.

In your case, the BIOS function int 0x10 / AH=0x13 is designed to read the string from: ES:BP. So you must set ES, even though DS already points to the right segment.
👉 Even if DS is correct, BIOS doesn't look at DS here — it only looks at ES. This is defined behavior by the BIOS designers.

4. yes
