# Assembly 101 Loops

Basic loop call by sub function 0x03
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



