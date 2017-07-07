# GDB

* 查看变量类型`explore type var`
* 查看上次程序结束时的返回值 `p $_exitcode`
* 查询上次查询的地址和地址内容 `p $_`, `p $__`
* 进入和退出图形化界面 `Ctrl+X+A`
* 设置gdb在一个变量的值发生变化时停下 `wa *(int*)0x6009c8`
* 设置gdb在读写变量的时候停下 `aw *(int*)0x6009c8`
* 设置gdb在读变量的时候停下 `rw *(int*)0x6009c8`
* 设置环境变量 `set env LD_PRELOAD=/lib/x86_64-linux-gnu/libpthread.so.0`
* 在gdb启动后设置命令行参数 `set args a b c`
* 结构化打印结构体 `set print pretty on`
* 条件断点 `b *main+35 if i==100`
* 保存断点信息 `save breakpoints filename` `source filename`
* 执行shell命令 `!ls` 或 `shell ls`
* 打印当前局部变量的值 `bt full` 当前函数局部变量 `i locals`
* 将汇编指令和源代码映射 `disas /m main`
* 忽略断点若干次 `ignore num count`
* 反汇编时显示机器码 `disas /r main`
* 自动反汇编后面的代码 `set disassamble-next-line on`