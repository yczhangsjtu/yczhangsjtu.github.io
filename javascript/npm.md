---
title: NPM
---

# NPM

npm是一个分享JavaScript代码的平台。这些代码以包（package）为单位进行管理。一个包就是一个文件夹，里面有一个`package.json`文件。使用npm客户端，可以从官网下载包，或者发布自己的包等等。

npm客户端本身也是一个npm平台上维护的包，所以可以用npm来维护。需要升级npm时，可以运行

```shell
$ sudo npm install -g npm
```

npm安装包时，可以安装到当前文件夹，此时它仅仅是把东西下载下来而已，不影响全局。如果要让一个包在整个系统里可用，需要增加global选项-g。这时它会把包安装在系统文件夹。具体是哪个文件夹可以用`npm config get prefix`查看。其中`npm config`是管理npm配置的命令，`get <key>`则可以获取某个配置选项。npm会把包安装在这个prefix所指的文件夹下的lib目录下的node_modules文件夹中，可执行文件则会放在bin目录下，文档放在share目录下。

为了在安装的时候不用sudo，你可以把这三个文件夹的owner改成你自己。或者把一个新的文件夹设为prefix：`npm config set prefix /path/to/dir`。

局部安装时，`npm install <package>`会把包下载到`node_modules`目录下，整个包的内容在`node_modules/<package>`中。如果没有指定package名，npm会读取当前文件夹下的package.json文件，从`dependencies`属性，得知依赖哪些包，并将其安装。

简单地说，npm在运行install时，假设你正在开发一个npm的包，正在这个包的根目录下，它是在帮你把依赖的包下载下来。除了依赖的包，package.json还会指定本包的其他属性，比如包名和当前版本，这两个是必须的。其他选项可以没有，包括`dependencies`。

创建一个package.json文件，并不一定需要从空白文件写起。可以用`npm init`来创建。

如果要把一个本地的包去掉，同时在package.json中删除对应的dependency项，可以用`npm uninstall <package> --save`命令。同样，使用`--save`命令在install的时候也会改写`dependencies`。

如果你手动把包名从`dependencies`中去掉了，这时已经安装的包会成为多余的，可以用`npm prune`来把多余的包删除。
