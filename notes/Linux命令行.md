# Linux命令行

获取内存信息

```shell
$ cat /proc/meminfo
```

获取共享内存信息

```Shell
$ ipcs -m
```

密码文件`/etc/passwd`

```shell
# username:password_placeholder:user_id:group_id:full_name:home_dir:default_shell
```

启动bash的命令

```shell
$ bash -c 'command'
# Start bash to execute command
$ bash -r
# Start a restricted shell with directory limited
```

控制shell的prompt：两个环境变量PS1和PS2。PS1控制了默认情况下prompt会显示什么。

| 字符                         | 意义                                 |
| -------------------------- | ---------------------------------- |
| `\a` `\n` `\r` `\123` `\\` | 这些和C语言里的转义字符一样                     |
| `\d, \t, \T, \@`           | 7月17日, 16:50:29, 04:50:29, 04:50下午 |
| `\h, \H, \s, \v, \V, \u`   | 主机名，完整主机名，shell名，shell版本，发型号，用户名   |
| `\w, \W, \!, \#, \$`       | 当前路径，当前目录，当前命令历史号，当前命令号，$或#        |

