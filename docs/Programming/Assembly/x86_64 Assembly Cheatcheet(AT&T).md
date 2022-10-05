# X86-64 Assembly Cheatsheet (AT&T syntax)
From the book **Programming from the Ground Up** by **Jonathan Bartlett**.
# Simple Assembly Program

    .section .data
    .section .bss
    .section .text
    .globl _start
    _start:
    movl $1, %eax
    movl $0, %ebx
    int $0x80
## data section
used for declaring initialized data or constants. Data does not change at runtime
you can declare various constant values, file names, or buffer size, etc., in this section

    .section .data
## bss section
used for declaring variables

    .section .bss
## text section
program instructions live here

    .section .text
**_start**

    A symbol, which means that it is going to be replaced by something else either during assembly
    or linking. Symbols are generally used to mark locations of programs or data, so you can
    refer to them by name instead of by their location number. _start is a special symbol that
    always needs to be marked with .globl because it marks the location of the start of the
    program. Without marking this location in this way, when the computer loads your program it
    won’t know where to begin running your program.
**.globl**

    Used to mark important symbols in the program.
    Assembler shouldn’t discard this symbol after assembly, because the linker will need it.
**the rest of the program**

    movl $1, %eax - load the value 1 into the %eax register. The %eax register stores system calls.
    movl $0, %ebx - load the value 0 into the %ebx register The %ebx register stores the exit status
                    of the program.
    int $0x80 - int means interrupt. int $0x80 means we're calling the kernel and asking it to do
                something. How does the kernel know what the program wants it to do? It checks the
                contents of the eax register. As for the program above, we loaded the value 1 into the
                %eax register which is a system call for exit. So basically we're telling the kernel to
                exit the program with exit status 0 which is in the %ebx register. An exit status other
                than 0 means there was a problem.
# Data Types
## .byte

    take up one storage location for each number
    limited to numbers between 0 and 255.
## .int

    Ints (which differ from the int instruction) take up two storage locations for each number.
    limited to numbers between 0 and 65535.8
## .long

    take up four storage locations. This is the same amount of space the registers use,
    hold numbers between 0 and 4294967295.
## .ascii

    The .ascii directive is to enter in characters into memory. Characters each take up one
    storage location (they are converted into bytes internally). So, if you gave the directive
    .ascii "Hello there\0", the assembler would reserve 12 storage locations (bytes). The
    first byte contains the numeric code for H, the second byte contains the numeric code for e,
    and so forth. The last character is represented by \0, and it is the terminating character (it
    will never display, it just tells other parts of the program that that’s the end of the
    characters). All of the letters are in quotes.
## .equ

    if you wanted to substitute the name "Jonathan Bartlett", you would do
    .equ NAME, "Jonathan Bartlett"

    so now if you wanted to use the name, instead of typing it , you use $NAME and everywhere
    $NAME appears, it would be substituted by "Jonathan Bartlett

# Instructions and Arithmetic
## movl

    movl SRC,DEST - move whatever is in SRC to DEST
## addl

    addl SRC, DEST - add SRC and DEST, store results in DEST
## subl

    subl SRC, DEST - sub SRC from DEST, store results in DEST


## Comparison instructions
**cmpl**

    compl SRC, DEST
    compare SRC and DEST and set FLAG
**je**

    Jump if the values were equal
**jg**

    Jump if the second value was greater than the first value
**jge**

    Jump if the second value was greater than or equal to the first value
**jl**

    Jump if the second value was less than the first value
**jle**

    Jump if the second value was less than or equal to the first value
**jmp**

    Jump no matter what. This does not need to be preceeded by a comparison.
# Data Accessing Modes
--------------------------
## 1. direct addressing mode
This is done by only using the **ADDRESS_OR_OFFSET** portion. Example:

    movl ADDRESS, %eax
This loads **%eax** with the value at memory address **ADDRESS**.
## 2. indexed addressing mode
This is done by using the **ADDRESS_OR_OFFSET** and the **%INDEX** portion. You can use any
general-purpose register as the index register. You can also have a constant multiplier of 1, 2,
or 4 for the index register, to make it easier to index by bytes, double-bytes, and words. For
example, let’s say that we had a string of bytes as **string_start** and wanted to access the
third one (an index of 2 since we start counting the index at zero), and **%ecx** held the value **2**.
If you wanted to load it into **%eax** you could do the following:

    movl string_start(,%ecx,1), %eax
This starts at **string_start**, and adds **1 * %ecx** to that address, and loads the value into %eax.
## 3. indirect addressing mode
Indirect addressing mode loads a value from the address indicated by a register. For
example, if **%eax** held an address, we could move the value at that address to **%ebx** by doing
the following:

    movl (%eax), %ebx
## 4. base-pointer addressing mode
Base-pointer addressing is similar to indirect addressing, except that it adds a constant value
to the address in the register. For example, if you have a record where the age value is 4
bytes into the record, and you have the address of the record in **%eax**, you can retrieve the
age into %ebx by issuing the following instruction:

    movl 4(%eax), %ebx
## 5. immediate mode
Immediate mode is very simple. It does not follow the general form we have been using.
Immediate mode is used to load direct values into registers or memory locations. For
example, if you wanted to load the **number 12** into **%eax**, you would simply do the
following:

    movl $12, %eax
Notice that to indicate immediate mode, we used a dollar sign in front of the number. If we
did not, it would be direct addressing mode, in which case the value located at memory
**location 12** would be loaded into **%eax** rather than the number 12 itself.

# Commandline Arguments

This description is valid for Linux 32 bit: When a Linux program begins, all pointers to command-line arguments are stored on the stack. The number of arguments is stored at **0(%ebp)**, the name of the program is stored at **4(%ebp)**, and the arguments are stored from **8(%ebp)**.
1. [Writing an X86–64 Assembly Language Program - Part III: Printing Command Line Arguments](https://blog.devgenius.io/writing-an-x86-64-assembly-language-program-648b6005e8e)
2. [Stack Overflow - Linux 64 command line parameters in Assembly](https://stackoverflow.com/questions/3683144/linux-64-command-line-parameters-in-assembly?answertab=trending#tab-top)
3. [How do I get filename, file handling in assembly](https://stackoverflow.com/questions/18945881/how-do-i-get-filename-file-handling-in-assembly)
4. [Reading from a file in assembly](https://stackoverflow.com/questions/3347747/reading-from-a-file-in-assembly)

# Functions
## Simple Function

    .type power, @function
    power:
    (function instructions)
## Function Explained
Declaring the function

    .type FUNCTION_NAME, @function
Then define the value of the function name

    FUNCTION_NAME:
    instructions
Calling the function

    call FUNCTION_NAME

# System Calls

![system calls](./Images/system_calls1.png)
