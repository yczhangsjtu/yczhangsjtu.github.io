---
title: Promise
---

# Promise

## Javascript中的Promise用法

以下以Javascript中的Promise为例，对Promise进行讲解。

Promise可以看做是一套用来处理异步操作异常的接口，不止是用来处理HTTP的。实际上，这个概念不止在Javascript中有，C++11用来处理多线程的部分也有Promise的概念。

比如说，读一个JSON文件，并解析读到的JSON。读文件的时候可能会出错，比如文件不存在；解析JSON的时候也可能出错。如果手动来处理异常，用连串的或者嵌套的try和catch，代码很快就会乱成麻。

一个Promise对象是用来表示异步操作的结果的。它有三个状态：

* 等待
* 完成（异步操作成功）
* 拒绝（异步操作失败）

创建Promise对象的时候传入一个初始化函数。初始化函数有两个参数，这两个参数也是函数，分别是succeed函数“成功时的handler”和fail函数“失败时的handler”。

初始化函数告诉Promise对象怎样判断异步操作是否成功，并适当地调用两个handler。新创建的Promise对象会立刻调用初始化函数，虽然handler还没有确定。如果handler还没确定，异步操作就已经完成了呢？没关系，succeed函数（fail函数）会等你的handler：什么时候你给了，什么时候才执行。

一个Promise对象有自己的回复值。等待的时候它的回复值是undefined。一旦异步操作完成，Promise立刻由等待状态转为完成（拒绝）状态。如果是完成状态，它的回复值定义为传给succeed函数的参数。如果是拒绝状态，它的回复值定义为传给fail函数的参数。

给一个Promise设定handler的方法叫做then。它可以只接受一个参数，只设定succeed的handler，也可以接受两个参数，两个都设置了。你也可以只设置fail的handler，把第一个参数设为undefined（等价的方法是catch）。

目前为止，使用promise相当于用另一种方式写try和catch而已。Try里面的内容放到初始化函数和成功的handler里，该throw错误的地方换成调用fail函数；catch里的内容放到失败的handler里。唯一的区别就是成功的handler里如果继续报错的话不会跳到失败的handler里。

调用then函数的返回值是一个新的Promise对象。这个对象初始化时：

* 执行传给then函数的handler（无论是哪个）;
* 如果一切顺利，则这个Promise的返回值为上一个handler的返回值，并用这个返回值作为参数调用则下一个成功的handler；
* 如果执行过程中出现错误（可以是自己throw的），则这个Promise的返回值是错误，并以此为参数调用下一个失败的handler。

换句话说，除了第一个Promise是你自己给定初始化函数，一切都要自己打理外（也因此有很高的自由度，随便你什么时候调用哪个handler），以后用then产生的Promise，都自动帮你做好了一切，但必须要用错误来激发它的失败handler。

而它的handler需要继续对它调用then来指定。如果没特别的事，这个Promise可以直接忽略。这个Promise对象可以一直放着，甚至作为参数传递，它一直忠实地记录着它的状态和返回值。

如果then函数的返回值是个Promise，这个Promise就不会被直接当做新Promise的返回值了。新的Promise等这个返回的Promise完成或者拒绝，才相应地设定自己的结果。

如果一个Promise没有指定某个handler，它会用下一个Promise的对应handler作自己的。如果还没有，接着用下一个的。总之，如果需要，它那个该执行的handler会等待，直到某个promise有对应的handler为止。这个效果其实相当于成功和失败各有一个默认handler：默认成功handler直接返回给它的参数，默认失败handler直接throw给它的错误。

## C++11中的Promise

C++11中，Promise可以用来解决下面这个问题：

线程B正在忙碌，线程A要等B的工作完成才能继续工作。线程A需要等待B通知它。请问B该怎样通知A？

一个naive的方法就是声明一个原子布尔变量，初始化为false。A循环读，直到B修改它为true时退出循环。代码如下：

```c++
std::atomic<bool> flag(false);
// 线程A
while(!flag);
// 线程B
// 完成计算
flag = true;
```

但这样的代码会让A一直忙碌。A既然什么都没在做，最节省资源的做法应该是让这个线程挂起来。但死循环会导致CPU时不时地切换回这个线程，造成资源浪费。

另一个办法就是利用Promise。

```c++
std::promise<void> p;
// 线程A
p.get_future().wait();
// 线程B
p.set_value();
```
