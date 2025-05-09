## Assembly101 User Input

```
; Program to demonstrate int 0x21 sub function 0x01
number: db 0x00                 ; declare some data to store input into memory
start:
mov AH, 0x01                    ; set up for int 0x21 sub function 0x01
int 0x21                        ; call interrupt for accepting single char
mov byte [offset number],  AL   ; preserve input from AL into memory
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
