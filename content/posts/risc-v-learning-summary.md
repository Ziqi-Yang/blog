+++
title = "Risc-V Learning Summary"
author = ["Zarkli Leonardo"]
description = "Enjoying and Hating assembly."
date = 2023-01-04T00:00:00+08:00
lastmod = 2023-01-04T00:00:00+08:00
tags = ["risc-v", "assembly"]
categories = ["assembly"]
draft = false
+++

Recently, I self-learned the [cs61c(su20)](https://inst.eecs.berkeley.edu/~cs61c/su20/#by-week) class, which requires you to write a `risc-v` program to make a simple neural network in the project 2. It could be annoying that you write some bug in the assembly since the debug condition is frustrating. After all, through the project I learned a lot about risc-v, especially the calling conventions(at the beginning I haven't paid much attention to it, and it had really caused a lot of problems to my work). After all, I summarized some things that seem important to risc-v assembly coding.


## Calling Conventions {#calling-conventions}

It is very important to assembly coding. To make things work properly, you should follow calling conventions, especially when your project grows bigger and bigger.


### The Caller's Responsibility {#the-caller-s-responsibility}

The caller should manage its temporary variables like `t0`, `a0` and `ra`. Since these variables are used for temporary purposes and in many cases many temporary variables are randomly used, the callee have no responsibility to save the caller's temporary variables, which makes variable management easy.<br />
When the caller calls a function, make sure you have stored those temporary variables which carries information through the processes. The following demo demonstrates the principle:

```asm
# condition: the special value '1' needed to be used after calling a function

## good example
li t0, 1
mv s1, t0 # save t0 to s1, or save it to the stack
jal function
# use s1

## bad example
li t0, 1
jal function # may change t0
# use t0
```


### The Callee's Responsibility {#the-callee-s-responsibility}

The callee should save the long-life variables(like `s0`) before doing its work, and revert them after the work before return. <br />
However, there are many long-life variables(12 variables' name begins with the character 's'), should we save all of them? Absolutely no. You only need to save those variables that will be used in the following callee's work. For example:

```asm
function:
  addi sp, sp, -12 # you can write the size in the final cause sometimes you need to use
                  # more long-life variables than you decided at the beginning
  sw s0, 0(sp)
  sw s1, 4(sp)
  sw ra, 8(sp) # usually save ra along with s-variables is a good practice

  # your work

  lw s0, 0(sp)
  lw s1, 4(sp)
  lw ra, 8(sp) # usually save ra along with s-variables is a good practice
  addi sp, sp, 12
  ret
```


## Multiple File Example {#multiple-file-example}

`main` file

```asm
.import ../../src/write_matrix.s
.import ../../src/utils.s

.data
m0: .word 1, 2, 3, 4, 5, 6, 7, 8, 9
file_path: .asciiz "outputs/test_write_matrix/student_write_outputs.bin"

.text
main:
  la s0, m0 # poiner to int array
  la s1, file_path # string
  # code
```

`function` file

```asm
#define c_printHex 34

# ecall wrappers
.globl print_int, print_str, atoi, sbrk, exit, print_char, fopen, fread, fwrite, fclose, exit2, fflush, ferror, print_hex

# helper functions
.globl file_error, print_int_array, malloc, free, print_num_alloc_blocks, num_alloc_blocks

.data
error_string: .string "This library file should not be directly called!"

.text
# Exits if you run this file
main:
    la a1 error_string
    jal print_str
    li a1 1
    jal exit2

print_int:
  # code
  ret
print_char:
  # code
  ret
```

It should be noticed that the exposing function(label) only exposed its label name and address (my understanding), and its end is the `ret`, however the compiler doesn't know where its end is.


## Pass Command Line Arguments to the Program {#pass-command-line-arguments-to-the-program}

If you don't change a-beginning variables at the beginning, then `a0` is `argc` (`int` type), and `a1` is `argv` (`char **` type). RISC-V handles command line arguments in the same way as C, which means `a1[0]` is the program itself. You can load command line arguments like this:

```asm
# a1: char **, and char * is address, costs for 4 bytes
# 0(a1) stands for the program itself
lw s0, 4(a1)
lw s1, 8(a1)
lw s2, 12(a1)
lw s3, 16(a1)
```


## Ecall - The Environmental Calls {#ecall-the-environmental-calls}

You can refer to [venus - Environmental Call](https://github.com/kvakil/venus/wiki/Environmental-Calls). The starter code for the project2 is also a good example: <https://github.com/61c-teach/su20-proj2-starter/blob/master/src/utils.s>


## Resources {#resources}

-   [resc-v reference](https://inst.eecs.berkeley.edu/~cs61c/sp21/resources-pdfs/riscvcard.pdf) and [large version](https://inst.eecs.berkeley.edu/~cs61c/sp21/resources-pdfs/riscvcard_large.pdf)
-   [unoffical risc-v manual](https://jemu.oscc.cc/)
-   [venus wiki](https://github.com/kvakil/venus/wiki) <br />
    it contains useful information like `Assembler Directives` and `Environmental Calls`
-   [cs61c venus reference](https://inst.eecs.berkeley.edu/~cs61c/sp21/resources/venus-reference) mainly about `venus` tool, but also contains some RISC-V knowledge
