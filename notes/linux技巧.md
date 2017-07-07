# Linux技巧

* 压缩PDF文件

```bash
gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -sOutputFile=output.pdf 1.pdf
```

其中`/screen`是质量最低的，文件也最小。`/ebook`质量稍高一些。

* 命令集

| **Command**                              | **Functionality**                        |
| ---------------------------------------- | ---------------------------------------- |
| `$ man -k keyword`                       | To search for `man` entries containing a keyword. |
| `$ apropos keyword`                      |                                          |
| `$ man -f cat`                           | To see what `cat` is used for.           |
| `$ whatis cat`                           |                                          |
| `$ cat m???`                             | Print content of a file with name length 4 and began by `m`. |
| `$ ps x`                                 | Get a list of all processes running on your system. |
| `$ ps aux`                               | To see everything that's running, including the daemons. |
| `$ ps -el`                               |                                          |
| `$ chgrp others data`                    | Change file `data` so that it is owned by the user `george` and group `others`. |
| `$ chown george data`                    |                                          |
| `$ cd /usr/share/man/man1``$ for file in *``> do zcat $file | col -b | grep -i column | sed "s/^/$file:/"``> done` | Search for keyword `column` ignoring case in #man# pages removing the overstriking. |
| `$ type cat`                             | To find out which version of command you are using. |
| `$ which cat`                            |                                          |
| `$ stty size`                            | Print size of terminal.                  |
| `PS1="$USER@`uname -n`$ "`               | Set the shell prompt.                    |
| `$ set -o ignoreeof`                     | Prevent the terminal from exiting by `Ctrl-D`. |
| `$ xlsfonts`                             | Print all available fonts.               |
| `$ xev`                                  | Open the mouse/keyboard event tester.    |
| `$ mkdir -p in/path/that/does/not/exist` | Create directory where the parent directory does not exist. |
| `$ ls -t`                                | Sort file by modification time.          |
| `$ ls -tu`                               | Sort file by access time.                |
| `$ ls -tc`                               | Sort file by file node change time.      |
| `$ ls -B`                                | Ignore files endded with `~`.            |
| `$ ls -S`                                | List files by size.                      |
| `$ ls -Q`                                | Quote the file names.                    |
| `$ ls --color=yes | cat -v`              | Show the special characters in coloring. |
| `$ dircolors`                            | Print the `ls` color scheme.             |
| `$ find . -type f -exec grep -n 'linux' {} ';' -print` | Find all files under current directory and search for 'linux' in the content. |
| `$ agrep -2 homogenous foo`              | Search in file `foo` all occurrences of `homogenous` within at most two edit distance. |