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




Lods & Stos Challenge

```
; Number Addition Challenge
;------------------------------------------------------
; Ask the user to input two values between 0-9999
; Accept two user inputs
; Add the two user inputs together into a memory address
; The program can end with the sum in the memory address
; Hint: remember that each digit is stored in ascii
;------------------------------------------------------
; Data section
value1: db 0x04 db [0x00,0x05]
value2: db 0x04 db [0x00,0x05]
string1: db "Please Enter Value 1"
string2: db "Please Enter Value 2"
null: db 0x0
sum: dw 0x0000
; Code start here
start:
; Print Message 1
mov AH, 0x13
mov CX, offset string2
sub CX, offset string1
mov BP, offset string1
int 0x10
; Accept Value 1
mov AH, 0xa
mov DX, offset value1
int 0x21
; Print Message 2
mov AH, 0x13
mov CX, offset null
sub CX, offset string1
mov BP, offset string1
int 0x10
; Accept Value 2
mov AH, 0xa
mov DX, offset value2
int 0x21
; Convert Value 1 contents from ascii to decimal
mov BL, offset value1
mov CL, byte [BX,1]                                 ; Put length of value 1 array into CX for looping
mov SI, offset value1
add SI, 0x02
convert_to_decimal_1:
lods byte                                           ; Load byte of ascii into AL
sub AL, 0x30                                        ; Convert from ascii to raw number
push CX                                             ; Preserve CX for outerloop
dec CX                                              ; Counter needs to decrement to account for first number being in 0 position
mov AH, 0x00                                        ; Zero out AH so it's empty for math
mov BX, AX                                          ; Put AX into BX so we can use AL to hold the smaller mul number of 10
cmp CX, 0x00                                        ; Check if CX is 0, if it we don't need to do exponent bc it's last digit
je after_exp_1                                      ; If it is 0 jump over exponent
exp_loop_1:
mov AX, 10                                          ; Mov 10 into AL so we can multiply
mul BX                                              ; Multiply the contents of BX by 10
mov BX, AX                                          ; The results of mul will be in AX, mov back to BX to mul again if needed
loop exp_loop_1                                     ; Loop back and do exponent again if needed
after_exp_1:
add word sum, BX                                    ; Add the result to the total sum
pop CX                                              ; Pop the initial CX back for the outer loop to count through remaining digits
loop convert_to_decimal_1                           ; Loop back for next digit if required
; Convert Value 2 contents from ascii to decimal, and add them to sum
mov BL, offset value2
mov CL, byte [BX,1]
mov SI, offset value2
add SI, 0x02
convert_to_decimal_2:
lods byte
sub AL, 0x30
push CX
dec CX
mov AH, 0x00
mov BX, AX
cmp CX, 0x00
je after_exp_2
exp_loop_2:
mov AX, 10
mul BX
mov BX, AX
loop exp_loop_2
after_exp_2:
add word sum, BX
pop CX
loop convert_to_decimal_2
```



