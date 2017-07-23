---
title: Debian System Configuration Tools
---

# Debian系统配制工具

* 系统引导是你想要启动的服务，很有用工具。 

```
rcconf 
```

* 基系统配制，第一次启动后，碰到的就是它吧，配制的方面很多哦，呵呵。 

```
base-config 
```

* Debian 包裹配制系统 

```
debconf
```

* 配制一个已经安装的包裹 

```
dpkg-reconfigure
```

很有用哦，任何安装的包裹都可以用它来配制。 

* 网络的配制，包括主机名，IP，DHCP，DNS，GATEWAY，NETMASK。。。等。 

```
apt-get install etherconf 
dpkg-reconfiguration etherconf 
```

* 如果如果你用lan上网，这几个文件很重要： 

```
/etc/hostname # 主机名 
/etc/network/interfaces # 网络配制 
/etc/resolv.conf # DNS配制 
```

如： 

```
nameserver 202.96.104.18 
nameserver 202.96.103.36 
```

* 鼠标，键盘，显示器和显卡配制，能不能进X，全看它了。或手工修改/etc/X11/XF86Config-4,作用一样。 

```
dpkg-reconfiguration xserver-xfree86 
```

* 大家肯定会在刚开始装系统时碰到那个另人望而生畏的基于表单的模块选取界面，就是它了。Debian想的非常周到，它把你须要的模块都做好了，只等你动手选了，以后忘了选或想移除模块，千万不要靠重装来解决问题。 

```
modconf 
```

当然也可以手动添加了： 

```
/etc/modules 
```

这里写的都是你引导时要加载的内核模块,可以自己添加， 

* 模块配制，这个不用自己改， 

```
/etc/modules.conf 
```

在你修改了`/etc/modules`后，可用`update-modules`来重建`/etc/modules.conf`和`/etc/chandev.conf`。 

* 几个好用的命令： 

```
modprobe - high level handling of loadable modules 
```

用来加载模块 

```
modprobe -c # 显示当前正被使用的模块配制 
modprobe -l # 显示能匹配的模块列表，你可以找你需要的模块 
modprobe modname # 加载模块 
modprobe -r modname # 移除模块 
```

* 给正在运行的内核安装一个可加载模块。 

```
insmod - install loadable kernel module 
```

* 从正在运行的内核卸载模块。 

```
rmmod 
```

* 列出已加载的模块。 

```
lsmod 
```

* 显示每个模块的信息，很有趣。 

```
modinfo modname 
```

* 配制apt源，就是安装时的你看到的那个，帮助你写/etc/apt/source.list 

```
apt-setup 
```

* 配制apt，如禁用哪个apt源，自定义添加apt源（就象是个储藏室）等，找的是你的source.list，好玩。 

```
apt-get install aptconf 
dpkg-reconfigure aptconf 
```

* 配制时区，日期，和时间。 

```
apt-get install timezoneconf 
dpkg-reconfigure timezoneconf 
```

* 配制locale，不用我说了吧。 

```
apt-get install localeconf 
dpkg-reconfigure localeconf 
```

* 查看本地的locale 

```
locale 
```

* 功能同localeconf 

```
dpkg-reconfigure locales 
```

* 中文图形配制工具。 

```
cpanel
```

* 查找上述的工具

```
apt-cache search debconf 
```
