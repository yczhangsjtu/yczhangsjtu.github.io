---
title: 微信小程序入门
---

# 微信小程序入门

微信小程序开发起来很像前端，使用WXML（微信自己设计的一套XML），WXSS（和CSS基本没区别）和JS（支持ES6）来写页面。实际上，掌握微信小程序的开发需要有前端经验。并且小程序的页面适合用Flex布局，因此如果能掌握Flex就更好了。但是，和前端不同的是，微信小程序中没有DOM的概念，其页面中的标签也和HTML完全不同。

## 开发环境和项目结构

### 安装开发环境

微信小程序最方便高效的开发工具就是微信官方提供的开发者工具。点击[此处](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)下载微信开发者工具。

下载完成后，安装，打开，微信扫码登录。

### 新建小程序项目

扫码登录之后，点击添加小程序项目，输入项目名称和保存目录，以及AppID。每个项目都需要一个AppID。如果只是为了测试和学习，用微信官方提供的测试ID即可。

### 文件类型和项目目录结构

一般地，一个微信小程序项目会包含

* 一个pages目录，用来存放页面
* 三个以`app`为主文件名的文件，后缀分别为`.wxss`，`js`和`json`。

每个页面由四个文件组成，这四个文件的主文件名相同，即这个页面的名字，后缀分别为`.wxml`，`.wxss`，`js`和`json`。这四个文件必须在一起。通常会把每个页面的四个文件单独放到以这个页面的名字命名的文件夹下（但这并不是必须的）。一个页面的**路径**是从项目顶层目录为根目录，到页面的名字的路径，一个典型的页面路径如：

```
/pages/main/main
```

对应这个页面，项目目录中会有如下四个文件：

```
/pages/main/main.wxml
/pages/main/main.wxss
/pages/main/main.json
/pages/main/main.js
```

## 小程序页面设计

设计微信小程序很像设计一个网站或一个手机APP，设计若干页面，并将其用跳转链接起来。

### 页面配置

每个页面的JSON文件对这个页面的一些属性进行配置。

配置导航栏的颜色的关键字为`navigationBarBackgroundColor`。

配置导航栏标题的关键字是`navigationBarTitleText`。

### 注册小程序页面

每个小程序的页面都需要在全局配置文件`app.json`中注册。具体是，在其中的一个数组类型的关键字`pages`（如果没有，就自己添加上）中添加这个页面的路径。如果在`app.json`中添加一个还不存在的页面路径，系统会自动根据路径生成这个页面的四个文件。这可以用来快速创建一个页面。数组`pages`中的第一个路径指向的页面被视为启动页面，即刚进入小程序时进入的页面。

### 页面中的基本组件

小程序页面中最常用的是三类组件（即HTML标签）：`<view>`，`<image>`和`<text>`。其中，`<view>`的作用和HTML中的`<div>`相同，后两者分别用来放置图片和文字。

> 文字不放在`<text>`标签里也可以呈现出来，但无法被长按选中。

### 微信小程序内置的高级组件

**Swiper**组件一般用来做轮播图。用法很简单，在`<swiper>`标签中列举`<swiper-item>`标签，每个`<swiper-item>`标签包含的内容即为一个页面的内容。内容可以没有任何组件（纯文字），也可以是复杂的标签组合。例如

```html
<swiper>
    <swiper-item>
        <text>Title One</text>
        <image src="src/to/first/image"></image>
    </swiper-item>
    <swiper-item>
        <text>Title Two</text>
        <image src="src/to/second/image"></image>
    </swiper-item>
    <swiper-item>
        <text>Title Three</text>
        <image src="src/to/third/image"></image>
    </swiper-item>
</swiper>
```

**Swiper**组件有如下属性：

| 属性 | 功能 |
| -------------- | ---------------------------------- |
| indicator-dots | 显示一排点，标示目前滚动到哪个页面 |
| autoplay       | 是否自动切换                       |
| interval       | 自动切换的时间间隔                 |
| vertical       | 是否竖直滚动                       |

### 页面的脚本

自动生成的页面的JS文件中，会看到全局只有一个语句`Page(...)`，传入一个JS对象，这个对象中指定了一些参数和函数，包括一个JSON对象`data`和函数`onLoad()`，`onShow()`，`onReady()`，`onHide()`和`onUnload()`。其中，`data`对象会在数据绑定中用到。后面五个函数定义了页面的生命周期。

类似NodeJS，在JS文件中可以用`require`语句引入其他脚本文件的内容，传给`require`的路径只能为相对路径。

### 页面的样式

WXML文件里不需要`<link>`引用WXSS文件，而是自动应用同一个页面的WXSS文件的样式。

### 移动端分辨率

微信小程序中引入了一个长度单位**rpx**，它的作用是让同一个组件在不同的设备上占据相同的屏幕宽度比例。

客户端常用的长度单位有**点（pt）**和**像素（px）**。pt是一个绝对的长度单位（1pt约为0.35mm），用pt做单位的分辨率被称为逻辑分辨率。px是像素点的个数，表示的长度取决于设备的像素点大小。

在iPhone 6上，1px=1rpx=0.5pt。iPhone 6的设计图一般会给750px的宽度，但微信小程序模拟器的宽度只有375px。设计图中组件的宽度用px为单位，在小程序中则要保持数字不变，单位改为rpx。

不过，文字大小有时不能用rpx，而是用px，以免在不同的设备上文字过大或过小。

**PPI（Pixel Per Inch）**表示每英寸包含了多少个PX。

## 数据绑定

小程序没有DOM的概念，因此，要用JS操纵页面中的内容，需要用到数据绑定。将页面中的内容和JS中的变量绑定起来，在JS中直接操作变量，即可改变页面中的内容。

在JS文件中的`data`关键字中定义要绑定的变量。在WXML文件中，用双花括号{% raw %}`{{}}`{% endraw %}把`data`关键字中的变量插入进去。双花括号{% raw %}`{{}}`{% endraw %}中还可以插入更复杂的JS表达式。

最后，在JS文件的函数中，用`this.setData()`设置`data`的值。这是因为改变`data`之后需要重新渲染页面。不过，在`onLoad()`函数中设置`data`时，可以直接对其中的关键字赋值。

### 控制标签是否显示

给一个WXML标签的`wx:if`属性赋值，可以控制这个标签是否出现在最终渲染的页面中。

{% raw %}
```html
<text wx:if="{{show_text}}"></text>
```
{% endraw %}

> 这个属性接受的是布尔类型的值，如果指定的是字符串，则只有空字符串代表`false`；其他的，即使是`"false"`字符串，也会被解释为`true`。

### 列表渲染

如果要在WXML中生成一组相同的内容，可以用`<block>`标签。如

{% raw %}
```html
<block wx:for="{{array}}" wx:for-item="item">
    <text>{{item}}</text>
</block>
```
{% endraw %}

其中`array`是被循环遍历的数组，每个元素都会作为变量`item`传入`<block>`标签的内容中，将这些内容渲染一遍。渲染结束后的页面中，`<block>`标签消失，`<block>`中的内容被复制若干遍，直接出现在`<block>`所在的位置。如果`array`等于`['A', 'B', 'C']`，则上述代码渲染的结果为

```html
<text>A</text>
<text>B</text>
<text>C</text>
```

> `wx:for-item`属性的默认值即为`"item"`，因此以上示例中这个属性可以省略。
>
> 如果要访问到数组索引，可以用`wx:for-index`属性定义索引的变量名，其默认值为`"index"`。

## 内容的复用：模板

模板是一种比列表渲染更灵活的内容复用。如果说列表渲染相当于编程语言中的循环，模板则相当于编程语言中的函数。

模板只复用内容和样式，因此一个目标只需要两个文件，WXML文件和WXSS文件。这两个文件可以放在项目的任何地方，名字也不一定相同。不过，一般情况下，会把它们取相同的主文件名，一起放在单独的文件夹里。

在模板的WXML文件中，放置一个`<template>`标签，设置`name`属性，并在标签中放入需要复用的内容。

```html
<template name='temp-name'>
    <text>Title</text>
    <image src='/path/to/image'></image>
    <text>Content</text>
</template>
```

在用到这个模板的WXML文件中

1. 在文件开头，使用`<import>`标签导入模板文件，`src`属性指定模板的路径（可以是相对或绝对路径）；
2. 在需要插入模板内容的地方，放置`<template is='temp-name'>`标签。

像函数一样，模板也可以接受参数，方式为设置`<template>`标签的`data`属性：

{% raw %}
```html
<template is="temp-name" data="{{key1: value1, key2: value2}}"></template>
```
{% endraw %}

在模板中可以直接使用`data`中包含的关键字。

同时，在WXSS文件中也要引用模板的样式文件，方式是用`@import`语句：

```css
@import 'path/to/template.wxss'
```

> 模板中如果需要用到`<image>`标签，标签的`src`属性要指定为绝对路径，因为模板的内容可能会在项目中任何地方出现。

## 事件处理

WXML中的标签支持响应许多触屏事件，主要包括两类，一类是接触事件，包括`touchstart`，`touchmove`和`touchcancel`，分别开始手指接触标签、在标签上滑动和离开事件。另一类表示触屏事件，包括`tap`和`longtap`事件，分别表示快速触屏一次和长按触屏。这两类事件分别类似于桌面系统中的鼠标按下和鼠标点击。

为了监听标签事件，需要设置标签的属性。每个事件对应的属性的名称为事件名之前加上“bind”。所以`tap`事件对应的属性就是`bindtap`，属性值为字符串，表示事件处理函数的名字。事件处理函数写在JS文件中给`Page`函数传入的JS对象里。

例如，在WXML中，设置一个文字标签对触屏事件做出响应。

```html
<text bindtap="onTextTap"></text>
```

在JS文件中，定义对应的事件处理函数

```js
onTextTap: function(event) {
    wx.navigateTo({
        url: '/pages/main/main'
    })
}
```

于是点击这个文字标签时，就会跳转到`/pages/main/main`路径对应的页面。

>  **冒泡事件**：如果一个事件是冒泡事件，子元素会把这个事件传递给父元素。在微信小程序中，大部分事件都是冒泡事件。如果要在标签上阻止一个事件冒泡，将标签中对应事件的属性名开头的“bind”改成“catch”。

事件对象（上述示例中的`event`参数）中包含产生该事件的标签的信息。当不同的标签可能触发同一个事件处理函数时，事件中包含的标签的信息可以用来区分该事件是在哪个标签上触发的。访问的`event.currentTarget`属性获取标签对象。访问`event.currentTarget.dataset`对象获取标签的属性值。

WXML标签可以设置一类特殊的属性，向事件处理函数传递额外的数据。这些属性以`data-`开头，然后接上数据的名称，数据的名称为全小写，多个单词之间用短横线连接。比如`data-post-id`。在事件对象中，这个数据的名称被转化为驼峰格式的关键字，如`event.currentTarget.dataset.postId`。

## 全局内置方法

微信小程序提供了一些内置方法，可以实现一些常用的功能。这些内置方法被包装在`wx`模块中。这个模块不需要引用，在小程序的任何脚本中都可以使用。

### 页面的跳转

最常用的跳转方法是`wx.navigateTo()`，传入的参数为一个JS对象，至少需要指定`url`这一个参数。`url`为目标页面的路径。如下所示

```js
wx.navigateTo({
	url: '/pages/posts/post'
});
```

跳转到的页面会成为原页面的子页面，原页面的`onHide()`函数会执行，但不会执行`onUnload()`。新的页面左上角会有后退按钮，可以后退回原页面。

页面之间的跳转关系构成了页面的层级结构。打开后直接进入的页面为一级页面。从一级页面使用`navigateTo`跳转到的页面为从属于这个一级页面的二级页面。一级页面和二级页面仅仅是程序运行时的概念，和页面在项目中存放的位置没有任何关系。

另外一个跳转函数为`wx.redirectTo()`，用法完全相同，但跳转到的页面和原页面平级。跳转时，原页面的`onUnload()`函数会执行。

跳转到另一个页面时，可以传递参数，方法为在`url`后面附加`?`和`key=value`对，就像HTTP那样。传递给另一个页面的参数会以JS对象的格式传入这个页面的`onLoad()`函数的第一个参数，一般命名为`options`。

### 缓存

微信小程序的缓存是一个全局的键值对数据库。缓存用来存储一些永久数据，这些数据在小程序退出时能够保存下来。微信小程序限制缓存中内容的大小不超过10M。

向缓存中存储数据的方法为

```js
wx.setStorageSync('key', value)
```

其中`value`可以为JS对象或字符串。

上述方法为同步方法。异步方法的调用方式为

```js
wx.setStorage({
    key: value
})
```

从缓存中读取的方法为

```js
wx.getStorageSync('key')
```

删除缓存中的数据的方法为

```js
wx.removeStorageSync('key')
```

上述两个方法都有其对应的异步版本。

清除所有缓存则用

```js
wx.clearStorage()
```

### 通知

微信小程序提供下列向用户弹出通知的方法：

#### Toast

向用户展示一些内容，过一段时间后自动消失。

```js
wx.showToast({
	title: 'title',
	icon: 'success'/'loading',
	duration: 1500,
})
```

其中`icon`参数只支持`success`和`loading`两个选项，前者会显示一个对号图标，后者会显示一个正在加载的动画。`duration`参数指定通知存在的时间，单位为毫秒。

#### 对话框

停留在页面上，等待用户关闭，可以让用户点选“确认”或“取消”按钮。

```js
wx.showModal({
	title: 'title',
	content: 'content',
	showCancel: true,
	cancelText: 'Cancel',
	cancelColor: '#ffffff',
	confirmText: 'Confirm',
	confirmColor: '#ffffff',
})
```

#### 弹出菜单

在页面上弹出一个菜单，让用户选择。

```js
wx.showActionSheet({
	itemList: [
		'action1',
		'action2',
	],
	itemColor: '#ffffff',
	success: function(res) {
		// res.cancel 表示用户是否没有点任何选项
		// res.tapIndex 表示用户点了哪个选项
	}
})
```

### 播放音乐

下面的函数用来播放背景音乐。

```js
wx.playBackgroundAudio({
	dataUrl: 'http://url.mp3',
	title: 'title',
	coverImgUrl: 'http://url.png',
})
```

其中，音乐的URL和封面图片都不能用本地文件，只能用网络流媒体。

其他操作背景音乐播放过程的函数有

```js
wx.pauseBackgroundAudio()
wx.stopBackgroundAudio()

wx.onBackgroundAudioPlay(callback)
wx.onBackgroundAudioPause(callback)
wx.onBackgroundAudioStop(callback)
```

### 向服务器请求数据

发送HTTP请求。

```js
wx.request({
	url: 'URL',
	data: {},
	method: 'GET',
	success: function(res) {

	},
	fail: function() {

	},
	complete: function() {
	
	}
})
```

如果发送POST请求，`data`中包含的键值对会被转换为`key=value`形式，放在HTTP内容中。

## 全局配置

### 页面配置

全局配置文件`app.json`存储小程序的全局配置。其中，`window`关键字中的JSON结构体为每个页面的配置选项设置了缺省值。比如，如果需要配置所有页面的导航栏为一样的颜色，只需设置`app.json`中`window`关键字中的`navigationBarBackgroundColor`关键字即可。如果个别页面需要配置不同的颜色，则在页面的配置文件中进行覆盖。

在`app.json`中设置`tabBar`关键字，可以在页面中增加选项卡栏。设置`tabBar.list`为一个数组，数组的每个元素为一个JSON对象，至少要设置`pagePath`和`text`两个属性。还可以设置`iconPath`和`selectedIconPath`，分别表示这个选项按钮未选中时的图标和被选中时的图标。只有`tabBar`中包含的页面才会出现选项卡栏。

### 全局变量

在`app.js`中，类似于页面的JS文件，全局只有一个函数调用，即`App({...})`，传入一个JS对象。这个JS对象可以在每个页面中访问，因此可以看做全局变量。

```js
App({
	someData: {
		key: value
	}
});
```

在页面中访问全局变量时，调用`getApp()`即得到这个JS对象。

```js
var app = getApp();
var value = app.someData.key;
```

另外，类似于页面，App对象有三个生命周期函数
1. onLaunch()
2. onShow()
3. onHide()

## 开发环境IDE功能

alt+shift+F：自动优化代码格式