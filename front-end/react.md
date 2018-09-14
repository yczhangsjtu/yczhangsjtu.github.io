---
title: Introduction to React
---

### Introduction to React

React是一个基于JS的前端开发框架。

#### 开始

用npx（npm5.2以上自带），安装一个工具create-react-app，然后用这个工具创建一个新的react工程。一开始，只需已经安装好的npm和nodejs，别的都不需要。然后输入命令：

```bash
$ npx create-react-app my-app
```

就会自动创建文件夹`my-app`，并把一切初始化工作做好。

然后，进入这个文件夹，`npm start`，就启动服务器，并打开了网页，一切就绪，可以开始开发了。

#### 代码结构

可以看到，`my-app`目录下，有文件夹`src`和`public`。其中，`public`里放了index.html等前端文件。注意到，访问主页的请求会获取这个index.html文件，但这个index.html不会直接发送给前端，而是经过一定的处理之后才发送的。比如里面的`%PUBLIC_URL%`会被替换，而`body`的末尾会添加一行JS链接。

`src`文件夹里的是ES6的代码，这些代码会被编译成普通JS放到前端里运行。所以，在浏览器上查看网页源代码时，会发现里面什么也没有。最终呈现的内容，全都是JS动态产生的。

如果以前没有接触过ES6的话，就会发现ES6的代码和一般的JS已经差别很大了。ES6中有类的概念，此外，分号也变得可有可无。

一个简单的React主页页面呈现过程如下。

![](/assets/images/react-flow.png)

index.html文件中，会有一个`id`设为`root`的`<div>`空标签，放在`<body>`中。而`index.js`中则会看到一行代码

```js
ReactDOM.render(<App />, document.getElementById('root'));
```

这一行代码做的事情就是：找到文档中的ID为`root`的标签，将其内容渲染成App标签的内容。

#### React组件

App不是HTML标签，它到底是什么，只有React自己会理解。React会把它翻译成HTML。这个翻译是在浏览器中完成的。所以，如果你在浏览器中查看网页源代码，你会看到一个和`public/index.html`差不多的页面，里面仍然有一个`id="root"`的`<div>`空标签。但如果“检查”来查看网页源代码，就会发现这个div里已经有了一坨东西。这一坨东西就是`<App />`标签翻译出来的结果。

App是一个用户自定义的标签，这种标签被称为React组件（Component）。React组件通过扩展`React.Component`类来声明，然后定义其中的`render`方法来告诉React怎样把这个组件翻译成HTML。如下

```js
class App extends React.Component {
    render() {
        // 这里不需要引号。ES6的代码里充斥着大量的HTML，如果都要用引号的话实在太麻烦。
        return <h1>Hello World!</h1>
    }
}
```

组件可以接受参数，并在代码中引用。给组件传递参数的方法和HTML标签类似，比如

```js
<App title="Hello"/>
```

在组件的实现代码中，参数则通过`this.props`来获得。这个参数会在默认工厂函数中设置好。

```js
class App extends React.Component {
    render() {
        // 虽然没有引号，但本质上是字符串，在字符串中插入变量的值，需要用大括号作标记。
        return <h1>{this.props.title}</h1>
    }
}
```

此外，组件可以有自己的内部状态，通过`this.state`来引用。状态可以在工厂函数`constructor`中初始化

```js
class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            title: props.title
        }
	}
    render() {
        // 虽然没有引号，但本质上是字符串，在字符串中插入变量的值，需要用大括号作标记。
        return <h1>{this.state.title}</h1>
    }
}
```

注意，除了在工厂函数里可以直接给`this.state`赋值外，在其他地方需要改变`this.state`的值时，必须用`this.setState()`函数。这是因为，一个组件的内部状态改变往往会导致它的显示内容改变，JS需要在组件状态改变时对组件重新渲染，重新调用组件的`render()`函数。`setState`函数可以做这件事，但直接给`this.state`赋值是不行的。另外，`setState`是一个异步函数，所以在它调用之后，`this.state`的值不会立刻改变。所以，不要在调用`this.setState(newState)`之后试图使用`this.state`来代替`newState`。

作为一个例子，我们在App组件中加入一个按钮，按钮的功能是将`this.state.title`修改为`"Button Pressed"`。首先定义一个方法`changeTitle`

```js
class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            title: props.title
        }
	}
    changeTitle() {
        this.setState({
            title: "Button Pressed"
        })
    }
    render() {
        // 虽然没有引号，但本质上是字符串，在字符串中插入变量的值，需要用大括号作标记。
        return <h1>{this.state.title}</h1>
    }
}
```

然后将`render`函数的返回语句改为

```js
return (
    <div>
        <h1>{this.state.title}</h1>
        <button onClick={() => this.changeTitle()}>Change title</button>
    </div>
)
```

#### 构建

通过`npm start`命令，可以在开发（development）模式下运行，React会启动一个server，默认监听3000端口。此时可以修改代码，修改后的结果会立刻反映到浏览器上，无需重启服务。

然而，真正部署这个应用时，是不能用开发模式来运行的。

虽然React生成的网页是动态的，但是所有动态效果都是通过静态的JS生成的。实际上，只要一个简单的文件服务器就可以运行起这个网站。前提是，React需要把所需的静态文件预处理好，放到指定的位置，这个过程被称为构建。构建的产物是可以用在生产环境下的。`npm run build`命令就可以做这件事。

构建之后，应用目录下会产生一个build文件夹。进入这个文件夹，运行`python -m SimpleHTTPServer`（Python2）或者`python -m http.server`（Python3），一切效果都和开发模式下一样。

#### 和后端结合

如果应用已经构建好，无需改变，那么在后端代码中使用它非常简单，只需将static文件请求导向build文件夹就可以了。比如，如果用expressjs做后端，只需要在app.js中把如下一行中的`public`换成`build`文件夹的相对路径。为了简便，可以把`my-app`放到express应用的根目录下，这样`build`文件夹的路径就是`my-app/build`。

```js
app.use(express.static(path.join(__dirname, 'public')))
```

不过，一般来说，应用中会使用Ajax访问后台API，所以，在应用的开发过程中，就需要后台服务器运行起来。React自己的开发模式下的服务器并不知道怎样处理API请求，它需要把这些请求转发给后台服务器。

在`my-app/package.json`中，添加一个字段`proxy`，值设为后台服务器的访问URL，一般来说类似于`http://localhost:8080`。端口号自己来设。

有了`proxy`字段，React的开发模式下的服务器会自动把所有非静态文件请求全部转发给`proxy`指定的服务器。

![](/assets/images/react-express-flow.png)

#### 多页面

React本身也可以进行一些路由，根据不同的路径渲染不同的部件。只不过，这些路径访问到的都是同一个网页、同样的JS脚本。只是这个JS脚本根据访问的路径对页面做了不同的处理而已。这类应用有一个专门的名字，叫做Single-Page Applications。

首先，安装`react-router-dom`。

```bash
$ npm install --save react-router-dom
```

然后，在index.js中import相应的模块

```js
import {BrowserRouter as Router, Route, Link} from 'react-router-dom'
```

渲染Router组件

```js
ReactDOM.render(
    <Router>
    	<div>
    		<Link to='/'>Home</Link>
    		<Link to='/users'>Users</Link>
    		<Link to='/about'>About</Link>
    		<Route exact path='/' Component={Home}/>
    		<Route path='/users' Component={Users}/>
    		<Route path='/about' Component={About}/>
    	</div>
    </Router>
)
```
