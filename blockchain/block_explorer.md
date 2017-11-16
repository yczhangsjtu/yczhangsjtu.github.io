---
title: Block Explorer环境搭建
---

# Block Explorer环境搭建

GitHub上第一个：Iquidus

https://github.com/iquidus/explorer

## Iquidus概览

Iquidus启动后开始运行一个Web服务器，用来展示Block Explorer。Block Explorer所需的区块和交易信息全部来自数据库。

个别零散数据（如Network Hash Rate等）由Web服务器直接向钱包（全节点）做RPC访问。

数据库中的数据也是通过对钱包RPC查询获得的，这个过程由另外一个定期运行的脚本额外完成。

## Iquidus搭建

### 配置数据库

需要安装MongoDB。

```shell
$ sudo apt install mongodb-server mongodb-clients
```

按照GitHub上的Readme所述，配置数据库：创建database explorer和用户iquidus及其口令以供接下来inquidus使用。

### 配置Inquidus

从GitHub上clone下来Iquidus，到explorer文件夹。按照Readme所述安装所有依赖的npm包（命令`npm install --production`），这些包会被存放到explorer目录下的node_modules文件夹中。接下来所有提到的路径和文件，如果没有特殊说明，都默认为相对于Inquidus的根目录，也就是explorer目录。

然后把explorer目录下的模板配置文件settings.json.template拷贝一份为settings.json。

修改settings.json文件，文件不大，所有配置都有注释，基本上一看就明白是做什么的。需要改的地方大概有如下：

* title: 网页标题，改成Hcash Explorer之类的。
* address: 默认为本地地址3001端口，可能会被系统程序占用（使用`netstat -nltp`查看，如果冲突的话运行Inquidus时会出现bind错误），建议改成一个不常用的端口
* coin: 币名，改成Hypercash
* symbol: 币简称，改成Hcash
* logo: 这个可以不变，但要用Hcash的Logo图片换下public/images/logo.png。（注：public是Web服务器的根目录）
* favicon: 用另一个图标换下public/favicon.ico。
* theme: 主题，默认为黑色的Cyborg，还有许多别的可以选择。
* port: 和address保持一致。（不知道为什么会重复）
* dbsettings: MongoDB的配置。除了用户名和密码，没有什么好改的。如果改了，不要忘了在MongoDB里创建。
* wallet: port改为hcashd的RPC Server监听端口（默认为11009）；用户名密码改成hcashctl.conf文件里的用户名和密码；此外，再增加两个参数：ssl设为true，以开启HTTPS；另外因为我们的Hcashd用的证书是自签名的，所以要添加设置sslStrict为false，承认自签名的证书合法。
* index：将其中的difficulty设置为Hybrid，即PoW和PoS的difficulty都要显示。
* genesis_tx和genesis_block: 通过`hcashctl getblockhash 0`和`hcashctl getblock <hash>`得到我们的链的创世区块和创世交易。
* heavy: 改为true，支持更多特性。
* txcount: 每个地址存最近多少个交易。
* supply: 怎样得到当前货币发行量？默认为COINBASE，即所有coinbase的总和作为发行量；建议改为HEAVY，即直接RPC查询得到。
* nethash_units: 网络总算力的单位，目前可以选K和M（不然小数点后好几个零）

### 启动Inquidus

修改完毕后，就可以启动Inquidus了，方法是在根目录下执行`npm start`。根据package.json文件，这一命令会执行bin目录下的脚本cluster，这个脚本会执行bin目录下另一脚本instance。后者包含了启动服务器的主要代码。

启动后，就可以在浏览器里访问设定的端口，访问explorer了。每次访问，启动`npm start`就会输出Web访问的日志，以及可能出现的错误。

此时，MongoDB数据库还没有同步，所以区块浏览器一片空白。只有Network Hash和挖矿难度还有区块总数不为空，因为它们是Inquidus直接RPC访问hcashd得到的。而Supply虽然也可以一个RPC访问直接得到，却仍然要存在数据库里，因此现在是0。

### 同步数据库

同步数据库用的是另外一个脚本scripts/sync.js。输入命令`nodejs scripts/sync.js index update`开始同步。这里index指同步区块信息，另一个选择是market，同步市场信息（Hcash的市价，和区块链无关，需要从别的渠道获取）。
为了能够同步，启动hcashd的时候需要启动选项txindex。

update是更新区块信息，另外的选项reindex则是删除全部数据从头建立数据库，check则用来补上一些漏掉的地址和交易。

Readme中给的建议是第一次同步完成后，再用Linux下的contab工具安排同步脚本每分钟运行一次，实时把区块信息更新到MongoDB中。

同步数据库脚本实际上也是用RPC请求从hcashd获取区块信息，因此工作起来很慢。同步进行的时候，可以看到Inquidus收到大量的请求，循环播放getblockhash，getblockbyhash，getrawtransaction等请求。

## 其他注意事项

* 如果用Ctrl-C中断过scripts/sync.js，会在tmp文件夹（在explorer下，不是`/tmp`）中留下一个index.pid文件。这是用来防止脚本重复运行的。强制退出后要删除这个文件，不然会提示script is already running错误。
* 我们的测试链数据量非常小，所以没有出现栈溢出的错误，但将来可能会出现。如果出现，按照Readme中exceeding stack size的建议进行设置。
