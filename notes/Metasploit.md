# Metasploit

Metasploit提供了一个Ruby库，Ruby Extension (Rex)，提供了许多基础功能。这个库本身不依赖任何其他库，除了Ruby的标准库。

MSF核心是基于Rex的扩展，提供了攻击模块等接口。在MSF核心的基础上是MSF基础库，提供了和MSF核心打交道的常用函数。最终，Metasploit的UI在此基础上提供了用户界面。

Metasploit提供四种用户界面，`msfconsole`，`msfcli`，`msfgui`和`msfweb`。其中`msfconsole`提供了最全面的功能。

## 安装Metasploit

Metasploit被Rapid收购后开始收费，但。

这里只讲Ubuntu下的安装。

从Rapid7的网站下载Metasploit，从邮件收到License Key：
（教程中还是命令行界面的安装过程，但现在安装已经是图形界面了。）
http://downloads.metasploit.com/data/releases/metasploit-latest-linux-x64-installer.run

先`chmod +x`让它可执行，然后执行即可。执行完后会自动打开浏览器，按照提示进行设置即可。

也可以用git：

```bash
$ git clone git://github.com/rapid7/metasploit-framework.git
```

但git不会帮你安装各种依赖的库。

虽然最新的Metasploit提供了强大的Web界面，但这里还是介绍console界面。

## 信息搜集

* Google Hacking Database: https://www.exploit-db.com/google-hacking-database/ 怎样用Google发现漏洞
* SHODAN: https://www.shodan.io/ 联网设备的搜索引擎
* Netcraft: https://www.netcraft.com/
* WHOIS: https://who.is 第三方DNS查询网站

### 基本的用于信息搜集的Linux工具

有些Linux工具可以用于信息搜集，不用Metasploit。

* `whois`可以用来获取关于域名的信息。该工具会联网查询whois数据库。

  ```bash
  $ whois google.com
  ```

* `dig`用来访问DNS服务器，做基本的域名解析。

   ```bash
   $ dig google.com
   ```

* `nslookup`提供的信息和`dig`相似，不过还额外提供了DNS服务器的信息。

* `arp`和`arp-scan`可以用来查找局域网内的所有设备。

### 端口扫描

* Nmap: 关于nmap的教程可以写一本书

* DNmap: 分布式的nmap扫描系统

* SSH version scanner: 首先查看msf上有哪些ssh version扫描模块，然后使用该模块

  ```bash
  msf > search ssh_version
  msf > use auxiliary/fuzzers/ssh/ssh_version_2
  msf auxiliary(ssh_version_2) > set RHOST 192.168.0.2
  msf auxiliary(ssh_version_2) > run
  ```

* 还有一些工具需要在本机上运行这些工具的server，然后在Metasploit中调用它们的功能。这类工具有`nessus`，`nexpose`。

* OpenVAS也是一个强大的扫描工具。


## 针对操作系统的漏洞利用

MSF上有上千个exploit和上百个payload，使用`show exploits`和`show payloads`可以列出所有。也可以用`search`命令找出所需。比如`search ms16`可以找到一些2016年被发现的Windows系统或者IE的漏洞的利用模块（这类模块通常就以`ms`加年份命名）。

要使用一个模块，用`use`命令加上模块的完整路径，然后设置参数。

这一章讲的基本上就是怎么利用这些模块攻击系统漏洞，所举例子全都已经过时。并没有涉及任何技术性内容。现在如果要用MSF的模块进行攻击，需要了解最近出来的一些漏洞以及MSF上对应的模块。