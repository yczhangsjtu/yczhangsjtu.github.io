# Linux版IOLI-crackme破解

## 下载

* 地址 [http://dustri.org/b/files/IOLI-crackme.tar.gz](http://dustri.org/b/files/IOLI-crackme.tar.gz)
* 解压到任意目录下

```
$ cd /path/to/directory
$ tar xf /path/to/IOLI-crackme.tar.gz
```

## 运行

* 程序为32位，在64位Linux环境下可能无法运行。如果提示无法找到可执行文件，请安装对i386的支持。

```Shell
$ sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
```

* 进入`bin-linux`文件夹，运行`crackme0x00`，随便输入一个密码回车。

```Shell
$ cd IOLI-crackme/bin-linux
$ ./crackme0x00
IOLI Crackme Level 0x00
Password: abc
Invalid Password!
```

## 破解

### 破解 crackme0x00

运行 `gdb`。使用`-q`参数告诉程序不要输出问候语句。

```
$ gdb -q ./crackme0x00
Reading symbols from ./crackme0x00...(no debugging symbols found)...done.
(gdb) 
```

反汇编`main`函数

```
(gdb) disas main
Dump of assembler code for function main:
   0x08048414 <+0>:     push   %ebp
   0x08048415 <+1>:     mov    %esp,%ebp
   0x08048417 <+3>:     sub    $0x28,%esp
   0x0804841a <+6>:     and    $0xfffffff0,%esp
   0x0804841d <+9>:     mov    $0x0,%eax
   0x08048422 <+14>:    add    $0xf,%eax
   0x08048425 <+17>:    add    $0xf,%eax
   0x08048428 <+20>:    shr    $0x4,%eax
   0x0804842b <+23>:    shl    $0x4,%eax
   0x0804842e <+26>:    sub    %eax,%esp
   0x08048430 <+28>:    movl   $0x8048568,(%esp)
   0x08048437 <+35>:    call   0x8048340 <printf@plt>
   0x0804843c <+40>:    movl   $0x8048581,(%esp)
   0x08048443 <+47>:    call   0x8048340 <printf@plt>
   0x08048448 <+52>:    lea    -0x18(%ebp),%eax
   0x0804844b <+55>:    mov    %eax,0x4(%esp)
   0x0804844f <+59>:    movl   $0x804858c,(%esp)
   0x08048456 <+66>:    call   0x8048330 <scanf@plt>
   0x0804845b <+71>:    lea    -0x18(%ebp),%eax
   0x0804845e <+74>:    movl   $0x804858f,0x4(%esp)
   0x08048466 <+82>:    mov    %eax,(%esp)
   0x08048469 <+85>:    call   0x8048350 <strcmp@plt>
   0x0804846e <+90>:    test   %eax,%eax
   0x08048470 <+92>:    je     0x8048480 <main+108>
   0x08048472 <+94>:    movl   $0x8048596,(%esp)
   0x08048479 <+101>:   call   0x8048340 <printf@plt>
   0x0804847e <+106>:   jmp    0x804848c <main+120>
   0x08048480 <+108>:   movl   $0x80485a9,(%esp)
   0x08048487 <+115>:   call   0x8048340 <printf@plt>
   0x0804848c <+120>:   mov    $0x0,%eax
   0x08048491 <+125>:   leave  
   0x08048492 <+126>:   ret    
End of assembler dump.
(gdb)
```

观察发现，该程序的过程非常简单，没有在`main`函数中调用其他用户过程，首先调用了两次`printf`函数，一次`scanf`函数，一次`strcmp`函数，最后有两个`printf`函数，其中第一个下面紧跟着`jmp`指令跳转到程序结束。可以推测过程如下：
1. 前两个`printf`函数分别打印了`IOLI Crackme Level 0x00`和`Password: `
2. 由`scanf`读入用户输入
3. 用`strcmp`与写在程序当中的密码作比较
4. 根据比较结果执行最后两个`printf`中的某一个。

虽然逻辑很简单，我们依然按照严格的逻辑过程来追本溯源，因为本教程的目的是介绍用`gdb`进行逆向的基本思过程，旨在熟悉基础的`gdb`指令。

我们的最终目的是让程序输出类似“Password ok”这样的结果，避免输出“Invalid Password!“。因此我们首先观察最后两个`printf`函数，确定哪个输出了成功提示，哪个输出了失败提示。决定该函数输出内容的是传给它的参数。在i386中，传给调用函数的参数通常放在当前函数的栈顶，即`$esp`寄存器所指向的内存附近。这里两个`printf`函数的参数都只有一个，因此调用过程比较简单。

```
   0x08048472 <+94>:    movl   $0x8048596,(%esp)
   0x08048479 <+101>:   call   0x8048340 <printf@plt>
```

第一个`movl`指令将一个立即数（这里是一个指针）放入`$esp`寄存器指向的地址中，也就是栈顶，然后调用`printf`函数。因为`printf`函数的第一个参数永远是字符串，即指向字符串第一个字符的指针，用`x`命令查看一下这个指针所指位置的内容。

```
(gdb) x 0x8048596
0x8048596:	0x61766e49
(gdb)
```

该指令将以给定内存地址开头的一个字（在i386中为四字节）输出来，按照人的阅读习惯将低地址处的字节内容放在后面，因此以该地址开头的四个字节分别是0x49, 0x6e, 0x76和0x61，刚好对应了a, v, n和I的ASCII码。当然，`x`命令非常强大，可以将内存地址处的内容以任意形式输出。加上`/s`将内存内容以字符串形式输出。

```
(gdb) x/s 0x8048596
0x8048596:	"Invalid Password!\n"
(gdb)
```

同样，我们检查一下最后一个`printf`输出的内容。

```
(gdb) x/s 0x80485a9
0x80485a9:	"Password OK :)\n"
(gdb)
```

所以，我们的目标就是让程序执行最后一个`printf`函数，而避免执行前一个。观察最后这段代码

```
   0x08048469 <+85>:    call   0x8048350 <strcmp@plt>
   0x0804846e <+90>:    test   %eax,%eax
   0x08048470 <+92>:    je     0x8048480 <main+108>
   0x08048472 <+94>:    movl   $0x8048596,(%esp)
   0x08048479 <+101>:   call   0x8048340 <printf@plt>
   0x0804847e <+106>:   jmp    0x804848c <main+120>
   0x08048480 <+108>:   movl   $0x80485a9,(%esp)
   0x08048487 <+115>:   call   0x8048340 <printf@plt>
   0x0804848c <+120>:   mov    $0x0,%eax
   0x08048491 <+125>:   leave  
   0x08048492 <+126>:   ret
```

我们发现位置`<+92>`处的指令跳转到最后一个`printf`函数前。该跳转为条件跳转，跳转条件为CPU中的`Z`标志被置为1。前一指令为`test %eax,%eax`，即如果`$eax`寄存器为0则将`Z`标志设置为1。在i386中，`$eax`寄存器被用来存储函数返回值，因此，这段代码中，只有当`strcmp`返回0时，该函数会按我们的期望跳过输出`Invalid Password!`的指令。

我们再观察`strcmp`的参数输入。

```
   0x0804845b <+71>:    lea    -0x18(%ebp),%eax
   0x0804845e <+74>:    movl   $0x804858f,0x4(%esp)
   0x08048466 <+82>:    mov    %eax,(%esp)
   0x08048469 <+85>:    call   0x8048350 <strcmp@plt>
```

这段代码将`strcmp`所需的两个参数（字符指针）分别放入`$esp`指向的内存地址和`$esp+4`位置的内存。放入`$esp+4`位置的地址是立即数（即该字符串为编译进程序中的字符串常量），我们查看一下它的内容。

```
(gdb) x/s 0x804858f
0x804858f:	"250382"
(gdb)
```

如果另一个参数就是`scanf`从标准输入中读入的字符串，那么这里的`250382`即为我们所需的密码了。另一个参数通过`lea`指令放入`$eax`寄存器，又紧接着`mov`进`$esp`所指向的内存地址。这里`lea`指令和`mov`指令类似，唯一的区别就是把地址而不是内容赋值给目标寄存器，在这里其实等价于`mov %ebp,%eax$`与`add -0x18,%eax`，即把`$ebp`指向的内存地址的前18个位置的地址存入`$eax`寄存器。

这个地址是不是`scanf`读入的字符串存放的的地址呢？我们再看一下`scanf`的调用过程。

```
   0x08048448 <+52>:    lea    -0x18(%ebp),%eax
   0x0804844b <+55>:    mov    %eax,0x4(%esp)
   0x0804844f <+59>:    movl   $0x804858c,(%esp)
   0x08048456 <+66>:    call   0x8048330 <scanf@plt>
```

可以看出，这个参数以相同的方式传给了`scanf`函数，只不过放在了`$esp+4`位置，即为第二个参数。而第一个参数即格式化字符串的内容为

```
(gdb) x/s 0x804858c
0x804858c:	"%s"
(gdb)
```

所以这段代码确实是将用户输入的字符串读入`$ebp-0x18`所指向的内存地址。该地址又被传入`strcmp`函数与`0x804858f`所指向的字符串（即`"250382"`）进行比较。如果`strcmp`返回值为0，则跳转到最后一个`printf`调用前，输出`"Password OK :)\n"`。

我们试着使用这个密码。首先在`gdb`中运行

```
(gdb) r
Starting program: /path/to/IOLI-crackme/bin-linux/crackme0x00 
IOLI Crackme Level 0x00
Password: 250382
Password OK :)
[Inferior 1 (process 21934) exited normally]
(gdb)
```

运行成功。然后退出`gdb`，运行`./crackme0x00`程序。

```
(gdb) q
$ ./crackme0x00
IOLI Crackme Level 0x00
Password: 250382
Password OK :)
```

当然，实际破解中会进行大量的猜测，对这么个小程序，并不必这么繁琐。根据代码可以直接猜出`strcmp`会接受真正密码作为参数，直接查看内存地址即可。更简单地，可以用`strings`命令代替`gdb`，输出文件中包含的所有ASCII字符串，将其中看起来像密码的内容拿出来试验。

```
$ strings crackme0x00
GLIBC_2.0
PTRh
IOLI Crackme Level 0x00
Password: 
250382
Invalid Password!
Password OK :)
GCC: (GNU) 3.4.6 (Gentoo 3.4.6-r2, ssp-3.4.6-1.0, pie-8.7.10)
```

其中看起来最像密码的就是`250382`。
只不过，这个程序是一个简单的样例，一般程序没有这么简单就能猜出来的。因此，接下来对其他样例的破解说明只作基本的描述并进行大量猜测，仅在涉及新知识点时略作说明。

### 破解 crackme0x01

该程序给出的界面和 crackme0x00 类似。同样，用`gdb`打开，查看`main`函数汇编代码。

```
$ gdb -q ./crackme0x01
Reading symbols from ./crackme0x01...(no debugging symbols found)...done.
(gdb) disas main
Dump of assembler code for function main:
   0x080483e4 <+0>:   push   %ebp
   0x080483e5 <+1>:   mov    %esp,%ebp
   0x080483e7 <+3>:   sub    $0x18,%esp
   0x080483ea <+6>:   and    $0xfffffff0,%esp
   0x080483ed <+9>:   mov    $0x0,%eax
   0x080483f2 <+14>:  add    $0xf,%eax
   0x080483f5 <+17>:  add    $0xf,%eax
   0x080483f8 <+20>:  shr    $0x4,%eax
   0x080483fb <+23>:  shl    $0x4,%eax
   0x080483fe <+26>:  sub    %eax,%esp
   0x08048400 <+28>:  movl   $0x8048528,(%esp)
   0x08048407 <+35>:  call   0x804831c <printf@plt>
   0x0804840c <+40>:  movl   $0x8048541,(%esp)
   0x08048413 <+47>:  call   0x804831c <printf@plt>
   0x08048418 <+52>:  lea    -0x4(%ebp),%eax
   0x0804841b <+55>:  mov    %eax,0x4(%esp)
   0x0804841f <+59>:  movl   $0x804854c,(%esp)
   0x08048426 <+66>:  call   0x804830c <scanf@plt>
   0x0804842b <+71>:  cmpl   $0x149a,-0x4(%ebp)
   0x08048432 <+78>:  je     0x8048442 <main+94>
   0x08048434 <+80>:  movl   $0x804854f,(%esp)
   0x0804843b <+87>:  call   0x804831c <printf@plt>
   0x08048440 <+92>:  jmp    0x804844e <main+106>
   0x08048442 <+94>:  movl   $0x8048562,(%esp)
   0x08048449 <+101>: call   0x804831c <printf@plt>
   0x0804844e <+106>: mov    $0x0,%eax
   0x08048453 <+111>: leave  
   0x08048454 <+112>: ret        
End of assembler dump.
```

观察发现，`<+78>`处的指令跳转到`<+94>`位置，之前的比较指令`cmpl $0x149a,-0x4(%ebp)`将内存地址`$ebp-0x4`处存的整数与`0x149a`进行比较。猜测这次`scanf`读的是一个整数，地址为`$ebp-0x4`。我们可以把传给`scanf`的`0x804854c`处的模式字符串输出验证一下

```
(gdb) x/s 0x804854c
0x804854c:	"%d"
(gdb)
```

把`0x149a`转化为十进制是`5274`，我们把这个当做密码输入试验

```
(gdb) r
Starting program: ~/IOLI-crackme/bin-linux/crackme0x01 
IOLI Crackme Level 0x01
Password: 5274
Password OK :)
[Inferior 1 (process 25838) exited normally]
(gdb)
```

### 破解 crackme0x02

同样，首先观察`main`函数。这里不再给出`main`函数的具体内容。

跳转到第二个`printf`前的语句在`<+109>`位置，之前的比较语句如下

```
mov    -0x4(%ebp),%eax
cmp    -0xc(%ebp),%eax
jne    0x8048461 <main+125>
```

即将内存`$ebp-0xc$`处的值和`$ebp-0x4$`处的值进行比较。这之前使用许多指令来计算`$ebp-0xc`处内存的值，但从`scanf`函数到这里为止，`$ebp-0x4`处的值从来没有变过。并且地址`$ebp-0x4`在`scanf`函数调用前通过

```
lea    -0x4(%ebp),%eax
mov    %eax,0x4(%esp)
```

指令放在`scanf`第二个参数的位置上，而通过

```
movl   $0x804856c,(%esp)
```
指令传入的模式字符串为

```
(gdb) x/s 0x804856c
0x804856c:	"%d"
(gdb)
```

因此我们需要输入一个整数，使得它与程序计算出的值相等。我们不必通过观察代码来手动计算这个整数到底是多少。我们在`cmp`指令处下一个断点

```
(gdb) b *main+106
Breakpoint 1 at 0x804844e
```
然后运行程序到断点处

```(gdb) r
Starting program: /path/to/IOLI-crackme/bin-linux/crackme0x02 
IOLI Crackme Level 0x02
Password: 123

Breakpoint 1, 0x0804844e in main ()
```

再观察`$ebp-0xc`处的值

```(gdb) x $ebp-0xc
0xffffd16c: 0x00052b24
(gdb)
```

把`52b24`转化为十进制得到`338724`。继续执行程序

```
(gdb) c
Continuing.
Invalid Password!
[Inferior 1 (process 28870) exited normally]
```

删除断点

```
(gdb) d br
Delete all breakpoints? (y or n) y
```

重新运行，输入`338724`作为密码

```(gdb) r
Starting program: /path/to/IOLI-crackme/bin-linux/crackme0x02 
IOLI Crackme Level 0x02
Password: 338724
Password OK :)
[Inferior 1 (process 29390) exited normally]
(gdb)
```

