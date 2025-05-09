# Assembly 101 basic

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

When do we use ES vs DS and other segment registers generally?
- DS	Default for most memory operations (data access, mov AX, [var])
- ES	Used in string operations (e.g., movsb, stosw) and some BIOS calls like int 0x10, AH=0x13
- SS	Stack segment (used with SP and BP when accessing stack)
- CS	Code segment (where your code instructions are loaded)

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

    Q1.2. what is 13h? what does h means? also, is the function called 0x10? or function 13h?

    A: h suffix means “This number is in hex.”. So: 13h = 19 in decimal, 0x13 = same thing, just a different syntax (used in C/C++/many modern languages),13 (no suffix) would be decimal 13, which is different.
    So all 3 of the below do the same shit:
    ```
      mov AH, 13h     ; hex
      mov AH, 0x13    ; hex, C-style
      mov AH, 19      ; decimal
    ```
    Is the function called 0x10 or 13h? int 0x10 or int 10h → This is the BIOS video interrupt — like calling a service. AH = 13h (or 0x13) → This tells the BIOS which function under interrupt 0x10 you want. So tgt `int 0x10  +  AH = 13h`, it means “Call the BIOS video interrupt and run sub-function 13h (write string to screen).”. Int 0x10 is just to tell the assembler to interrupt and look for functions to execute, and which function to execute is actually from AH so the function is 0x13 or 13h and int is just to tell it to kick start the function

2. yes, 12 and 0x0C result in the same value in CX: 000C. It means: Store the value 12 into the 16-bit CX register. In the context of AH=0x13, CX tells BIOS how many characters to print from the memory location you pass in. So, since “Hello World!” is 12 characters long, you must load CX = 12.
3. This is the default setting of the chip for function 0x10 (print), normally:
   - DS = Default Data Segment:  Used by default for most data memory access (e.g., for mov AX, [myVar]).
   - ES = Extra Segment:  Used explicitly by some instructions or BIOS functions — not used by default.

In your case, the BIOS function int 0x10 / AH=0x13 is designed to read the string from: ES:BP. So you must set ES, even though DS already points to the right segment.
👉 Even if DS is correct, BIOS doesn't look at DS here — it only looks at ES. This is defined behavior by the BIOS designers.

4. yes

### Pointers

**This program (specifically lea) does not work in the emulator, but it works fine in real 8086 chip**

```
; Program to demonstrate pointers on the 8086

db [0x01, 0xa]                  ; adding data to start of program to make pointers not point at zer0
number: dw 0x1234
string: db "This is a string"   
start:
mov word [0x02], 0xabcd
mov BX, 0x02                    ; creating a pointer in BX pointing to memory address 0x02
mov AX, word [BX]               ; dereferncing pointer BX to mov the contents of the memory address pointed to by BX
mov CX, word [0x02]             ; dereferncing hard coded pointer to mov the contents of a memory address into CX
lea AX, word number
mov DX, offset number
mov BX, offest string
mov AX, word[BX, 3]             ; pointer arithmetic, add 3 to pointer address and then dereference.
```

**Questions I had with this program**
1. db [0x01, 0xa] — What does it mean?
2. For these 2 lines: `mov BX, 0x02 , mov AX, word [BX]` I understand that i want to move value 0x02 to BX and then treat BX as an address and move the value of memory address 0x02 into AX. Does this act called as dereferencing?
3. Is BX the only register that can be used to dereference? howbout others?
4. What does lea do?
5. In `mov AX, word [BX + 3]`, does 3 means 3 bytes? When we say address, I know that we are taking the starting point of that data is stored, but when we say +3 bytes like now, does that assembler automatically knows that end of that data point? so my case BX is storing the address at string, so I guess the address of char "T" since it is the start of the sentence, does the assembler knows the end? I assume it does right? so +3 means adding 3 bytes at the end of the address where char "g" is at.


**Answer**
1. "Define two bytes in memory, the first is 0x01, and the second is 0x0a." It does not mean to repeat 0x01 ten times. It just inserts 0x01 followed by 0x0A (10 in decimal) at the start of the program. This is often done to offset data like you guessed — so the next labels like number: don't start at address 0x0000.
2. Yes, this is called dereferencing: you're saying, "go to the memory at the address in BX and get what's stored there."
3. No, 8086 allows several registers to be used for memory addressing: BX, SI, DI, BP are address registers. You cannot do [AX], [CX], or [DX] — those are general-purpose registers and not valid for memory addressing in this context. So:

    ```
      ✅mov AX, [BX] → valid
      ❌ mov AX, [AX] → invalid on 8086
    ```
4. lea = Load Effective Address. This instruction does not load the value at the label number. It loads the address of number into AX. So if number is at offset 0x02, AX becomes 0x02.

     
    ```c
    int x = 10;
    int *ptr = &x;  // LEA
    int val = x;    // MOV
    ```

   Offset is also similar to lea. `mov DX, offset number` is same as `lea DX, number`. Both load the address @ number into DX
5. When you say BX holds the address of string: BX points to the start of the data (in this case, the character 'T' in the string "This is a string"). BX holds the address of 'T' (start of the string). BX + 3 simply means moving 3 bytes forward from the address in BX. Now, if BX points to 0x00 (address of 'T'): BX + 3 points to 0x03 (the character 's'). `mov AX, word [BX + 3]` means move the 2 bytes of data starting at address 0x03 (the characters 's' and ' ') into AX.

### Stack

```
; Program to demonstrate interacting with the stack on the 8086

number: dw 0x5678
start:
mov AX, 0xabcd              ; can't push immediate to stack so move to AX first
push AX                     ; push AX onto stack, note how stack pointer increments
push AX
pop BX                      ; pop from top of stack into BX
mov CX, 0x1234
push CX
mov word [0xfffa], 0x6789
push CX                     ; note how stack operation overwrites other memory
```

### Challenge

conditions for printing: AH has to be 0x13, CX should hold the length to print, ES is to indicate the segment to print, BP is to indicate the offset to print in that segment

```
; Write a program to store the string "Don't Print This. Print This." starting at 0x20000
; the program should then print out only the "Print This." portion of the string

set 0x2000 ;set the memory page (segment) to 20000
string: db "Don't Print This. Print This." ;declare the Data Directive
start:
mov AH, 0x13 ;Set it to print function
mov CX, 11 ;set CX to 11 char to print
mov BX, 0x2000 ;since we can only use BX to ES and cannot move 0x2000 directly to ES, thus we need this line
mov ES, BX ;move 0x2000 to ES
mov BP, 18 ;start printing at offset 18, which is where "P" at in "Pint This."
int 0x10 ;interupt and call the funtion

```


