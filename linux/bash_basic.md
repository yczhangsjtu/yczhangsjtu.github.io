---
title: Bash脚本基础
---

# Bash脚本基础

## Bash脚本运行控制

进程之间是用信号传递信息的。Linux系统定义的信号可以用`man signal`查看。默认情况下脚本忽略大部分信号，但对SIGHUP和SIGINT信号做出反应。如果要忽略这两个信号，可以用`trap`命令。

命令可以在背后运行，不占用命令行，只要输入命令的时候在最后补充一个&符号。但终端退出的时候，所有由它启动的背后运行的命令都会退出。如果不想让它们退出，可以使用`nohup`命令。这个命令使得背后运行的程序脱离终端。但因此它也没有了标准输入和标准输出，所以会默认输出到一个文件。

如果要查看这个终端背后正运行的程序，使用`jobs`命令。除了用&符号运行的命令外，还有用^Z暂停的程序。如果要暂停的程序重新运行起来，用`bg`命令。

使用`at`命令安排一些程序在指定的时间运行。使用`batch`命令安排一些程序在电脑利用率低的时候运行。用`cron`命令安排一些程序周期运行。

## Bash中的命令行参数

Bash的命令行参数及其相关的量都存在一些特殊变量里。比如其内容存在数字命名的变量里，从`$0`到`$9`。如果更多的话需要用大括号，如`${10}`。这些变量有个共同特点是用户不可能给它们赋值。

命令行参数的数量为`$#`。注意没有参数时这个值是零，而不是像C++里面那样把程序名算上。

所有命令行参数的集合放在`$*`和`$@`中。其中，`$*`被当做一个整体，而`$@`是一个数组。如果对它们分别用for..in循环，`$*`的循环体只会执行一次，而`$@`则对每个命令行参数执行一次。

处理命令行参数时，`shift`命令非常好用。不加任何参数时，`shift`把第一个参数扔掉（所有上面提到的变量都会相应改变）。这样可以做一个while循环，每次只处理`$1`，处理前进行检查看看它是不是还有，处理完就`shift`一下，直到命令行参数为空。注意`$0`一直不变，永远保存着程序名。

Bash也支持getopt，不过getopt的用法和C里面完全不同。Bash有一个“标准”的命令行参数模式：程序名后跟着所有选项（带-的），然后用双短横（--）隔开，接下来跟着所有的参数（注意选项和普通参数的概念是区分开的）。getopt命令的作用，就是把原本用户友好的命令行参数（比如多个短横选项合并）处理成标准模式。使用getopt的时候，一般是用`set`命令把整个命令行替换掉。

```shell
set -- `getopt ab:cd "$@"`
```

可惜getopt做得不够好，特别是命令行里有引号括起来的空格时，它没有做合并处理。

getopt还有一个表兄弟，getopts，它的用法倒是和C里面的getopt差不多了：它也不需要把当前的命令行作为参数输入，它直接就会去读，并把当前的选项放到给定的变量名里。如果是带参选项，选项参数会放到`$OPTARG`变量里。所以一般使用模式如下：

```shell
while getopts ab:cd opt; do
  case "$opt" in
  a) echo "Found -a" ;;
  b) echo "Found -b $OPTARG" ;;
  c) echo "Found -c" ;;
  d) echo "Found -d" ;;
  *) echo "Unknown $opt" ;;
  esac
done
```

## Bash中的输入和输出

Bash中一个`read`命令用来读取标准输入或文件到一个变量。从标准输入读的命令就是`read <varname>`。变量可以有多个，输入用空格隔开；也可以一个也没有，输入结果会放入环境变量REPLY中。

`read`命令可以有时限，时限到了仍然没有输入，该命令以非零值退出（因此可以用if语句判断是否有输入）。

如果改从文件读取，可以用`cat`命令加管道将文件输入`read`命令。

Bash中最基本的输出方式就是到标准输出。如果需要写到文件就用重定向。不过，bash中也可以有文件操作符。和其他语言一样，bash自带了三个文件操作符，STDIN，STDOUT和STDERR，分别对应数字0，1，2。

重定向的时候，可以指定重定向哪个文件操作符。`>`符号重定向输出，`<`重定向输入。左边为文件操作符，右边为文件。把文件操作符转为文件的符号是`&`。如果左边没有指定操作符，默认为标准的输入或输出。

比如要重定向标准错误，则用`2>`符号。如果两个输出都重定向到一个文件，则用`&>`符号。而如果想把额外的信息输出到标准错误，可以用`>&2`进行重定向。

如果要长久地进行重定向，用`exec`命令。脚本中一句`exec 2> testfile`之后，接下来所有命令的标准错误都重定向到了文件中。类似地，将文件重定向到标准输入，则用`exec 0< testfile`命令。

`exec`命令还可以把别的文件操作符（从3到9）重定向到文件，接下来如果要输出到这个文件，就输出到这个操作符。另外，别的文件操作符还可以起到备份标准文件操作符的作用。

```
exec 3>&1exec 1>testfileecho "Something"exec 1>&3
```

Bash创建临时文件的命令是`mktemp`。该命令的参数为一个模板，模板末尾任意个数的X会被替换为随机的字母或数字，作为临时文件的文件名。该命令创建文件，并输出文件名。如果使用`-t`选项，文件就会出现在`/tmp`文件夹下，输出的文件名也为绝对路径。如果使用`-d`选项，则建立临时文件夹。

## Bash中的条件判断

理解bash中的条件判断，就要理解一件事情，那就是bash的if语句只能做一件事，那就是判断一个程序是否成功退出（退出值为0）。

```Shell
if ls; then
  echo 'Worked!'
fi
```

要用if语句做什么复杂的判断，实际上就是写个程序分析命令行输入，如果合意，就正常退出，不然就返回一个非零的退出值。

还好，Linux系统已经给我们准备好了一些这样的程序。其中最常用的就是test。它可以做三类判断：比较数字、字符串，和关于文件的判断。

比如做数字大小判断：

```shell
$ test 5 -gt 4
$ echo $?
0
$ test 5 -lt 5
$ echo $?
1
```

这类程序不用做任何输出，唯一的价值就是它的退出值。

为了让代码更好看，test命令还有另一种写法，就是把命令行参数放在一对中括号里，注意和前后括号之间一定要有空格！

```shell
$ [ 5 -gt 4 ]
$ echo $?
0
$ [ 5 -lt 5 ]
$ echo $?
1
```

所以你看到的bash代码中的if语句，后面都要跟一对诡异的中括号，还有奇怪的空格的要求：if和中括号之间必须有空格。这很好理解，你把if和test之间没有空格地写在一起试试看？

把这对中括号看成test命令的参数，它的风格问题就很好理解了，比如连与或运算都可以用短横参数表示。只有字符串的比较用的是一般意义上的数学符号，逻辑运算也可以用C中的相应符号。需要的时候，随时查`man test`，这里就不展开它的细节了。只是在字符串比较时提醒注意：

* 用于字符串比较的大于号和小于号要转义（escape）掉，不然会被当成重定向。
* 大小写字符的先后顺序和sort是相反的：test认为大写字母在先（按照ASCII表），而sort用的是语言顺序，也就是按文件名排列时用到的顺序。

类似的还有双括号(())，可以用来计算更复杂的数学式子，并判断结果是不是零。这个就比中括号宽松多了，没有那么多空格限制，也不用把特殊符号转义掉。

双中括号是专门用来比较字符串的，比test命令包含了更高级的字符串比较工具——正则表达式模式匹配。不过，和单中括号一样，对空格有很多要求。但它不需要转义特殊符号。

## Bash中的循环

最常用的是for..in循环，简单地说就是把字符串拆开，将拆出来的小串赋值给循环变量。

默认情况下，bash用来拆字符串的分界符有空格、tab和换行符。这个存储在一个环境变量`IFS`中。如果你想对每行进行操作，修改`IFS`即可。

```shell
$ IFS=$'\n'
$ for f in `ls`; do echo $f; done
```

如果需要多个分界符，把它们拼在一起就好了，比如`IFS=$'\n':;"`。

for..in循环还可以遍历由wildcard产生的列表。

Bash也支持C风格的循环，区别是把小括号换成了两重。

Bash支持while和until循环，其中的条件判断和if语句一样。稍微不同的地方在于，while和until循环的条件部分允许多行语句，只取最后一行的结果。

Bash的循环支持多重跳出，在break后加上跳出的重数即可。默认为1。continue也一样（多重continue相当于break出里层的循环，再continue最外层的循环）。

一个循环语句相当于一个命令。循环当中所有的输出都是这个命令的输出。因此可以在循环最后进行重定向，把循环的全部输出重定向到一个文件。