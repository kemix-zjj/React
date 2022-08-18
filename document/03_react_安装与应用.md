# react 应用（基于 react 脚手架）

## 使用 create-react-app 创建 react 应用

### 脚手架

1. xxx脚手架: 用来帮助程序员快速创建一个基于xxx库的模板项目
   1. 包含了所有需要的配置（语法检查、jsx编译、devServer…）
   2. 下载好了所有相关的依赖
   3. 可以直接运行一个简单效果
2. react提供了一个用于创建react项目的脚手架库: create-react-app
3. 项目的整体技术架构为:  react + webpack + es6 + eslint
4. 使用脚手架开发的项目的特点: 模块化, 组件化, 工程化

### 创建项目并启动

**第一步**，全局安装：npm i -g create-react-app

**第二步**，切换到想创项目的目录，使用命令：create-react-app hello-react

**第三步**，进入项目文件夹：cd hello-react

**第四步**，启动项目：npm start

### react 脚手架项目结构

public ---- 静态资源文件夹

​		favicon.icon ------ 网站页签图标

​		**index.html** **--------** **主页面**

​		logo192.png ------- logo图

​		logo512.png ------- logo图

​		manifest.json ----- 应用**加壳**的配置文件

​		robots.txt -------- **爬虫协议**文件

src ---- 源码文件夹

​		App.css -------- App组件的样式

​		**App.js** **---------** **App组件**

​		App.test.js ---- 用于给App做测试

​		index.css ------ 样式

​		**index.js**  ------ **入口文件**

​		logo.svg ------- logo图

​		reportWebVitals.js

​			--- 页面性能分析文件(需要web-vitals库的支持)

​		setupTests.js

​			---- 组件单元测试的文件(需要jest-dom库的支持)

### 功能界面的组件化编码流程（通用）

1. 拆分组件: 拆分界面,抽取组件拆分组件: 拆分界面,抽取组件
2. 实现静态组件: 使用组件实现静态页面效果w
3. 实现动态组件
   - 动态显示初始化数据 
     - 数据类型
     - 数据名称
     - 保存在哪个组件
   - 交互(从绑定事件监听开始)

## 组件的组合使用 -- TodoList

**tips:**

1. 拆分组件、实现静态组件，注意：className、style的写法

2. 动态初始化列表，如何确定将数据放在哪个组件的state中？

   ——某个组件使用：放在其自身的state中
   		——某些组件使用：放在他们共同的父组件state中（官方称此操作为：状态提升）

3. 关于父子之间通信：

   1. 【父组件】给【子组件】传递数据：通过props传递
   2. 【子组件】给【父组件】传递数据：通过props传递，要求父提前给子传递一个函数
   3. 【孙组件】给【父组件】传递数据：同理【子组件】传【父组件】，【父组件

4. 注意 defaultChecked 和 checked的区别，类似的还有：defaultValue 和 value

   defaultChecked 只在第一次起效，checked 要配合 onChange 使用

5. **状态在哪里，操作状态的方法就在哪里**



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b9324f855e314a4284ca7b2c1f81d1e8~tplv-k3u1fbpfcp-watermark.image?)

- 实现通信

  将数据状态存储到组件 APP 中，然后通过父向子传递信息，即将 组件App 的 state 传递给 组件 List。

  同时将组件 App 中的一个 addTodos 函数传递给子组件 Header。然后通过子组件触发 addTodos 函数将添加的一个新的 todoObj 传递给父组件 App。

[TodoList 案例源码]()