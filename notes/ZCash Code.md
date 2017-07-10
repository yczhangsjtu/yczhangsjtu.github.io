# ZCash Code (1.0.10-1)

## Initialization 

All the command-line parameters are specified in a global map `mapArgs` provided by `boost`. In `AppInit()` function defined in `bitcoind.cpp`, initialize the very basic settings such as the data directory `datadir` and configuration file `conf`. If not specified in command line, use the default `$HOME/zcash` and `zcash.conf` respectively. Then read configuration file and modify the `mapArgs` to update the settings (as if they has been specified in command-line). Note that this does not change the settings already set in command-line. In another word, command-line options are superior to configuration file.

After that, call `AppInit2()` to deal with all the settings, transforming the string represented settings to value of global variables. This function basically handles all the options.

Zcash zk-SNARK parameters (namely, `sprout-proving.key` and `sprout-verifying.key`) are loaded in `AppInit2()` by invoking `ZC_LoadParams()`, which initializes the curve parameters in libsnark.

`AppInit()` declares a `threadGroup` and passes it to `AppInit2()`. After `AppInit2()`, `AppInit()` invokes `WaitForShutdown()` to wait for the threads started in `AppInit2()` to stop.

The threads started in `AppInit2()` are as follows:

* `ThreadScriptCheck`: The number of threads is set by `-par` option, or auto detected if `-par=0` is set. The function executed by the thread is in `main.cpp`:

  ```c++
  scriptcheckqueue.Thread();
  ```

  Where `scriptcheckqueue` is a static global queue of structure:

  ```c++
  static CCheckQueue<CScriptCheck> scriptcheckqueue(128);
  ```

  The `CCheckQueue` class implements a queue for verifications that have to be performed. `CScriptCheck` is the class implementing Bitcoin script functionalities.

* `ThreadShowMetricsScreen`: periodically print the messages on the screen.

* `HTTPServer`: a thread looping and handling a queue of `WorkItem`, which is parameterized by `HTTPRequest`, a `string` path and a functional as handler.

* `ThreadImport`: Thread to import blocks.

* `StartNode`: Handling the network stuffs.

* `ThreadFlushWalletDB`: Flush wallet periodically.

此外，`AppInit2()`还在`threadGroup`之外启动了一些线程，比如启动挖矿程序`GenerateBitcoins()`。

## 挖矿

挖矿程序在`AppInit2()`中调用`miner.cpp`中的`GenerateBitcoins()`启动。`GenerateBitcoins()`通过命令行参数和机器的线程数综合判定出挖矿线程数，然后调用`BitcoinMiner()`函数正式开始挖矿。所有这些函数都分为有钱包和无钱包两个版本，由宏`#ifdef ENABLE_WALLET`来判断。

首先，调用`Params()`函数获取链参数。

```c++
const CChainParams& chainparams = Params();
```

### 链参数

整个`CChainParams`类在`chainparams.h`头文件中声明和定义，其他`ChainParams`类都是它的派生类。这个类就像是一个结构体，集合了所有区块链的参数设置，只不过把对所有成员都保护了起来，然后每个都给出一个`const`的`get`函数。

其他所有派生类也都非常简单，只是在构造函数里对每个成员赋值。这样一个派生类就对应一套参数设置，也就对应了一条区块链（因为参数里包括了整个创世区块，决定了以这一套参数设置是不能简简单单随便生成一个区块链出来的，所以一套参数设置就有点像是浏览器里内置的一张根证书）。

参数里还规定了：

* 各种地址（pubkey address, secret key, script address等等）的第一个字节（的base58编码），用来区分不同的地址类型。
* 创始人的地址们（都是script address，也就是比特币地址）。
* 若干检查点（指定某些高度的区块的Hash值）。
* Equihash的参数$n$和$k$。
* 默认端口`nDefaultPort`。
* 共识算法（挖矿算法）的参数，比如难度调整的窗口大小，慢启动区块数，区块奖励减半周期。
* 一些字符串，如`NetworkID`，`CurrencyUnits`。

目前的ZCash代码里给了三套参数，也就是三个派生类，第一个是`CMainParams`，也就是ZCash主链的参数。还有`CTestParams`和`CRegTestParams`。注意它们是一个继承一个串起来的，并且只有一个构造函数。因为C++中基类的构造函数会首先运行，所以这些派生类的参数设置只是在基类的基础上进行覆盖，没有涉及的就和基类相同。也就是说，`CTestParams`继承了`CMainParams`里的某些参数，改动了另外一些。`CRegTestParams`也一样。

这三个类定义每一个都紧接着定义一个静态全局变量。`chainparams.cpp`里还定义了一个`pCurrentParams`指针，用来指向当前到底用哪一个参数。而这个指针的设置，早在`AppInit()`函数里就做了（`SelectParamsFromCommandLine()`函数）。目前ZCash代码的逻辑是，当`testnet`和`regtest`的选项都没有时，用主网络。只有一个选项时，用对应的网络。都有时，返回错误。

调用`Params()`函数则会直接引用`pCurrentParams`指针，所以在调用它之前必须要先调用`SelectParams()`函数。

### 挖矿地址

如果支持钱包的话，会从钱包的地址池里保留一个出来，从钱包里暂时删除（只是删除一个索引）。这个地址是一个`ReserveKey`类型的，析构函数会自动把地址返回钱包。

### Solver

接下来，`BitcoinMiner()`函数从命令行获取Solver设置`-equihashsolver`（目前只有两个，`default`和`tromp`）。然后，开始一个`while(true)`循环，这个循环体占据了这个函数剩下的篇幅。

这个循环是对外层`nonce`的值的循环，也就是改变交易内容等信息，以防小的`nonce`值跑完了还没找到结果。

首先，如果这个挖矿参数需要联系其他节点（除了`regtest`外，一般都是如此），则进行忙等待，直到连接上其他节点。这个时候挖矿计时暂停。

等一切更新好之后，获取最新的区块，就开始构造自己的区块了。同样，如果有`ReservedKey`的话，就用它从Wallet中预留出一个地址作为挖矿地址，否则就随机生成一个。然后，设置难度目标。

接着，又开始一个`while(true)`循环，这个循环就是对小`nonce`值的循环了。首先算出除了`nonce`以外的`Equihash`的状态，存储在`crypto_generichash_blake2b_state`结构体`state`中，然后用`curr_state`继续update进`nonce`。随后，定义两个lambda函数，分别是验证区块哈希是否满足难度要求`validBlock`，和检查是否取消`cancelled`。

所谓不同的挖矿算法，其实是给定了`curr_state`的时候，求解general birthday problem的算法。所以，直到现在输入已经完全确定了的时候才程序才开始区分用哪个算法。区分的方式是用`if`语句比较字符串。因为程序主要耗时在求解birthday problem上，这些字符串比较之类的事情时间就忽略不计了。

Tromp算法直接就实现在了`if`块里面，而默认的算法则被封装成了函数`EhOptimisedSolve()`。如果当前的`nonce`给出的solution能够得到符合条件的区块，就直接`break`出这个`while`循环。否则增加`nonce`继续算。

## libzcash

Libzcash is basically an implementation of the DAP scheme (modified version in the Zcash protocol specification), which does not concern the PoW or consensus protocols. The source code lies in the `zcash` subdirectory in `src`.

Note the choice of elliptic curve of zcash.

```c++
// zcash/Proof.cpp
typedef alt_bn128_pp curve_pp;
typedef alt_bn128_pp::G1_type curve_G1;
typedef alt_bn128_pp::G2_type curve_G2;
typedef alt_bn128_pp::GT_type curve_GT;
typedef alt_bn128_pp::Fp_type curve_Fr;
typedef alt_bn128_pp::Fq_type curve_Fq;
typedef alt_bn128_pp::Fqe_type curve_Fq2;
```

### JoinSplit

`JointSplit` (in `JoinSplit.cpp`) is a class in `libzcash`, defined as follows:

```c++
template<size_t NumInputs, size_t NumOutputs>
class JoinSplit {
  static JoinSplit* Generated();
  static JoinSplit* Unopened();
  
  ...
  virtual ZCProof prove(...);
  virtual bool verify(...);
};
```

Then `ZCJoinSplit` is defined as a typedef, with `NumInputs` and `NumOutputs` specified (both to constant two).

`JoinSplit` is a virtual class. The implementor is `JoinSplitCircuit`.

The real work is done in `joinsplit_gadget` in directory `circuit`. This gadget is built on other gadgets in `circuit` directory, and all the way to the gadgets constructed in `libsnark`.

The proof generation and verification is done in `Proof.cpp`, via two classes `ZCProof` and `ProofVerifier`.

The `ProofVerifier` class provides two static member functions to obtain an instance of this class. They are `Strict()` and `Disabled()` respectively. The difference is actually setting `perform_verification` to `true` or `false`. When `Disabled()`, the `check()` method always return true without actually checking the proof.