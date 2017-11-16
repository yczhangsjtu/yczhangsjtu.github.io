---
title: Boost处理时间
---

# Boost处理时间

## 计时

使用timer类来计时。
```C++
#include <boost/timer.hpp>
using namespace std;

timer t;
// Do something
t.elapsed();
```
接口简单，易于使用，但精度比较差，而且依赖于平台。

另一个progress_timer类继承自timer，接口相同，不过在析构的时候会自动输出时间。构造的时候也能传入输出流指定自动输出到哪里。

## 进度

progress_display类可以显示一个进度条。

## 日期和时间

date_time类处理日期和时间。

### 日期

date_time的日期支持从1400-01-01到9999-12-31，位于命名空间gregorian中，需要头文件boost/date_time/gregorian/gregorian.hpp。使用这个类中的某些函数时，需要链接库选项-lboost_date_time。

处理日期的核心类为date，内部使用一个32位整数来存储日期，是一个轻量级的对象，可以被快速复制。

```C++
date d1;
date d2(2000,1,1);
date d3(2000,Jan,1);
date d4 = from_string("2000/1/1");

cout << d4.year() << endl;
cout << d4.month() << endl;
cout << d4.day() << endl;
cout << d4.day_of_week() << endl;
cout << d4.day_of_year() << endl;
date::ymd_type ymd = d4.year_month_day();
cout << ymd.year;
cout << ymd.month;
cout << ymd.day;

cout << to_simple_string(today) << endl;
cout << to_iso_string(today) << endl;
cout << to_iso_extended_string(today) << endl;
```

可以用day_clock类提供的静态函数获得当前日期。

```C++
cout << day_clock::local_day() << endl;
cout << day_clock::universal_day() << endl;
```

### 日期长度

date_duration类表示日期长度。很多方面像整型，支持加减法和递增递减，以及整数除法，还有全序比较。它有一个别名days。
类似的类还有`months`和`years`，`weeks`。它们之间可以相互加减。

### 日期区间

date_period类表示日期区间。

## 时间长度

posix_time命名空间用来处理时间。需要包含头文件boost/date_time/posix_time/posix_time.hpp。

time_duration类表示时间长度。支持多种时间长度分辨率：hours, minutes, seconds, milliseconds, microseconds和nanoseconds。

需要访问时分秒时，用`hours()`，`minutes()`和`seconds()`函数。如果需要获得总秒数、毫秒数等，可以用`total_seconds()`，`total_milliseconds()`和`total_microseconds()`函数。

默认时间精度为微秒，秒以下的全部时间用微秒来计。如果需要纳秒级别的精度，需要在头文件的包含语句前定义宏BOOST_DATE_TIME_POSIX_TIME_STD_CONFIG。

## 时间点

时间点ptime包含一个日期date和一个time_duration。创建时间点时，指定这两个参数。如果不指定time_duration，则默认为当天的零点。访问这两个参数的成员函数分别为date()和time_of_day()。

second_clock和microsecond_clock两个类提供了静态函数local_time()和universal_time()可以获取当前时间。

## 时间区间

time_period和date_period类似。前者就像后者的高精度增强版。
