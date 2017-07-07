# Effective Modern C++

## enum class
C++98中被`enum`定义的变量范围和该`enum`本身一样。而C++11引入了`enum class`将`enum`定义的变量限制在`class`的名字里，类似于Java中的`enum`（但为兼容性考虑，还是保留了原有的`enum`特性）。

`enum class`可以预先声明，`enum`不可以，除非指定用哪种整型来实现，如：

```c++
enum Color: std::uint8_t {
	red, blue
};
```

`enum class`的类型明确限定为该类型，而`enum`可以强制转换为整型。

`enum`比起`enum class`有一个好处，那就是在使用`std::tuple`的时候，获取一个`tuple`的某个域元素需要用到模板函数`std::get<1>(item)`，而把`1`换成`enum`类型的变量可以大大提高代码的可读性。如果用`enum class`代码就会复杂得多。

## delete member function
你有可能不想让你的类的用户调用某个函数，办法就是不声明它。但有时C++会自动生成拷贝构造函数和赋值运算符，这时候防止用户调用之的方法就是把它们声明为`private`而不定义它们。这样试图使用它们就会造成编译错误。

C++11有个更高级的做法来代替这种原始的方式，那就是在函数声明后面加上`= delete`。通常把这样的函数声明为`public`，这样使用者能从编译错误中看出这个函数“作者不希望它被调用”，而不是因为它是私有的。

删除函数的机制可以用来防止用户将浮点型数作为参数传给只希望接受整型的函数。

删除函数的机制可以用来阻止模板成员函数被实例化为特定的类型，只需在函数声明外本该定义该函数的地方把大括号换成`= delete`。

## overriding function
覆盖基类的virtual函数时，用基类的指针指向派生类的对象，调用被覆盖的函数，仍然是执行派生类的实现。这一点和Java相同。

（为什么派生类的覆盖函数仍然可以是virtual的？）

函数覆盖要求**一切**都完全相同，并且基类函数必须是virtual的（这一点不像Java，函数不一定要是abstract才能被覆盖）。

为了让编译器帮忙检查小疏忽造成的覆盖错误，可以在派生类的函数声明后面加上`override`关键字（这就像Java的`@override`）。

为了兼容性考虑，C++11增加的这个`override`关键字只在这一个地方被保留，其他地方可以随便用。类似的一个关键字是`final`。

C++11中，函数的参数可以被限定必须是左值或者右值。左值的限定和C++98一样，一个&符号即可。但只能传为右值的参数是C++11引入的，把一个&符号换成两个&&。同样，成员函数也可以在后面加一个或两个&符号，声明调用该成员的对象必须为左值或者右值。

## const iterator
C++98中的`const_iterator`用起来很不方便，导致程序员一般不用。

C++11中，container增加了`cbegin()`和`cend()`等函数可以返回`const_iterator`类型的迭代器，并且`const_iterator`可以和`iterator`一样用来指定容器中的插入和删除位置。

如果要追求通用性，选择全局的`cbegin(container)`和`cend(container)`函数。

## noexcept function
当编译器处理`noexcept`函数时，可以不必维护一个调用堆栈，从而得到更优化的代码。

有的标准库函数（如`swap`）是否是`noexcept`的取决于用户定义的函数是否是`noexcept`的。可以确定不会抛出异常的函数需要尽量声明为`noexcept`。

一定要在代码确实不会抛出异常的时候来声明`noexcept`，而不要为了声明`noexcept`来强行把代码实现成不会抛出异常。

生产函数和析构函数默认为`noexcept`的，除非函数实现中调用了明确表示会抛出异常的函数，但这种情况非常少见，标准模板库里是没有的。如果用户定义了这样的类并交给模板库去调用，会产生未定义的结果。

## constexpr
`constexpr`的字面意思是指在编译的时候就确定的值。但用它来修饰函数的时候，意义会大不一样！

对`constexpr`的对象而言，可以理解为编译的时候就固定，因此它们有特权出现在很多地方，包括编译器要求必须是字面值常量的地方。

当`constexpr`用来修饰函数时，对该函数的每一个调用，如果参数在编译时就确定，那么该函数的返回值也是编译时确定的，此时这个函数可以用在需要一个编译时确定的值的地方。但如果有哪怕一个参数不能在编译时确定，这个函数就和普通函数没什么区别了。

比如说，如果我们需要一个数组，大小为$3^n$，我们可以手动算出来，但代码的可维护性就会变差。但用一个函数来算的话，结果不能作为数组大小。这时可以用`constexpr`写一个计算幂的函数。

当然，`constexpr`的函数需要在编译时就被算出来，编译器不能随随便便就调用任何函数，这样的函数的实现就需要限制。在C++11里，这个限制很强：最多一个`return`语句。在C++14里面，这个限制放松了一些，可以用循环了。

在C++11里，返回值只能是除了`void`之外的内置类型。而在C++14里，用户定义的类型也可以是字面值的（这样的类需要有一个`constexpr`的工厂函数），这样的类型也可以作为`constexpr`函数的返回值。

## const member function
将成员声明为`mutable`可以允许被声明为`const`的函数修改它们，实现一些特殊的应用。但`mutable`变量的存在导致多线程同时调用`const`函数时也有可能产生冲突（本来`const`函数是只读的，两个线程同时读一段内存不会有什么问题）。这时需要用`mutex`。也可以用`std::atomic`，但不能同时有两个这样的变量。

## special member function
所谓特殊成员函数，就是C++会自动帮你生成的成员函数。在C++98中，有这么四个函数：默认的工厂函数，析构函数，复制工厂函数，和赋值运算符（后两个统称为copy操作）。如果它们没有被声明却被调用了，编译器不会报错而是默默生成一份自己的代码。

在C++11中，又多了两个特殊成员函数，就是move工厂函数和move赋值运算符（即参数必须是右值，统称为move操作）。区别是，这两个copy操作是完全独立的，实现了一个不会阻止编译器自动生成另一个。但两个move操作并不独立，也就是说它们会用同一个实现。

另外，如果任何一个copy操作被声明了，那么编译器会认为默认的逐个元素复制的拷贝方式是不合适的，也就不会默认生成move操作函数了。同样，声明一个move操作也会阻止编译器自动生成copy操作函数。虽然这些函数的类型并不相同。

The Rule of Three宣称，只要声明了copy工厂函数，copy赋值函数和析构函数中的任何一个，就最好把三个函数都一起声明了，毕竟其中任何一个被定义都意味着编译器默认生成的逐个元素赋值的方式并不正确。C++11支持析构函数的声明可以阻止编译器自动生成move操作。

如果你声明了析构函数，同时还想让编译器自动生成move函数，可以在move函数后面加上`= default`。

## smart pointers
有一百种理由舍弃原始的指针，拥抱C++11提供的智能指针。

C++11有四种智能指针：`auto_ptr`，`unique_ptr`，`shared_ptr`，`weak_ptr`。不过`auto_ptr`是C++98的过时产物，被`unique_ptr`代替了。

## unique pointer
当你需要指针时，`unique_ptr`是通常的选择。一般场合可以代替原始指针，大小和效率都可以和原始指针相匹配。`unique_ptr`无法复制，因为要保证它指向的内存只能被释放一次。`unique_ptr`在被销毁时自动释放内存。

`unique_ptr`可以由用户来指定销毁函数，不过模板里需要增加销毁函数的类型，同时传入销毁函数作为参数。建议用lambda函数作为销毁参数，而不是用函数指针。

## shared pointer
`shared_ptr`维护一个control block，其中记录着所指向对象的reference count，当一个`shared_ptr`不再指向某个对象时（被赋值或被析构）就把reference count减一，如果减到零就释放内存。

因为`shared_ptr`不能让被指向的对象感觉到自己的存在，它没有办法从对象本身获知还有哪些`shared_ptr`指向了该对象，所以一旦不小心使用就会造成一个对象有多个control block，意味着它会被释放多次。因此，使用`shared_ptr`时尽量用一个`shared_ptr`来生成另一个，而不要多个都直接从对象本身生成。最好直接把`new`语句传给`shared_ptr`的构造函数，这样构造的对象就只能通过这个`shared_ptr`来访问了。

`shared_ptr`可以从`unique_ptr`直接赋值得来（这个过程会把`unique_ptr`赋值为零），但反过来却不行，`shared_ptr`不能赋值给`unique_ptr`。

`shared_ptr`也可以由用户定义析构函数，但这个函数不影响它的类型，只是把一个函数指针存在control block里。

和`unique_ptr`不同的是，`shared_ptr`不可以指向数组。

## weak pointer

`weak_ptr`就像一个`shared_ptr`，只是它不影响control block，不占有对象的拥有权，因此有可能会指向已经释放的内存。看起来就像个原始指针了。不过它比原始指针智能一点，就是当它所指向的对象被释放时它是知道的，可以调用它的`expired()`函数来检查这一点。

`weak_ptr`不能被取值，甚至不能被检验是否是空指针。不过，这些功能可以通过从`weak_ptr`构造`shared_ptr`来实现。一种方式是调用`weak_ptr`的`lock()`函数得到指向同一内存地址的`shared_ptr`，如果内存已经被释放，得到的就会是空指针。另一种方式是将`weak_ptr`作为参数传给`shared_ptr`的构造函数。如果内存已经被释放，就会抛出异常。

`weak_ptr`可以解决`shared_ptr`出现循环时造成的内存泄露问题。把一个指针换成`weak_ptr`可以打破这种循环，否则，这种循环让每个指针都保持着至少一个reference count，即使程序中不再有指向它们的指针，它们也会保留在内存中，造成内存泄露。

实现上，虽然`weak_ptr`不影响reference count，但它和`shared_ptr`一样，指向同一个对象的`weak_ptr`和`shared_ptr`维护着同一个control block，因为它要判断指向的内存是否已被释放。另外，它对control blo并不是完全只读，它和`shared_ptr`一样在control block中维护着一个类似的weak count。

## make_unique and make_shared

`make_unique`在C++11中不存在（只有`make_shared`），到C++14才有。不过很容易写一个：

```c++
template<typename T, typename... Ts>
std::unique_ptr<T> make_unique(Ts&&... params)
{
  return std::unique_ptr<T>(new T(std::forward<Ts>(params)...));
}
```

从这代码也可以看出，`make_unique`和`make_shared`大概是怎么工作的。这两个函数的功能是用来代替C++98中的`new`的：给定要生产的对象的工厂函数的参数，用这些参数构造一个新的对象，并返回一个智能指针。还有一个同样功能的函数是`allocate_shared`，和`make_shared`的区别是需要传给它一个allocator对象。

比起`new`，它的第一点好处就是只输入了一遍对象的类型。

另外，`make_shared`只是一个函数调用，而`shared_ptr<T>(new T)`的过程中`new T`和`shared_ptr`的工厂函数执行过程是分开的，一旦中间出现异常，就可能造成内存泄露。

`make_shared`可以将对象的内存和control block的内存一起分配，提高效率。

有些时候只能用`new`而不能用`make_unique`，比如需要指定销毁函数，后者没有这个接口。

另外一点就是，如果需要用大括号`{}`来初始化，`make_unique`不会这么把参数传给构造对象的工厂函数。

这两个问题对`make_shared`也同样。不过`make_uniqe`的问题就到此为止了，`make_shared`还有别的问题。

首先，`make_shared`用来生成有自定义`operator new`和`operator delete`的类的对象很有问题。

另外，因为`make_shared`把对象的内存和control block的内存一起分配了，必须要等control block被释放时对象的内存才能一起释放。但如果还有`weak_ptr`存在，control block就会多活一段时间。如果对象的内存占用很大，这个开销也就不小。但如果用`new`，这个问题就不存在，因为对象可以先释放，control block等多久都没问题。

## Pimpl Idiom

所谓的Pimpl Idiom，就是如果类A有一个类B的成员，`A.h`里只保留一行`class B`的声明，`class A`的声明中只放一个B的指针，这样`A.h`不必引用`B.h`头文件（当然`A.cpp`里还是需要的），免得B的头文件的频繁改动导致所有包含`A.h`的文件频繁重新编译。

对`A.h`来说，`class B`的类型是不完整的。Pimpl Idiom就是利用了对不完整的类型仍然可以声明一个指针这样的特性。因为指针大小都是一样的，不影响类的内存布局。

把Pimpl Idiom和`unique_ptr`一起用时，却有可能出问题。问题出在special member function上，也就是编译器会自动帮你生成的那几个函数上。因为用智能指针时，不需要自己释放内存，也就不需要自己写析构函数，这时候编译器自动生成析构函数。默认的构造函数和析构函数，会检查被释放指针的类型是不是完整的。所以要确保编译器处理这些函数时，一定要知道完整的类型信息。所以，一定要在`A.cpp`里给出`A`的构造函数和析构函数的定义。

如果用`shared_ptr`，则以上问题全不存在。因为`unique_ptr`和`shared_ptr`不同之处在于，`unique_ptr`的类型包含其deleter的类型，所以释放它的时候必须知道对象的完整类型。`shared_ptr`没有这回事。

## std::move and std::forward

一句话解释`move`和`forward`：`move`什么也不move，`forward`什么也不forward。它们完全不产生任何可执行代码，连一个字节都没有。

换句话说，它们实际上不过是强制转换而已。`move`把对象强制转换为右值，`forward`也一样，不过需要满足一定条件。

下面是C++11里的一个`move`的实现样例（并不完全符合标准，但也快了）。

```c++
template<typename T>                       // in namespace std
typename remove_reference<T>::type&&
move(T&& param) {
  using ReturnType =                       // alias declaration;
    typename remove_reference<T>::type&&;
  return static_cast<ReturnType>(param);
}
```

所以还有人建议过，把`move`改成`rvalue_cast`。它之所以叫`move`，是因为它把一个对象变得“可以被挪走”。

注意`move`并不影响`const`性质，所以如果对`const`的对象实行`move`，结果就是实际上做了copy。

`forward`存在的意义是处理这样一个事实：所有的函数参数都是左值。声明为右值的参数也不过是对左值对象的一个右值引用。所以，如果在一个左右值参数版本各被`overload`的函数上套一层函数，结果就是只会调用它的左值版本。`forward`的作用就是保持参数的左右值特性。但`forward`又怎么知道这个参数在传进来前是左值还是右值呢？这个信息是在它模板参数里传进来的。

所以，`move`和`forward`的共同点就算它们都是强制转换，区别就是`move`总是转换，`forward`有时候转有时候不转。那么为什么一定要用`move`呢？都换成`forward`不行吗？答案是可以。实际上它们俩都不是必需的，因为强制转换可以自己写，不过代码会变得比较难读而已。同样，把`move`换成`forward`是要多传一个类型模板参数的，也会让代码更长更容易出错。

## Universal reference and rvalue reference

当两个&符号`&&`出现在类型后面时，它们并不总是代表着右值引用。实际上，当出现在一个待推导的类型（模板类型`T`或`auto`）后面时，它可以是任何类型的引用，所以是universal reference。

不过，universal reference的存在有很多限制条件。首先这个类型必须是单纯的一个名字比如`T`，换成模板类（比如`vector<T>`）就不行了，甚至连`const`这种修饰都会把universal去掉。

其次，模板的声明需要直接作用在函数声明上，所以模板类里的成员函数参数列表中的`T&&`仍然是右值引用而不是universal reference，除非这个函数另加了一个单独的模板声明。

当参数为universal reference的时候，要用`forward`来传递，不然如果用`move`可能会有意想不到的错误。

当函数返回参数本身时，也建议在`return`语句中用`move`和`forward`。但尽量不要在返回局部变量的时候用。

## Overload universal reference

注意，带有universal reference的函数是最贪婪的，因为它几乎能接受任何类型的参数。所以如果重载一个universal reference的函数，很多时候是不会成功的，因为重载的函数可能没有universal reference函数更匹配调用的参数。

但如果确实需要一个能够处理大部分类型的函数，只有个别特殊的类型需要特殊处理，这时候该怎么做呢？一个办法就是不要重载，换个函数名。还有就是把universal reference换成`const T&`。这都会带来效率上的问题。另外，可以直接用传值的方式`T`，然后用`move`转换为右值。

然而，如果确实需要`forward`，就只能用universal reference。我们还想要重载，怎么办呢？我们只好把处理重载的部分和实现的部分分离开，让实现函数多接受一个参数，比如`std::is_integral<T>()`。这样并不完全正确，因为对`T`的任何修饰都会让这等于`false`，所以把这里的`T`换成`typename std::remove_reference<T>::type`。而实现函数可以写成这样：

```c++
template<typename T>
void implement(T&& name, std::false_type) {
}
```

这就处理了所有非整型的类型。其他的只要实现另外一个函数，把`false_type`换成`true_type`就可以了。调用这两个函数中的哪一个，在编译时就可以被确定。

这个技巧却不能处理生产函数的情况。因为编译器自动生成的生产函数有时候却偏偏能完美对应，把重载权抢过去。这时候问题反而是universal reference不够贪婪了。这个问题的解决方案是用`std::enable_if`，不过比较复杂，这里就不写了。

## Reference collapsing

程序员是不能声明对引用的引用的，但编译器有时候可能推导出引用的引用类型来。因为引用有左右值两种，所以引用的引用就有四种。这种情况下引用会合并。如果两层引用都是右值，那么合并结果就是右值。其他情况下结果都是左值。

## Lambda expression notations

```c++
[ capture-list ] ( params ) mutable(optional) exception attribute -> ret { body }
[ capture-list ] ( params ) -> ret { body }
[ capture-list ] ( params ) { body }
[ capture-list ] { body }
```

第一个是完整的表达式，其中`exception`是可能会抛出的异常，`attribute`指定一些属性（比如`noreturn`，`carries_dependency`）。

第二个去掉`mutable`也就是将其声明为`const`。

第三个省略掉了返回类型。但在C++14中，`body`中如果只包含一个`return`语句，编译器会根据其推出返回值。否则类型就是`void`。

第四个省略了参数列表，表示这个函数没有参数。只有capture-list和body之间什么都没有的时候才能用。

Capture-list是指一列零个或多个逗号分开的capture。Capture的功能是把当前环境中的一些变量拷贝或引用到lambda表达式中。如果只有一个`[&]`，就获取所有的局部变量的引用。如果只有一个`[=]`，就获取所有局部变量的拷贝。这两个叫做default capture mode。如果这个lambda表达式不需要用局部变量，也要放一对空括号`[]`。

* **Lambda expression**: 也就是一个表达式，出现在表达式该出现的地方。
* **Closure**: lamda表达式创造出来的那个东西。Lambda表达式创造closure，就像表达式`1+2`创造出整型变量一样。
* **Closure class**: 一个closure既然是一个对象，它就有一个类。每个lambda表达式都创造出一个独一无二的closure类。

一般不必把这三个概念分得太开，但一些特别情况下理解这些概念的区别还是很重要的。

## Default capture modes

尽量不要用default capture mode。如果用来传引用`[&]`，容易创造出一个比被capture的局部变量存活更久的closure，也就会出现悬空引用（加上变量名虽然也会出现这种情况，但至少能当个提醒）。

即使是传值`[=]`也很有问题。它可能会传一个指针，然后指针指向的对象被销毁了，closure却没有。就算你发誓绝不用原始指针，你也可能会忽略掉`this`指针，因为它也会被`[=]`捕获，而这个closure也有可能比当前的对象活得更久。另外`[=]`无法捕捉`static`变量，却容易让人误以为它捕捉到了一些变量。

## Init capture

直到C++14才支持把变量move进closure。但C++11也有解决的办法。

C++14中的方式是init capture，形式是`[ var = something ]`，意思是“在closure里创造一个变量，初始化为`something`”。这里`something`可以是任何表达式。

C++11中的方式则是`std::bind`。它的第一个参数是一个closure，剩下的参数是传给closure的参数，得到的结果是一个bind object，很像一个closure，只不过某些参数被确定下来了。比如有一个lambda函数`f(x,y)`这是个二元函数，那么表达式`std::bind(f,x0)`就得到一个一元函数，把`x`固定在`x0`这个位置。

通过`bind`，可以先把需要capture的变量类型放在lambda表达式的参数列表里，再把这个变量`bind`进去。这时候就可以对这个变量用`move`了。

## Lambda and bind

`std::bind`提供了和lambda类似的功能，但无论是可读性还是效率上都远远不如，所以一般推荐用lambda。除了在C++11中需要把变量move进函数体时。