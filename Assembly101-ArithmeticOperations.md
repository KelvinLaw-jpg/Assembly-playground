# Arithmetic Operations

### Add

```
; Program to demonstrate add instruction

start:
mov AL, 0xab
add AL, 0x01                    ; AL + 0x01 with result stored in AL, result in 0xac
mov BX, 0xabcd                  
add BX, 0x0bcd                  ; BX + 0x0bcd with result stored in BX
mov byte[0x00], 0xf0
add byte [0x00], 0x0a;          ; [0x00] + 0x0a with result stored in address 0x00
mov word [0x01], 0xfff0
add word [0x01], 0x000c         ; adding word at 0x01 with 0x000c with result going into word starting at 0x01
```

**Add with Carrier flag**


```
; Program to demonstrate carry flag in 8086

start: 
mov AL, 250
add AL, 200             ; AL + 200 stored into AL, note the result overflow and carry flag set
clc                     ; clear the carry flag
mov AH, 20
add AH, 30
mov BX, 0xfff0
add BX, 0x00fb          ; BX + 0x00fb, note the result overflow and carry flag set
```

Sub
```
; Program to demonstrate sub instruction

start:
mov AL, 250
sub AL, 200                 ; AL - 200 with result stored into AL
mov BX, 25000
sub BX, 20000               ; BX - 20000 with results stored into BX
```

Sub negative value

```
; Unsigned Vs. Signed Numbers in Binary
; -----------------------------------------------------------------------------------------------
; Unsigned Byte Range = 0 -> 255 (0x00 -> 0xff)
; Signed Byte Range = -128 -> 0 -> 127 (0x00 -> 0xff)
; Signed Byte Range Positive Side: 0 -> 127 (0x00 -> 0x7F)
; Signed Byte Range Negative Side: -128 -> -1 (0x80 -> 0xff)
; Unsigned Word Range = 0 -> 63,535 (0x0000 -> 0xffff)
; Signed Word Range =  -32,768 -> 0 -> 32,767 (0x0000 -> 0xffff)
; Signed Word Range Positive Side: 0 -> 32,767 (0x0000 -> 0x7fff)
; Signed Word Range Positive Side: -32,768 -> -1 (0x7fff -> 0xffff)
; -----------------------------------------------------------------------------------------------

;Program to demonstrate subtraction with borrow flag

start:
mov AL, -128                ; Note how negative number is stored as signed byte
mov AL, 0x00
mov AL, 128                 ; Note how after zeroing out, same number in hex is stored for 128
mov AL, 200                
sub AL, 250                 ; AL = AL - 250, note the signed result and also the borrow flag (carry flag)
mov BX, 20000               
sub BX, 25000               ; BX = BX - 25000, note the signed result and also the borrow flag
```

**MUL**

```
; Program to demonstrate the mul instruction on the 8086

start:
mov AL, 255
mov BX, 0xffff
mul BX              ; BX * AL with result stored across DL AX
```

**DIV**
```
; Algorithm for div instruction on 8086
; --------------------------------------------------
; When we're dividing by a byte do the following:
; AL = AX / operand
; AH = remainder
; When we're dividing by a word
; AX = DX AX / operand
; DX = remainder
; --------------------------------------------------

; Program to demonstrate div instruction on 8086
start:
mov AX, 0115
mov BL, 10
div BL                      ; AL = AX / BL, remainder into AH
mov AX, 0xabcd
mov DX, 0xa
mov BX, 0xabc3
div BX                      ; AX = DX AX / BX, with remainder into DX
```

**Bitwise Boolean logic**

```
; Program to demonstrate bitwise boolean logic

start:
mov AL, 0b1011
mov AH, 0b1101
and AL, AH                                  ; AL & AH with result into AL

mov AL, 0b1001
mov AH, 0b0101
or AL, AH                                   ; AL || AH with result into AL

mov AL, 0b00001001                          ; !AL with result into AL

; XOR Truth Table
; 0 xor 0 = 0
; 1 xor 0 = 1
; 0 xor 1 = 1
; 1 xor 1 = 0

mov AL, 0b1001
mov AH, 0b0101
xor AL, AH                                  ; AL xor AH with result into AL

xor AL, AL                                  ; xor a register with itself to zero it out
mov AL, 0                                   ; this is the equivalent of xor AL, AL however xor is more efficient
```


