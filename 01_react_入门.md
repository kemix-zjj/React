# React 入门

## React 简介

### **react是什么？**

React用于构建用户界面的JS库。是一个将数据渲染为HTML视图的开源JS库。

### **为什么学？**

1. 原生JS操作DOM繁琐，效率低
2. 使用JS直接操作DOM,浏览器会进行大量的重绘重排
3. 原生JS没有组件化编码方案，代码复用低

## React 的基本使用

- 创建一个容器

  ```js
 <body>
    <div id="container">
        //...
    </div>
 </body>
  ```

- 创建虚拟 DOM，渲染到容器中

  ```js 
  const VDOM = <h1>Hello,React</h1> /* 此处一定不要写引号，因为不是字符串 */
  ```
  
- 渲染虚拟 DOM 到页面
  
  ```js
  ReactDOM.render(VDOM,document.getElementById('test'))
  ```

  ReactDOM.render函数可以直接对包含JSX表达式的变量进行渲染。该函数的第一个参数为要渲染的对象，第二个参数为对象插入的节点位置。 

## 虚拟 DOM

关于虚拟DOM：

1. React提供了一些API来创建一种 “特别” 的一般 js 对象，即虚拟 DOM（本质是 Object ）
2. 虚拟DOM比较“轻”，真实DOM比较“重”，因为虚拟DOM是React内部在用，无需真实DOM上那么多的属性。
3. 我们编码时基本只需要操作react的虚拟DOM相关数据, react会转换为真实DOM变化而更新，即虚拟DOM对象最终都会被React转换为真实的DOM，呈现在页面上。

两种创建虚拟 DOM 的方式：

1. 使用 JSX 创建虚拟 DOM（更推荐）

   ```js
  const VDOM = (
    <h1 id = {MyId.toLocaleUpperCase()>
      <span className = "sss" style = {{fontSize:'50px'}}>sss</span>
    </h1>
    )
   ```
  
2. 使用 JS 创建 虚拟 DOM

   ```js
   // 1.创建虚拟DOM[在这使用了js的语法]React.createElement(标签,标签属性,内容)
   const VDOM = React.createElement('h1',{id:"title"},"nihao")
   ```

## React JSX

### jsx 语法规则：

1. 定义虚拟 DOM 时，不要写引号。
2. 标签中混入 **JS 表达式**时要用{}。
3. 样式的类名指定不要用 class，要用 className。
4. 内联样式，要用双括号：style={{key:value}} 的形式去写。
5. 只有一个根标签
6. 标签必须闭合
7. 标签首字母
   - 若小写字母开头，则将该标签转为 html 中同名元素，若 html 中无该标签对应的同名元素，则报错。
   - 若大写字母开头，react 就去渲染对应的组件，若组件没有定义，则报错。

### 区分：js语句(代码) 与 js表达式

1. js 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方

   下面这些都是表达式：(左侧可以用 const= xxx)		(1). a		(2). a+b		(3). demo(1)		(4). arr.map() 		(5). function test () {}

2. js 语句(代码)：不会返回一个值

   下面这些都是语句(代码)：		 (1).if(){}		 (2).for(){}		 (3).switch(){case:xxxx}

### 小练习

动态渲染出列表数据

```js
<script type="text/babel" >

        //模拟一些数据
        const data = ['Angular','React','Vue']

        //1.创建虚拟DOM

        const VDOM = (
            <div>
                <h1>前端js框架列表</h1>
                <ul>
                   {
                        data.map((item,index)=>{
                            return <li key={index}>{item}</li>
                        })
                    }
                </ul>
            </div>

        )

        //2.渲染虚拟DOM到页面
        ReactDOM.render(VDOM,document.getElementById('test'))
    </script>
  ```

![img](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/269069843470446f8f19cc6388e2b537~tplv-k3u1fbpfcp-watermark.image?)

## 模块与组件、模块化与组件化

模块即 js 文件。模块之上为组件。

### 模块

1. 理解：向外提供特定功能的 js 程序, 一般就是一个 js 文件
2. 为什么要拆成模块：随着业务逻辑增加，代码越来越多且复杂
3. 作用：复用 js , 简化 js 的编写, 提高 js 运行效率

### 组件

1. 理解：用来实现局部功能效果的代码和资源的集合 ( html / css / js / image 等等)
2. 为什么要用组件： 一个界面的功能更复杂
3. 作用：复用编码, 简化项目编码, 提高运行效率

### 模块化

当应用的 js 都以模块来编写的, 这个应用就是一个模块化的应用

### 组件化

当应用是以多组件的方式实现, 这个应用就是一个组件化的应用
