## Assembly101 User Input

**single char input**
The code below shows the 0x21 sub functions mode, depending on AH, it does diffent things, 0x01 means it will read a single char from user and store the byte to AL.

```
; Program to demonstrate int 0x21 sub function 0x01
number: db 0x00                 ; declare some data to store input into memory
start:
mov AH, 0x01                    ; set up for int 0x21 sub function 0x01
int 0x21                        ; call interrupt for accepting single char
mov byte [offset number],  AL   ; preserve input from AL into memory
```

**Code Challenge**
 my attempt - ok but not good since I didn't manage to use sub address1, address2 to calculate the length of the string. So my CX is hardcoded and will cannot change.
```
string1: db "Enter a number between 0-9: "
string2: db "The number you input is: "
number: db 0x00
start:
mov AH, 0x13
mov CX, 28
mov BX, 0x0000
mov ES, BX
mov BP, offset string1
int 0x10

;take user input
mov AH, 0x01
int 0x21
mov byte [offset number], AL

;print the input number
mov AH, 0x13
mov CX, 26
mov BP, offset string2
int 0x10
```

Answer
```
; Write a program to ask a user to input a number between 0-9 and then print that number back
;---------------------------------------------------------------------------------------------------
; After accepting the input, it should be returned by printing the following string
; "The number you input is: " followed with the number input
; The second output including the number should all be on the same line printed with one interrupt
; --------------------------------------------------------------------------------------------------

message1: db "Pretty please input a number between 0-9"
message2: db "The number you input is definitely: "
char: db 0x00
start:
; calculate the length of the first string and put it into CX
mov CX, offset message2
sub CX, offset message1
; Print out the first message
mov AH, 0x13
mov BX, 0
mov ES, BX
mov BP, offset message1
int 0x10
; Accept user input of char and store into AL
mov AH, 1
int 0x21
; Move user input from AL into memory at offset of char
mov byte [offset char], AL
; Calculate the length of the second string plus the input char
mov CX, offset char
sub CX, offset message2
add CX, 1
; Print out the second message including the input char
mov AH, 0x13
mov BX, 0
mov ES, BX
mov BP, offset message2
int 0x10
```


**takes multiple char input**

```
; Program to demonstrate int 0x21 sub function 0xa by accepting user input into string and then print it back

db [0x00,6]                 ; Adding data to start of program so pointers aren't at zero
buffer: db 12 db[0x00, 13]  ; buffer to store max of 12 input chars

start:
; Take user input via int 0x21 sub function 0xa and store it into the buffer
mov AH, 0xa
mov DX, offset buffer
int 0x21
; Print out the contents of the buffer
mov AH, 0x13
mov BL, offset buffer
mov CL, byte [BX, 1]        ; derefernce pointer to take second byte which contains the amount of chars input
mov BX, 0
mov ES, BX
mov BP, offset buffer
add BP, 2
mov DX, 0
int 0x10
```


## Conditional Statements

```
; Program to demonstrate how the cmp instruction works

start:
mov AL, 10
mov BL, 3
cmp BL, AL                      ; Compare AL to BL, note how the various flags change when comparing different values
```

**Jumps**
```
; Program to demonstrate jmp instructions by accepting a user input between 0-9
; Then comparing it against 5 and printing a message

message: db "The number input is above 5"
null: db 0x00
start:
; Accepting User input
mov AH, 1
int 0x21
sub AL, 0x30                                    ; Convering from ascii to raw
; Checking user input to see if it is above 5
; This is the equivalent of if(user_input > 5)
cmp AL, 5
ja printmessage
jmp end
; Printing message
printmessage:
mov AH, 0x13
mov CX, offset null
sub CX, offset message
mov BP, offset message
int 0x10
end:
```


**Chaining JMPS**

```
; Program to demonstrate jmp instructions
;------------------------------------------------------------------------------------------
; By accepting a user input between 0-9 and then printing one of the following messages
; Message1: The number input is less than 5
; Message2: The number input is equal to 5
; Message3: The number input is greater than 5
; Pseudo code for high level language
; if (input < 5) then print message1
; else if (input ==5) then print message2
; else print Message3
;------------------------------------------------------------------------------------------

message1: db "The number input is less than 5"
message2: db "The number input is equal to 5"
message3: db "The number input is greater than 5"
null: db 0x00
start:
; Accepting User input
mov AH, 1
int 0x21
sub AL, 0x30                                        ; Converting ascii to raw
; Checking user input to see if it is below 5
; This is the equivalent of if(user_input < 5)
cmp AL, 5
jnb notbelow5                                       ; If it's not below 5 jump over print, if it is below 5 print message1
mov CX, offset message2
sub CX, offset message1
mov BP, offset message1                             
jmp printmessage                                    
notbelow5:
; Checking user input to see if equals 5
; This is the equivalent of else if(user_input == 5)
cmp AL, 5
jne above5                                           ; If it's not equal jump over print, if it is equal print message2
mov CX, offset message3
sub CX, offset message2
mov BP, offset message2                             
jmp printmessage
; This is the equivalent of else, if we get here it must be above 5 so print message3
above5:
mov CX, offset null
sub CX, offset message3
mov BP, offset message3
; Interrupt to print the message
printmessage:
mov AH, 0x13
int 0x10
```
