---
title: Tail recursion in C
layout: post
categories: blog
---
While [tail recursion](https://en.wikipedia.org/wiki/Tail_call) is the basis of iteration in most functional languages, in procedural ones it's kind of forgotten.

That said C compilers are trying their best in detecting it and optimising tail calls. So let's give it a go. Here's a really simple function with tail recursion in C.
```c
#include <stdio.h>

void fun(int n) {
    printf("%d\n", n);
    fun(n + 1);
}

int main() {
    fun(0);
    return 0;
}
```
> infinite.c

Now let's compile it:
```
$ cc -o infinite infinite.c
```
And run it:
```
$ ./infinite
0
1
2
3
...
261997
[1]    7175 segmentation fault  ./infinite
```

Well, that wasn't much of a surprise. `fun` never returns so the stack will eventually overflow (return addresses are being pushed there). But let's try some optimisation (just a little bit):
```
$ cc -O1 -o infinite infinite.c
```
Now when we run it, it will just spin and print out the numbers happily until the end of times.

So what happened here? The compiler is smart enought to figure out the tail recursion and it used a simple jump instead of a method call.

How do we know that? Well, let's check the assembly...
```
$ cc -O0 -S -o infinite0.S infinite.c
$ cc -O1 -S -o infinite1.S infinite.c
```
`-S` option will run the preprocessor, parser, LLVM generation, optimisation stages, target-specific code generation and produce an assembly file.

Clang produces the following for me with the `-O0` option:
```asm
	.globl	_fun                    ## -- Begin function fun
	.p2align	4, 0x90
_fun:                                   ## @fun
	.cfi_startproc
## BB#0:
	pushq	%rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Lcfi2:
	.cfi_def_cfa_register %rbp
	subq	$16, %rsp
	leaq	L_.str(%rip), %rax
	movl	%edi, -4(%rbp)
	movl	-4(%rbp), %esi
	movq	%rax, %rdi
	movb	$0, %al
	callq	_printf
	movl	-4(%rbp), %esi
	addl	$1, %esi
	movl	%esi, %edi
	movl	%eax, -8(%rbp)          ## 4-byte Spill
	callq	_fun
	addq	$16, %rsp
	popq	%rbp
	retq
	.cfi_endproc
                                        ## -- End function
```
As expected there's no optimisation, the assembly represents the C logic pretty much directly. Notice the `callq _fun` - it means that it will push a return address onto stack and crash with enough of those.

Now let's compare it with the optimised version (`-O1`):
```asm
	.globl	_fun                    ## -- Begin function fun
	.p2align	4, 0x90
_fun:                                   ## @fun
	.cfi_startproc
## BB#0:
	pushq	%rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Lcfi2:
	.cfi_def_cfa_register %rbp
	pushq	%r14
	pushq	%rbx
Lcfi3:
	.cfi_offset %rbx, -32
Lcfi4:
	.cfi_offset %r14, -24
	movl	%edi, %ebx
	leaq	L_.str(%rip), %r14
	.p2align	4, 0x90
LBB0_1:                                 ## =>This Inner Loop Header: Depth=1
	xorl	%eax, %eax
	movq	%r14, %rdi
	movl	%ebx, %esi
	callq	_printf
	incl	%ebx
	jmp	LBB0_1
	.cfi_endproc
                                        ## -- End function
```
Now, the whole magic happens under `LBB0_1` label:
```asm
LBB0_1:
	xorl	%eax, %eax
	movq	%r14, %rdi
	movl	%ebx, %esi
	callq	_printf
	incl	%ebx
	jmp	LBB0_1
```
It only contains of preparing the arguments for `printf` call, increment of `%ebx` register and an unconditional jump back to `LBB0_1` label - our infinite loop. No pushing onto stack, no stackoverflow, no end to the happy infinite loop.

# Factorial
Let's try something slightly more complicated. A text book example of recursion is [the factorial function](https://en.wikipedia.org/wiki/Factorial). I'll use this simple implementation:
```c
#include <stdio.h>

int fac(int n) {
    if (n < 1) {
        return 1;
    } else {
        return n * fac(n - 1);
    }
}

int main() {
    printf("%d\n", fac(4));
    return 0;
}
```
This implementation does not have a tail call. The recursive call is not the last operation in `fac` - it still needs to do a multiplication before returning. It can be modified to include an extra argument, accumulator, to make it a tail recursion but I'll keep it as is for our test.

Let's check what the compiler will make out of it. Here's the assembly for `fac` generated with `-O1`:
```asm
	.globl	_fac                    ## -- Begin function fac
	.p2align	4, 0x90
_fac:                                   ## @fac
	.cfi_startproc
## BB#0:
	pushq	%rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset %rbp, -16
	movq	%rsp, %rbp
Lcfi2:
	.cfi_def_cfa_register %rbp
                                        ## kill: %EDI<def> %EDI<kill> %RDI<def>
	movl	$1, %eax
	testl	%edi, %edi
	jle	LBB0_2
	.p2align	4, 0x90
LBB0_1:                                 ## =>This Inner Loop Header: Depth=1
	imull	%edi, %eax
	cmpl	$1, %edi
	leal	-1(%rdi), %ecx
	movl	%ecx, %edi
	jg	LBB0_1
LBB0_2:
	popq	%rbp
	retq
	.cfi_endproc
                                        ## -- End function
```
First thing to notice: there's no `callq _fac`! Clang was smart enough to figure out the iteration and change it into a loop under `LBB0_1` label:
```asm
LBB0_1:
	imull	%edi, %eax
	cmpl	$1, %edi
	leal	-1(%rdi), %ecx
	movl	%ecx, %edi
	jg	LBB0_1
```

# Summary
`-S` option is really useful if you are interested what happens to your code with different optimisation levels.

On one hand tail recursion seems like a valid option for iteration in procedural languages like C if the compiler is mature enough.

On the other it's not guaranteed on the language level [like in Scheme](http://www.r6rs.org/final/html/r6rs/r6rs-Z-H-8.html#node_sec_5.11). So you'll never know if some change in your code or an upgrade to the compiler itself will break your application.

Your mileage may vary.

# Note
In my system `cc` is Clang:
```
$ cc --version
Apple LLVM version 9.1.0 (clang-902.0.39.2)
Target: x86_64-apple-darwin17.7.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
```
