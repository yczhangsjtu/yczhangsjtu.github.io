---
title: APT and Dpkg Quick Reference
---

# APT and Dpkg 快速参考

Matthew Danish ( pye,quanliking合译 )

## 普通 APT 用法

* 下载 `<package>` 以及所有倚赖的包裹,同时进行包裹的安装或升级.如果某个包裹被设置了 `hold` (停止标志,就会被搁在一边(即不会被升级).更多 `hold` 细节请看下面.

```bash
apt-get install <package>
```

* 移除 `<package>` 以及任何倚赖这个包裹的其它包裹. `--purge` 指明这个包裹应该被完全清除 (`purged`) ,更多信息请看 `dpkg -P` .

```bash
apt-get remove [--purge] <package>
```

* 升级来自 Debian 镜像的包裹列表,如果你想安装当天的任何软件,至少每天运行一次,而且每次修改了 `/etc/apt/sources.list` 后,必须执行.

```bash
apt-get update
```

* 升级所有已经安装的包裹为最新可用版本.不会安装新的或移除老的包裹.如果一个包改变了倚赖关系而需要安装一个新的包裹,那么它将不会被升级,而是标志为`hold` .`apt-get update` 不会升级被标志为 `hold` 的包裹 (这个也就是 `hold` 的意思).请看下文如何手动设置包裹为 `hold` .我建议同时使用 `-u` 选项,因为这样你就能看到哪些包裹将会被升级.

```bash
apt-get upgrade [-u]
```

* 和 `apt-get upgrade` 类似,除了 `dist-upgrade` 会安装和移除包裹来满足倚赖关系.因此具有一定的危险性.

```bash
apt-get dist-upgrade [-u]
```

* 搜索满足 `<pattern>` 的包裹和描述.

```bash
apt-cache search <pattern>
```

* 显示 `<package>` 的完整的描述.

```bash
apt-cache show <package>
```

* 显示 `<package>` 许多细节,以及和其它包裹的关系.

```bash
apt-cache showpkg <package>
```

* APT 的几个图形前端(其中一些在使用前得先安装).这里 dselect 无疑是最强大的,也是最古老,最难驾驭.

```
dselect
console-apt
aptitude
gnome-apt
```

## 普通 Dpkg 用法

* 安装一个 Debian 包裹文件;如你手动下载的文件.

```bash
dpkg -i <package.deb>
```

* 列出 `<package.deb>` 的内容.

```bash
dpkg -c <package.deb>
```

* 从 `<package.deb>` 中提取包裹信息.

```bash
dpkg -I <package.deb>
```

* 移除一个已安装的包裹.

```bash
dpkg -r <package>
```

* 完全清除一个已安装的包裹.和 `remove` 不同的是, `remove` 只是删掉数据和可执行文件, `purge` 另外还删除所有的配制文件.

```bash
dpkg -P <package>
```

* 列出 `<package>` 安装的所有文件清单.同时请看 `dpkg -c` 来检查一个 .deb 文件的内容.

```bash
dpkg -L <package>
```

* 显示已安装包裹的信息.同时请看 `apt-cache` 显示 Debian 存档中的包裹信息,以及 `dpkg -I` 来显示从一个 .deb 文件中提取的包裹信息.

```bash
dpkg -s <package>
```

* 重新配制一个已经安装的包裹,如果它使用的是 debconf (debconf 为包裹安装提供了一个统一的配制界面).你能够重新配制 debconf 它本身,如你想改变它的前端或提问的优先权.例如,重新配制 debconf ,使用一个 dialog 前端,简单运行:

```bash
dpkg-reconfigure <package>
```

* 或者

```bash
dpkg-reconfigure --frontend=dialog debconf # (如果你安装时选错了,这里可以改回来哟:)
```

* 设置 `<package>` 的状态为 hlod (命令行方式)

```bash
echo "<package> hold" | dpkg --set-selections
```

* 取的 `<package>` 的当前状态 (命令行方式)

```bash
dpkg --get-selections "<package>"
```

* 支持通配符,如:

```bash
Debian:~# dpkg --get-selections *wine*
libwine                                         hold
libwine-alsa                                    hold
libwine-arts                                    hold
libwine-dev                                     hold
libwine-nas                                     hold
libwine-print                                   hold
libwine-twain                                   hold
wine                                            hold
wine+                                           hold
wine-doc                                        hold
wine-utils                                      hold
```

* 例如: 大家现在用的都是 gaim-0.58 + QQ-plugin,为了防止 gaim 被升级,我们可以采用如下方法:

	* 方法一:

	```bash
	Debian:~# echo "gaim hold" | dpkg --set-selections
	```

	然后用下面命令检查一下:

	```bash
	Debian:~# dpkg --get-selections "gaim"
	gaim                                            hold
	```

	现在的状态标志是 hold,就不能被升级了.

	如果想恢复怎么办呢?

	```bash
	Debian:~# echo "gaim install" | dpkg --set-selections
	Debian:~# dpkg --get-selections "gaim"
	gaim                                            install
	```

	这时状态标志又被重置为 install,可以继续升级了.

	同志们会问,哪个这些状态标志都写在哪个文件中呢?
	在 /var/lib/dpkg/status 里,你也可以通过修改这个文件实现 hold.

	有时你会发现有的软件状态标志是 purge,不要奇怪.
	如:事先已经安装了 amsn, 然后把它卸了.

	```bash
	apt-get remove --purge amsn
	```

	那么状态标志就从 install 变成 purge.

	* 方法二:
	在`/etc/apt` 下手动建一个 `preferences` 文件
	内容：

	```
	Package: gaim
	Pin: version 0.58*
	```

	保存

	更详细内容请看:
	http://linuxsir.com/bbs/showthread.php?s=&threadid=22601

* 在包裹数据库中查找 `<file>`,并告诉你哪个包裹包含了这个文件.(注:查找的是事先已经安装的包裹)

```bash
dpkg -S <file>
```

* 从源码建立deb packages

```bash
apt-get source [-b] <package> 
```

下载一个源码的包并解开。
你必须在你的`/etc/apt/sources.list`文件里写入一条 deb-src 的记录才能完成这项工作。
如果你输入了一个`-b`参数，并且是以`root`的身份，deb包会被自动的创建。

```bash
apt-get build-dep <package> 
```


自动下载并安装通过源码创建 `<package>` 时需要的包。
只有apt 0.5以上版本才支持这个功能。
现在woody和以上版本包含了这个功能。
如果你使有一个旧版本的apt，查找依赖性最简单的方法是查看源码包中 debian/control 这个文件，
注意这个路径是相对的，是包内的路径。

普通的用法，结合 apt-get source -b,例子 (as root)：

```bash
apt-get build-dep <package>
```

* 下载源码包，建立依赖性，然后尝试编译源码。

```bash
apt-get source -b <package>
```

* 如果你手工下载了一个程序的源码包，其中包含了几个类似.orig.tar.gz, .dsc,以及 .diff.gz 之类的文件，那么你就可以对 .dsc 文件使用这个命令来 unpack 源码包。

```bash
dpkg-source -x <package.dsc>
```

* 从 Debian 源码树建立一个deb包。你必须在source tree的主目录才能生效。例如：

```bash
dpkg-buildpackage
```

```bash
dpkg-buildpackage -rfakeroot -uc -b
```

这里 '-rfakeroot' 指定命令使用 fakeroot 程序来模仿 root 权限 (来实现所有者(ownership)目的)，
'-uc' 表示 "Don't cryptographically sign the changelog", '-b' 代表只建立二进制包.

* 一个快速打包脚本类似 dpkg-buildpackage ,能自动的识别是否使用 fakeroot, 同时为你运行 lintian 和 gpg 

```bash
debuild
```

* 修正倚赖关系

```bash
dpkg --configure --pending
```

如果dpkg在apt-get install upgrade dist-uptradeing 的时候出错退出，
尝试使用此命令来配置已经unpack的包。
然后再用 `apt-get install`, `upgrade`, or `dist-upgrade -f`

可能会重复多次，这样通常可以解决大多数的依赖性问题。
(同时,如果提示由于某种原因需要某个特定的包裹,你可以常识安装或卸载这个包)

```bash
apt-get install -f
```


```bash
apt-get upgrade -f
```

```bash
apt-get dist-upgrade -f 
```

注意 `apt-get install -f` 不需要 `<package>` 作为参数。
