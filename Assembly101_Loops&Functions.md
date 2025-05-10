# Assembly 101 Loops

before setting loop, CX is the counter to do something <3> times
```
;Loops
start:
mov CX, 0x03 ;how many times to print, 3 times now
start_loop:
mov AH, 0xa ;setting up the sub function
mov AL, 0x7 ;char to print in HEX
add AL, 0x30 ;convert AL to ascii, or decimal
int 0x10
```

Basic loop call by sub function 0x03

```
; Program to demonstrate looping manually with JMP and CMP

start:
mov CX, 0x05                            ; Set loop counter to 5
start_loop:
mov AH, 0xa
mov AL, CL
add AL, 0x30                            ; Convert raw to ascii
push CX                                 ; Preserve loop counter by pushing onto stack
mov CX, 0x01                            ; Only print char once in interrupt
int 0x10
pop CX                                  ; Retrieve preserved loop counter from stack
dec CX                                  ; Decrement CX
cmp CX, 0x00                            ; Check if it's zero (condition to stop loop)
jne start_loop                          ; If it's not zero jump back to the start of the loop
```

Basic while loop

```
start:
start_loop:
mov CX,0x01
int 0x21
cmp AL, 0x05 (condition to stop loop: AL=5)
jne start_loop (Jump to start loop if above is not true)
```


```
; Program to demonstrate looping with loop instruction

start:
mov CX, 0x05                            ; Set loop counter to 5
start_loop:
mov AH, 0xa
mov AL, CL
add AL, 0x30                            ; Convert raw to ascii
push CX                                 ; Preserve loop counter by pushing onto stack
mov CX, 0x01                            ; Only print char once in interrupt
int 0x10
pop CX                                  ; Retrieve preserved loop counter from stack
loop start_loop                         ; loop instruction will check if CX is 0, if it's not it will decrement CX and jump to label

; Note that looping can also be accomplished with CMP, JMP and either increment or decrement for more control over loop (see above)
```
