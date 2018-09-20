---
title: C++11 tuple类型
---

# C++11 tuple类型

当我们想将一些数据组合成单一对象，又不想麻烦地定义一个新的数据结构时，就可以用tuple。相关内容定义在`<tuple>`头文件中。

### 创建tuple对象

直接创建并初始化每个元素

```c++
tuple<int,string> a(1,"abc");
tuple<int,string> b{1,"abc"};
auto c = make_tuple(1,"abc");
```

### 访问tuple对象

访问tuple中第i个对象

```c++
tuple<int,double,string> d(1,2.3,"abc");
cout << get<0>(d) << endl;
cout << get<1>(d) << endl;
cout << get<2>(d) << endl;
```

访问tuple的元素个数

```c++
tuple<int,double,string> d(1,2.3,"abc");
typedef decltype(d) dtype;
cout << tuple_size<dtype>::value << endl;
```

访问tuple中第i个元素的类型

```c++
tuple<int,double,string> d(1,2.3,"abc");
typedef decltype(d) dtype;
typedef tuple_element<1,dtype>::type second_type;
cout << sizeof(second_type) << endl;
```

### tuple对象的比较

只有元素个数相等且对应位置的类型可进行该比较操作（==或<）时，才能对两个tuple类型的对象进行比较。两个tuple对象的大小比较默认用字典序。
