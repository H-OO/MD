## React@16.3.0

--------------------------------------------------

**组件通信**

目的：实现组件间的`双向`和`平行`通信

**场景1：给子组件传递初始值**

通过`this.props`
```
父组件中给子组件传递id为1
<ChildComponet id="1"/>
子组件接收id
this.props.id // 1
```

**场景2：子组件改变值传递给父组件**

也通过`this.props`
```
先在父组件的state中添加一个number属性，属性值为0
this.state.number = 0;
===============================================↓↓
父组件中给子组件传递number
<ChildComponet number={this.state.number}>
子组件接收number，将其改变为1，并传递回父组件
（注意：this.props是只读的，任何修改都会报错）
===============================================↓↓
正确的姿势：再传入一个处理函数
任意定义一个属性名onNumberChange，属性值为一个处理函数（可以将处理函数先写着外部再引入）

/**
 * parent
 */
class ParentComponet extends React.Component {
  // 使用this
  constructor() {
    super();
    this.state.number = 0;
    this.parentHandler = this.parentHandler.bind(this);
  }
  // 写于外部的处理函数，用于接收新值，并重新赋值给state中的number 【2】
  parentHandler(newNumber) {
    this.setState({
      number: newNumber
    })
  }
  // 传入处理函数
  render() {
    const number = this.state.number;
    return (
      <div class="wrap">
        <div>{number}</div>
        <ChildComponet number={number} onNumberChange={this.parentHandler}>
      </div>
    )
  }
}

/**
 * child
 */
class ChildComponet extends React.Component {
  // 使用this
  constructor() {
    super();
    this.childHandler = this.childHandler.bind(this);
  }
  // onChange事件触发的处理函数 【1】
  childHandler(e) {
    // 执行父组件传入的处理函数
    this.props.onNumberChange(e.target.value); // 将新值作为参数进行传递
  }
  // 传入处理函数
  render() {
    const number = this.props.number; // 接收父组件传递过来的值
    return (
      <div>
        <input type="text" value={number} onChange={this.childHandler} />
      </div>
    )
  }
}
```

**场景3：状态共享**

通过场景2的方式，可以实现父组件中的状态通过共享给多个子组件，也可称为状态提升

状态提升就是将需要共享的状态提升到最近的父组件中

这样只要有一个组件触发状态改变，共享该状态的组件都会相应触发改变

--------------------------------------------------

### JSX语法

本质上来讲，JSX 只是为 `React.createElement(component, props, ...children)` 方法提供的语法糖

注意使用JSX语法在编译后会调用`React.createElement`方法

所以在JSX代码中必须声明React变量`import React from 'react';`

React组件的语法风格：除有效的HTML标签外，其他React组件标签名首字母必须大写！

```javascript
// JSX
<MyComponent id="1">Component</MyComponent>
// 等同于↓↓↓
// JS
React.createElement(
  MyComponent,
  {id: '1'},
  'Component'
);
```
--------------------------------------------------

**JSX点表示法**

可以通过一个对象保存多个组件，这种方法也称为点表示法

```javascript
const MyComponents = {
  Component1: function(props) {
    return <div>{props.id}</div>
  }
};
// use
<MyComponents.Component1 id="1" />
```

React组件中传递字符串常量，格式正确的转义字符串能被编译后输出  
例如`<MyComponent msg="&lt;">`等同于`<MyComponent msg={'<'}>`

React组件中定义了属性，没有给属性传值，它默认为true  
例如`<MyComponent msg>`等同于`<MyComponent msg={true}>`


扩展属性`...`  
React组件中同时传入多个属性与属性值  
假设需要传入组件的属性如下
```
const attr = {
  id: '1',
  value: '100'
};
// 通过扩展属性传入
<MyComponent {...attr}>
// 等同于↓↓
<MyComponent id="1" value="100">
```
--------------------------------------------------

**props对象**

props对象是用来保存JSX属性与属性值的一个对象

例如：<MyComponent id="1" value="100">

在props对象中就是：
this.props = {
  id: '1',
  value: '100'
};

props对象中有个属性`children`保存的是JSX子代

--------------------------------------------------

**JSX子代**

子代是指在React组件开始和结束标签中间的内容  
中间的内容会被作为参数传递给其React组件内部  
在其内部使用`props.children`进行接收

子代传递的方式：
```
方式1 字符串常量
例如：<MyComponent>Hello World</MyComponent>
组件内部使用props.children接收到的内容为Hello World
注意：字符串常量首尾空格会被去除，字符串间的空格会被压缩成一个

方式2 JSX
例如：
<MyComponent>
  <Component1 />
  <Component2 />
</MyComponent>
组件内部使用props.children接收到是一个数组
这个数组保存着JSX，可以通过遍历输出将内容添加进HTML标签中进行渲染

React组件也能通过数组的形式返回多个元素
```
render() {
  return [
    <div key="0">0</div>,
    <div key="1">1</div>,
    <div key="2">2</div>
  ]
}

render() {
  return (
    <div>
      {[<div key="0">0</div>, <div key="1">1</div>, <div key="2">2</div>]}
    </div>
  )
}
```

方式3 JS表达式
例如：<MyComponent>{'Hello World'}</MyComponent>
组件内部使用props.children接收到的内容为Hello World
{}内可以写任意的表达式

```


没有子代可以直接使用闭合标签

```javascript
// JSX
<MyComponent id="1" />
// 等同于↓↓↓
// JS
React.createElement(
  MyComponent,
  {id: '1'},
  null
);
```

false、true、null、undefined都是有效的子代，但它们不会被直接渲染，相对于内容为空  
可以利用boolean值进行条件渲染

--------------------------------------------------

**ref属性**

ref属性用于获取React实例化创建出来的DOM或React元素（以下统称为node）

方式1 函数接收底层的DOM作为参数 （适用于获取自己组件内部的node）
```javascript
class Component extends React.Components {
  constructor() {
    super();
  }
  render() {
    return (
      <div ref={el => this.el = el}></div>
    )
  }
}
// node: this.el
```

方式2 通过 React.createRef 方法 （适用于获取其他组件的node）
```javascript
class Component extends React.Components {
  constructor() {
    super();
    this.myRef = React.createRef();
  }
  render() {
    return (
      <div ref={this.myRef}></div>
    )
  }
}
// node: this.myRef.current
```
注意：不能在JSX或函数式组件上使用 ref 属性，因为它们没有实例

--------------------------------------------------

**子组件向父组件暴露DOM节点**

父组件中创建ref对象，通过一个特殊属性传递给子组件  
子组件在需要暴露的DOM节点中加入ref属性，属性值为父组件传入的特殊属性  

```javascript
// parent
class ParentComponent extends React.Components {
  constructor() {
    super();
    this.childNode = React.createRef();
  }
  render() {
    return (
      <ChildComponent childNode={this.childNode} />
    )
  }
}

// child
class ChildComponent extends React.Components {
  render() {
    return (
      <div ref={this.props.childNode}>Child</div>
    )
  }
}
```
--------------------------------------------------

**父组件向子组件暴露DOM节点**

父组件中创建ref对象，给需要暴露给子组件的DOM节点添加ref属性，属性值为ref对象  
再通过一个特殊属性将这个ref对象传递给子组件

```javascript
// parent
class ParentComponent extends React.Components {
  constructor() {
    super();
    this.parentNode = React.createRef();
  }
  render() {
    return (
      <div>
        <div>Parent</div>
        <ChildComponent parentNode={this.parentNode}/>
      </div>
    )
  }
}

// child
class ChildComponent extends React.Components {
  constructor() {
    super();
    this.log = this.log.bind(this);
  }
  log() {
    console.log(this.props.parentNode.current);
  }
  render() {
    return (
      <div>
        <div>Child</div>
        <button onClick={this.log}>log</button>
      </div>
    )
  }
}
```
--------------------------------------------------















--------------------------------------------------

ps：可以通过组件间的通信方式，利用 `React.createRef` 将node进行传递  
实现父组件中获取子组件的node，相反在子组件中获取父组件的node

平行组件间获取node  
主要思路：将组件的node保存到平行组件的父组件中  
```javascript
// parent
class ParentComponent class React.Components {
  constructor() {
    super();
    this.saveChildNode = this.saveChildNode.bind(this);
  }
  saveChildNode(param) {
    this.childNode = param; // 接收参数保存node
  }
  render() {
    return (
      <childComponent getNode={this.saveChildNode} />
    )
  }
}

// child
class ChildComponent class React.Components {
  constructor() {
    super();
    this.myRef = React.createRef();
  }
  render() {
    this.props.getNode(this.myRef); // 将node作为参数传递
    return (
      <div ref={this.myRef}>child</div>
    )
  }
}
```
平行的组件通过这种方式，将node保存到父组件中，获取node就相当于子组件访问父组件的内容  
在父组件中定义好子组件的处理函数，再将处理函数传递进子组件，子组件在需要的时候触发就可以了

--------------------------------------------------
