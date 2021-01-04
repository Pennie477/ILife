# react 基础

本项目的代码将全部使用ES6语法

本文档涉及react基础语法的回顾，可以跳过回顾直接前往 `## 代码分离`

## hello world

将一个最简单的组件渲染到页面上。

```jsx
import React from 'react'
import { render } from 'react-dom'

// 定义组件
class Hello extends React.Component {
    render() {
        // return 里面写jsx语法
        return (
            <p>hello world</p>
        )
    }
}

// 渲染组件到页面
render(
    <Hello/>,
    document.getElementById('root')
)
```

**解析，这里`import React from 'react'`引用的是什么？**

这里的`'react'`对应的就是`./package.json`文件中`dependencies`中的`'react'`，即在该目录下用`npm install`安装的 react 。npm 安装的 react 的物理文件是存放在 `./node_modules/react`中的，因此引用的东西肯定就在这个文件夹里面。

打开`./node_modules/react/package.json`找到`  "main": "react.js",`，这里的`main`即指定了入口文件，即`./node_modules/react/react.js`这个文件。那么，问题的答案自然就出来了。


## jsx语法

jsx 语法和 html 很相似但是又有那么几点不一样。下面是jsx语法的一些特点。

### 使用一个父节点包裹

jsx 中不能一次性返回零散的多个节点，如果有多个请包涵在一个节点中。例如，

```jsx
// 三个 <p> 外面必须再包裹一层 <div>
return (
  <div>
    <p>段落1</p>
    <p>段落2</p>
    <p>段落3</p>
  </div>
)
```

再例如：

```jsx
// { } 中返回的两个 <p> 也要用 <div> 包裹
return (
  <div>
    <p>段落1</p>
    {
      true 
      ? <p>true</p>
      : <div>
        <p>false 1</p>
        <p>false 2</p>
      </div>
    }
  </div>
)
```

### 注释

jsx 中用`{/*  */}`的注释形式

```jsx
        return (
            // jsx 外面的注释
            <div>
                {/* jsx 里面的注释 */}
                <p>hello world</p>
            </div>
        )
```

### 样式

对应 html 的两种形式，jsx 的样式可以这样写：
css样式：`<p className="class1">hello world</p>`，注意这里是`className`，而 html 中是`class`
内联样式：`<p style={{display: 'block', fontSize: '20px'}}>hello world</p>`，注意这里的`{{...}}`，还有`fontSize`的驼峰式写法

### 事件

拿 click 事件为例，要在标签上绑定 click 事件，可以这样写

```jsx
class Hello extends React.Component {
    render() {
        return (
            <p onClick={this.clickHandler.bind(this)}>hello world</p>
        )
    }

    clickHandler(e) {
        // e 即js中的事件对象，例如 e.preventDefault()
        // 函数执行时 this 即组件本身，因为上面的 .bind(this)
        console.log(Date.now())
    }
}
```

注意，`onClick`是驼峰式写法，以及`.bind(this)`的作用

### 循环

在 jsx 中使用循环，一般会用到`Array.prototype.map`（来自ES5标准）

```jsx
class Hello extends React.Component {
    render() {
        const arr = ['a', 'b', 'c']
        return (
            <div>
                {arr.map((item, index) => {
                    return <p key={index}>this is {item}</p>
                })}
            </div>
        )
    }
}
```

注意，`arr.map`是包裹在`{}`中的，`key={index}`有助于React的渲染优化，jsx中的`{}`可放一个可执行的 js 程序或者变量

### 判断

jsx中使用判断一般会用到三元表达式（表达式也是放在`{}`中的），例如：

```jsx
return (
  <div>
    <p>段落1</p>
    {
      true 
      ? <p>true</p>
      : <p>false</p>
      </div>
    }
  </div>
)
```

也可以这样使用：

`<p style={{display: true ? 'block' ：'none'}}>hello world</p>`

## 代码分离

之前的demo代码都是在一个文件中，实际开发中不可能是这样子的，因此这里就先把组件的代码给拆分开。将使用 es6 的模块管理规范。

### page 层

页面的特点是其独特性，一个页面就需要创建一个文件。不可以两个页面公用一个文件。
- 比如：创建`./app/containers/Hello/index.jsx`文件，将之前创建组件代码复制进去

```jsx
import React from 'react'

class Hello extends React.Component {
    render() {
        return (
             <p>hello world</p>
        )
    }
}

export default Hello
```
注意将`hello`组件`export`出来。
然后`./app/index.jsx`中代码就可以这样写。

```jsx
import Hello from './containers/Hello';

render(
    <Hello/>,
    document.getElementById('root')
)
```

注意，代码`import Hello from './containers/Hello';`这里可以写成`./containers/Hello/index.jsx`也可以写成`./containers/Hello/index`

### subpage 层

如果`Hello`组件再稍微复杂一点，那么把代码都放一块也会变得复杂，可以再拆分。
- 比如：创建`./app/containers/Hello/subpage`目录，然后在其下创建三个文件`Carousel.jsx` `Recommend.jsx` `List.jsx`，分别写入相应的代码（看代码文件即可），然后`./app/containers/Hello/index.js`中即可这样写

```jsx
import Carousel from './subpage/Carousel'
import Recommend from './subpage/Recommend'
import List from './subpage/List'

class Hello extends React.Component {
    render() {
        return (
            <div>
                <p>hello world</p>
                <hr/>
                <Carousel/>
                <Recommend/>
                <List/>
            </div>
        )
    }
}
```

注意，这里`import`时`.jsx`后缀省略了。

`page层`与`subpage层`是页面和复杂页面的拆分，都是页面层级的，即`page`层。
- 复杂页面拆分为`subpage`其实没啥特别的，就是把复杂页面的代码拆分一下，会更加符合**开放封闭原则**。而且，只有复杂页面才有必要去拆分，简单页面根本没必要拆分。因此，无论是`page`还是`subpage`它都是页面级别的。

### component 层

组件层的特点是公共性，把多个页面都可能用到的功能，封装到一个组件中，代码放在`./app/components`下。比如：创建`./app/components/Header/index.jsx`文件，简单写入一个组件的代码（见源码文件），然后在`./app/containers/index.jsx`中引用

```jsx
import Header from '../../components/Header'

class Hello extends React.Component {
    render() {
        return (
            <div>
                <Header/>
                {/* 省略其他内容 */}
            </div>
        )
    }
}
```


## 数据传递 & 数据变化

### props

每个页面都会使用 `Header` ，但是 Header 上显示的标题每个页面肯定是不一样的。可以这样处理：页面中引用Header时，这样写 `<Header title="Hello页面"/>`，即给 Header 组件设置一个 title 属性。而在 Header 组件中可以这样取到

```jsx
    render() {
        return (
             <p>{this.props.title}</p>
        )
    }
```

在 React 中，父组件给子组件传递数据时，就是以上方式，通过给子组件设置 props 的方式，子组件取得 props 中的值即可完成数据传递。被传递数据的格式可以是任何 js 可识别的数据结构，上面demo是一个字符串。**React 中，props 一般只作为父组件给子组件传递数据用，不要试图去修改自己的 props ，除非想自找麻烦**

### props && state

已知`props` 不能被自身修改，如果组件内部自身的属性发生变化，如何处理？—— React 提供给了 `state`，比如：

```jsx
class Hello extends React.Component {
    constructor(props, context) {
        super(props, context);
        this.state = {
            // 显示当前时间
            now: Date.now()
        }
    }
     render() {
        return (
            <div>
                <p onClick={this.clickHandler.bind(this)}>hello world {this.state.now}</p>
            </div>
        )
    }
    clickHandler() {
        // 设置 state 的值的时候，一定要用 this.setState ，不能直接赋值修改
        this.setState({
            now: Date.now()
        })
    }
}
```

还有一点非常重要，**React 会实时监听每个组件的 props 和 state 的值，一旦有变化，会立刻更新组件，将结果重新渲染到页面上**


## 智能组件 & 木偶组件

这是用 React 做系统设计时的两个非常重要的概念。虽然在 React 中，所有的单位都叫做`组件`，但是开发时最好分开，将它们分别放在`./app/containers`和`./app/components`两个文件夹中,方便管理。

- **智能组件** 在日常开发中，简称`页面`。为何说它`智能`，因为它只做很聪明的事儿，脏活累活都不干。它只对数据负责，只需要获取了数据、定义好数据操作的相关函数，然后将这些数据、函数直接传递给具体实现的组件即可。
- **木偶组件** 这里`木偶`一词用的特别形象，它总是被人拿线牵着。它从智能组件（或页面）那里接受到数据、函数，然后就开始做一些展示工作，它的工作就是把拿到的数据展示给用户，函数操作开放给用户。至于数据内容是什么，函数操作是什么，它不关心。

## 生命周期

该项目开发中常用的几个生命周期函数（hook）

- **`getInitialState`**
初始化组件 state 数据，但是在 es6 的语法中，我们可以使用以下书写方式代替

```jsx
class Hello extends React.Component {
    constructor(props, context) {
        super(props, context);
        // 初始化组件 state 数据
        this.state = {
            now: Date.now()
        }
    }
}
```
- **`render`**

最常用的hook，返回组件要渲染的模板。

- **`comopentDidMount`**

组件第一次加载时渲染完成的事件，一般在此获取网络数据。实际开始项目开发时，常用到。

- **`shouldComponentUpdate`**

主要用于性能优化。

- **`componentDidUpdate`**

组件更新了之后触发的事件，一般用于清空并更新数据。实际开始项目开发时，常用到。

- **`componentWillUnmount`**

组件在销毁之前触发的事件，一般用户存储一些特殊信息，以及清理`setTimeout`事件等。
