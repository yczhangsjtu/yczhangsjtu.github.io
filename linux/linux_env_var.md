---
title: Linux环境变量
---

# Linux环境变量

环境变量包括全局变量和局部变量。全局变量在子进程可见。局部变量仅在当前进程可见。

## 基本操作

打印全局环境变量：`printenv`

打印当前所有环境变量（包括全局变量）：`set`

设置局部环境变量：`varname=value`

将变量设置为全局环境变量：`export varname`

删除环境变量：`unset varname`如果被删除的变量为全局变量，删除只在当前进程有效。

## 环境变量的初始化

登录shell会依次读取以下配置文件

- `/etc/profile`
- `$HOME/.bash_profile`
- `$HOME/.bash_login`
- `$HOME/.profile`

从其他进程启动的bash只会读取`$HOME/.bashrc`。

点`.`命令可以用来读取配置文件（相当于`source`命令）。

运行shell脚本时，`BASH_ENV`环境变量用来指定读取什么配置文件。默认为空。

## 数组环境变量

创建数组变量：`myarray=(one two three four)`

索引数组：`echo ${myarray[2]}`

打印整个数组：`echo ${myarray[*]}`

设置数组的某个元素：`myarray[2] = seven`

删除数组的某个元素：`unset myarray[2]`（注意，删除后那个位置是空的，其他元素位置不变）

## 权限管理

增加、删除用户：`useradd`，`userdel`

修改用户属性：`usermod`

设置用户默认组：`usermod -g primarygroup user`

设置一般组：`usermod -a -G secondarygroup user`

改密码：`passwd`和`chpasswd`

改默认shell：`chsh`

创建新组：`groupadd`

把用户加入组：`usermod -G group user`

修改组属性：`groupmod`

修改文件所属：`chown owner[.group] file`，`chgrp group file`

修改文件权限：`chmod [ugoa][+-=][rwxXstugo] file`

