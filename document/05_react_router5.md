# React 路由

单页面，多组件。

## SPA 的理解

1. 单页Web应用（single page web application，SPA）。
2. 整个应用只有**一个完整的页面**。
3. 点击页面中的链接**不会刷新**页面，只会做页面的**局部更新。**
4. 数据都需要通过ajax请求获取, 并在前端异步展现。

## **路由的理解**

### **什么是路由**

- 一个路由就是一个映射关系 (key:value)
- key 为路径, value 可能是 function 或 component

### **路由分类**

1. 后端路由：

1) 理解： value 是 function, 用来处理客户端提交的请求。

2) 注册路由： router.get(path, function(req, res))

3) 工作过程：当 node 接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据

1. 前端路由：

1) 浏览器端路由，value 是 component，用于展示页面内容。

2) 注册路由: <Route path="/test" component={Test}>

3) 工作过程：当浏览器的 path 变为 /test 时, 当前路由组件就会变为 Test 组件

## 前端路由的原理

- 点击导航链接，引起路径变化
- 路径的变化被路由器检测到，进行匹配组件，从而展示

本质上是靠 BOM 中的 history 进行实现的。

```html
<body>
<a href="http://www.atguigu.com" onclick="return push('/test1') ">push test1</a><br><br>
<button onClick="push('/test2')">push test2</button><br><br>
<button onClick="replace('/test3')">replace test3</button><br><br>
<button onClick="back()">&lt;= 回退</button>
<button onClick="forword()">前进 =&gt;</button>
<!-- 本质上对BOM上的history进行操作，但是路由通过别的方式间接对BOM上的history进行操作 -->
<!-- 这边的方式是通过引入第三方库，进行对history的间接操作 -->
<script type="text/javascript" src="https://cdn.bootcss.com/history/4.7.2/history.js"></script>
<script type="text/javascript">
	//方法一，直接使用H5推出的history身上的API【可能存在兼容性问题】
	// let history = History.createBrowserHistory() 
	//方法二，hash值（类似锚点:进行局部跳转，不会引起页面刷新。且会保存历史记录）【兼容性最好，但是路径丑。存在#】
	let history = History.createHashHistory()
	// 推进
	function push(path) {
		history.push(path)
		return false
	}
	// 替换
	function replace(path) {
		history.replace(path)
	}
	// 回退
	function back() {
		history.goBack()
	}
	// 前进
	function forword() {
		history.goForward()
	}
	// 监听
	history.listen((location) => {
		console.log('请求路由路径变化了', location)
	})
</body>
```

 **插件库 react-router-dom 的理解**

1. react的一个插件库。
2. 专门用来实现一个SPA应用。
3. 基于react的项目基本都会用到此库。

## react-router-dom 相关 API

### 内置组件

1. <BrowserRouter>
2. <HashRouter>
3. <Route>
4. <Redirect>
5. <Link>
6. <NavLink>
7. <Switch>

### 其它

1. history对象
2. match对象
3. withRouter函数

## 路由的基本使用

路由的基本使用

1. 明确好界面中的导航区、展示区
2. 导航区的a标签改为Link标签

​           <Link to="/xxxxx">Demo</Link>

1. 展示区写Route标签进行路径的匹配

​          <Route path='/xxxx' component={Demo}/>

1. <App>的最外侧包裹了一个路由器：<BrowserRouter>或<HashRouter>

   （路由器Router两种：BrowserRouter和HashRouter（有加#，类似于锚点））



## 路由组件与一般组件

1. **写法**不同：

- 一般组件：<Demo/>
- 路由组件：<Route path="/demo" component={Demo}/>

1. **存放位置**不同：

- 一般组件：components
- 路由组件：pages

1. **接收到的props**不同：

- 一般组件：写组件标签时传递了什么，就能收到什么
- 路由组件：接收到三个固定的属性
  - history:
    - go: ƒ go(n)
    - goBack: ƒ goBack()
    - goForward: ƒ goForward() 
    - push: ƒ push(path, state)
    - replace: ƒ replace(path, state)
  - location:
    - pathname: "/about"  
    - search: ""  
    - state: undefined
  - match: 
    - params: {} 
    - path: "/about"  
    - url: "/about"

## NavLink 与封装 NavLink

在React中靠路由链接Link或者NavList实现切换组件

需要点击高亮效果的选中NavLink，不然就选择Link

### NavLink 的使用

```js
import {NavLink} from 'react-router-dom'
//...
{/* 原生html中，靠<a>跳转不同的页面 */}
{/* <a className="list-group-item" href="./about.html">About</a>
<a className="list-group-item active" href="./home.html">Home</a> */}
{/* 在React中靠路由链接实现切换组件--编写路由链接 */}
//NavLink 可以通过activeClassName指定样式名来实现路由链接的高亮
<NavLink activeClassName="atguigu" className="list-group-item" to="/about">About</NavLink>
<NavLink activeClassName="atguigu" className="list-group-item" to="/home">Home</NavLink>
```

### 封装 NavList

App.jsx

```js
import Home from './pages/Home'; //Home是路由组件
import About from './pages/About'; //About是路由组件
import MyNavList from './components/MyNavList'; //封装了Home和About组件的自定义一般组件

{/* 在React中靠路由链接实现切换组件---编写路由链接 */}
{/* <NavLink  activeClassName="atguigu" className="list-group-item" to="/about">
  About
</NavLink>
<NavLink activeClassName="atguigu" className="list-group-item" to="/home">
  Home
</NavLink> */}

{/* 封装NavList进行统一管理 */}
{/* <MyNavList to="/about" title="About" />
<MyNavList to="/home" title="Home" /> */}

{/* 优化 */}
// About是标签体内容，a和b和to是标签属性
<MyNavList to="/about" a={1} b={2}> About </MyNavList>
<MyNavList to="/home"> Home </MyNavList>
```

MyNavList.jsx

```js
import React, { Component } from 'react';
import { NavLink } from 'react-router-dom';

export default class MyNavList extends Component {
  render() {
    //   当传递几个参数的时候  
    //   const { to, title } = this.props;
    console.log(this.props); //这里的props可以拿到标签属性也可以拿到标签体内容【props中的children】。
    //   当传递很多参数的时候
    const { title } = this.props;
    return (
       //   当传递几个参数的时候  
      //   <NavLink activeClassName="atguigu" className="list-group-item" to={to}>
      // 当传递很多参数的时候
      //   <NavLink activeClassName="atguigu" className="list-group-item"  {...this.props} >
      //     {title}
      //   </NavLink>
      // 当传递很多参数的时候 -- 优化
      <NavLink
        activeClassName="atguigu"
        className="list-group-item"
        {...this.props}
      />
    );
  }
}
```

## Switch 的使用

1. 通常情况下，path和component是一一对应的关系。

2. Switch可以提高路由匹配效率(单一匹配)。

   ```js
   {/* 注册路由 */}
   <Switch>
     <Route path="/about" component={About} />
     {/* 这时候，/home是看谁的？如果没有用Switch则都展示，用Switch则就展示第一个出现的，这样效率高！ */}
     <Route path="/home" component={Home} />
     <Route path="/home/a/b" component={Test} />
   </Switch>
   ```

## 解决多级路径刷新页面样式丢失的问题

localhost:3000 是react脚手架下的服务器地址。其默认访问的是public下的文件，如果url不匹配的话，则页面展示的是 index.html 页面

解决：

1. public/index.html 中 引入样式时不写 ./ 【写 /】 （最常用）
2. public/index.html 中 引入样式时不写 ./ 【写 %PUBLIC_URL% 】（常用：只适用于react脚手架中）
3. 使用 HashRouter（少用：url地址会多一个#）

## 路由的严格匹配与模糊匹配

1. 默认使用的是模糊匹配（简单记：【输入的路径-即在路由组件中的地址】必须**包含**要【匹配的路径-即在注册路由中的地址】，且顺序要一致）

   ```js
   // 输入地址
   <MyNavList to="/home/a/b">Home</MyNavList>
   //匹配路径
    <Route path="/home" component={Home} />
   ```

2. 开启严格匹配：exact={true} 或者 exact 

   注意：严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

   ```js
    <Route exact={true} path="/home" component={Home} />
   ```

## Redirect 的使用

1. 一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由

2. 具体编码：

   ```js
    <Switch> 
        <Route path="/about" component={About}/>
    	 <Route path="/home" component={Home}/>
     	{/* 重定向，兜底的人 */}
    	 <Redirect to="/about"/>
    </Switch>
   ```

## 嵌套路由(二级路由及上)

1. 注册子路由时要写上父路由的path值
2. 路由的匹配是按照注册路由的顺序进行的

示例：App组件下有Home组件和About组件；Home组件下面有两子组件：News组件与Message组件

Home.jsx

```js
import React, { Component } from 'react';
import { Route, Switch, Redirect } from 'react-router-dom';
import MyNavLink from '../../components/MyNavList'; 
import Message from './Message';  
import News from './News';

export default class Home extends Component {
  render() {
    return (
      <div>
        <ul className="nav nav-tabs">
          <li>
        //注意要写上/home，这样才能进入Home路由中进行再次匹配
            <MyNavLink to="/home/news">News</MyNavLink>
          </li>
          <li>
            <MyNavLink to="/home/message">Message</MyNavLink>
          </li>
        </ul>
        {/* 注册路由 */}
        <Switch>
        //注册子组件时要写上父路由的path值
          <Route path="/home/news" component={News} />
          <Route path="/home/message" component={Message} />
          <Redirect to="/home/news" />
        </Switch>
      </div>
    );
  }
}
```

## 向路由组件传递参数

ajax携带参数三种情况：query、params、body(urlencoded、json)

三种传递参数都很常用！非要对比哪个最常用：则params->search->state

#### params 参数

​       路由链接(携带参数)：<Link to='/demo/test/tom/18'}>详情</Link>

​       注册路由(声明接收)：<Route path="/demo/test/:name/:age" component={Test}/>

​              接收参数：this.props.match.params

```react
//在Message组件中
{/* 1.向路由组件传递params参数 */}
 <Link to={`/home/message/detail/${msgObj.id}/${msgObj.title}`}>
    {msgObj.title}
 </Link>
{/* 2.声明接收params参数 */}
 <Route path="/home/message/detail/:id/:title" component={Detail} />

//在Detail组件中
const Detaildata = [
  { id: '01', content: '你好，中国' },
  { id: '02', content: '你好，尚硅谷' },
  { id: '03', content: '你好，未来的自己' },
];
export default class Detail extends Component {
  render() {
    // console.log(this.props); //接收父组件传递的东西  牛逼!!!
    // 3.接收params参数
    const { id, title } = this.props.match.params;
    const findResult = Detaildata.find((detailObj) => {
      return detailObj.id === id;
    });
    return (
      <ul>
        <li>ID:{id}</li>
        <li>TITLE:{title}</li>
        <li>CONTENT:{findResult.content}</li>
      </ul>
    );
  }
}
```

#### search参数

路由链接(携带参数)：<Link to='/demo/test?name=tom&age=18'}>详情</Link>

​       注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>

​       接收参数：this.props.location.search

​       备注：获取到的search是urlencoded编码字符串，需要借助querystring解析

补充：

​	search接收参数简单，声明参数麻烦

​	search携带参数的时候是用urlencoded编码格式：key=value&key=value

​	利用react脚手架自带的：`import qs from 'qs' ` 进行转化

- 对象 ---> urlencoded：qs.strinfy(obj)
- urlencodes ---> 对象：qs.parse(str)

```react
//在Message组件中
{/* 1.向路由组件传递search参数 */}
<Link to={`/home/message/detail/?id=${msgObj.id}&title=${msgObj.title}`} >
  {msgObj.title}
</Link>
{/* 2.search参数无需声明接收,正常注册路由即可 */}
<Route path="/home/message/detail" component={Detail} />

//在Detail组件
//引入qs进行urlencoded格式与对象格式进行转化
import qs from 'qs';
const Detaildata = [
  { id: '01', content: '你好，中国' },
  { id: '02', content: '你好，尚硅谷' },
  { id: '03', content: '你好，未来的自己' },
];
export default class Detail extends Component {
  render() {
//3.接收search参数!!(有点复杂)
const { search } = this.props.location;
// slice(1)截取第一位至末尾
const { id, title } = qs.parse(search.slice(1));
 const findResult = Detaildata.find((detailObj) => {
      return detailObj.id === id;
 });
return (
      <ul>
        <li>ID:{id}</li>
        <li>TITLE:{title}</li>
        <li>CONTENT:{findResult.content}</li>
      </ul>
    );
  }
}
```

#### state参数

   路由链接(携带参数)：<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>

   注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>

   接收参数：this.props.location.state

   备注：**刷新也可以保留住参数** 在相关地方赋予{}

补充：

对比params与search传递参数的优势在于：携带的参数不会在地址栏中出现

```react
//在Message组件中
{/* 向路由组件传递state参数 */}
<Link to={{ pathname: '/home/message/detail', state: { id: msgObj.id, title: msgObj.title } }}>
  {msgObj.title}
</Link>
{/* state参数无需声明接收，正常注册路由即可 */}
<Route path="/home/message/detail" component={Detail} />

//在Detail组件中
const Detaildata = [
  { id: '01', content: '你好，中国' },
  { id: '02', content: '你好，尚硅谷' },
  { id: '03', content: '你好，未来的自己' },
];
export default class Detail extends Component {
  render() {
    console.log(this.props); //接收父组件传递的东西  牛逼!!!
    // 接收state参数
    // 在强转刷新的时候，刚刚开始的state是undefined的，只有在传递的时候才有值。
    // 因此我们需要在传递之前，即state为undefined的时候，就进行相关优化：
    // 1.在解构赋值的时候，若state为undefined，则赋予{}。
    // 2. 在使用findResult的时候，因为id为undefined，致使findResult也为undefined，则此时也要给findResult赋予{}
    const { id, title } = this.props.location.state || {};
    const findResult =
      Detaildata.find((detailObj) => {
        return detailObj.id === id;
      }) || {};
    return (
      <ul>
        <li>ID:{id}</li>
        <li>TITLE:{title}</li>
        <li>CONTENT:{findResult.content}</li>
      </ul>
    );
  }
}
```

## push与replace模式

区别：有无后退行为。默认是push，是有后退行为的。

```react
{/* 向路由组件传递state参数 */}
  <Link
     // 默认开启的是push，推送，会有压栈即后退行为
     // 替换 replace={true} 或 replace。没有压栈行为
       replace={true}
       to={{
            pathname: '/home/message/detail',
             state: { id: msgObj.id, title: msgObj.title },
          }}> 
    {msgObj.title} 
 </Link>
```

## 编程式路由导航

 借助this.prosp.history对象上的API对操作路由跳转、前进、后退

- this.prosp.history.push()
- this.prosp.history.replace()
- this.prosp.history.goBack()
- this.prosp.history.goForward()
- this.prosp.history.go()

```js
import React, { Component } from 'react';
import { Link, Route } from 'react-router-dom';
import Detail from './Detail';

export default class Message extends Component {
  state = {
    messageArr: [
      { id: '01', title: '消息1' },
      { id: '02', title: '消息2' },
      { id: '03', title: '消息3' },
    ],
  };
  replaceShow = (id, title) => {
    // replace跳转+携带params参数
    // this.props.history.replace(`/home/message/detail/${id}/${title}`);
    // replace跳转+携带search参数
    // this.props.history.replace(`/home/message/detail?id=${id}&title=${title}`);
    // replace跳转+携带state参数
    this.props.history.replace(`/home/message/detail`, { id, title });
  };
  pushShow = (id, title) => {
    // push跳转+携带params参数
    // this.props.history.push(`/home/message/detail/${id}/${title}`);
    // push跳转+携带search参数
    // this.props.history.push(`/home/message/detail?id=${id}&title=${title}`);
    // push跳转+携带state参数
    this.props.history.push(`/home/message/detail`, { id, title });
  };
    // 回退
    back = () => {
      this.props.history.goBack();
    };
    // 前进
    forward = () => {
      this.props.history.goForward();
    };
    // 前进与后退集合
    go = () => {
      // 正则前进，负则后退。数值代表几步
      this.props.history.go(2);
    };
  render() {
    const { messageArr } = this.state;
    return (
      <div>
        <ul>
          {messageArr.map((msgObj) => {
            return (
              <li key={msgObj.id}>
                {/* 模板字符串是js表达式,js表达式要用{}包裹 */}
                {/* 1.向路由组件传递params参数*/}
                {/* <Link to={`/home/message/detail/${msgObj.id}/${msgObj.title}`}> {msgObj.title} </Link> */}
                {/* 知识点：jsx中的回调函数，若想要进行传参的时候，就必须使用高阶函数或者箭头函数 */}
                {/* button这里的onClick是要写成函数形式。因此需要用()=>{}进行包裹，若只是()则给onClick赋值的是一个函数的返回值！！！！！！！！！！！ */}
                {/* <button onClick={() => this.replaceShow(msgObj.id, msgObj.title)} >replace查看</button> */}
                {/* 向路由组件传递search参数 */}
                {/* <Link to={`/home/message/detail/?id=${msgObj.id}&title=${msgObj.title}`}>{msgObj.title}</Link> */}
                {/* 向路由组件传递state参数 */}
                <Link
                  // 默认开启的是push，推送，会有压栈行为
                  // 替换 replace={true} 或 replace。没有压栈行为
                  replace={true}
                  to={{
                    pathname: '/home/message/detail',
                    state: { id: msgObj.id, title: msgObj.title },
                  }}
                >
                  {msgObj.title}
                </Link>
                &nbsp;<button onClick={() => this.pushShow(msgObj.id, msgObj.title)}>push查看</button>
                &nbsp;<button onClick={() => this.replaceShow(msgObj.id, msgObj.title)}>replace查看</button>
              </li>
            );
          })}
        </ul>
        <hr />
        {/* 注册路由 */}
        {/* 2.声明接收params参数 */}
        {/* <Route path="/home/message/detail/:id/:title" component={Detail} /> */}

        {/* search参数无需声明接收,正常注册路由即可 */}
        {/* <Route path="/home/message/detail" component={Detail} /> */}

        {/* state参数无需声明接收，正常注册路由即可 */}
        <Route path="/home/message/detail" component={Detail} />

		//history中的前进与回退以及go
		<button onClick={this.back}>回退</button>&nbsp;
		<button onClick={this.forward}>前进</button>&nbsp;
		<button onClick={this.go}>Go</button>
      </div>
    );
  }
}
```

## withRouter

withRouter可以加工一般组件，给一般组件上加上路由组件才具备的history、location、match三个属性

withRouter返回值是一个新组件

Header组件：

```react
import React, { Component } from 'react';
import { withRouter } from 'react-router-dom';

// withRouter首字母是小写的，因此其是一个函数，不能一个组件。其为了实现在一般组件中实现前进与后退功能
class Header extends Component {
  // back = () => {
  //   // 错误。因为只有路由组件才有history，location，match 但是Header是一般组件
  //   this.props.history.goBack();
  // };
  // 回退
  back = () => {
    this.props.history.goBack();
  };
  // 前进
  forward = () => {
    this.props.history.goForward();
  };
  // 前进与后退集合
  go = () => {
    // 正则前进，负则后退。数值代表几步
    this.props.history.go(2);
  };
  render() {
    console.log('Header一般组件收到的props是', this.props);
    return (
      <div className="page-header">
        <h2>React Router Demo</h2>
        <button onClick={this.back}>回退</button>&nbsp;
        <button onClick={this.forward}>前进</button>&nbsp;
        <button onClick={this.go}>Go</button>
      </div>
    );
  }
}
// withRouter的功能是给一般组件上加上路由组件才具备的history、location、match三个属性。
export default withRouter(Header);
```

## BrowserRouter与HashRouter的区别

**BrowerRouter用得相对比较多一些。**

1. 底层原理不一样：

- BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。
- HashRouter使用的是URL的哈希值。

1. path表现形式不一样

- BrowserRouter的路径中没有#,例如：localhost:3000/demo/test
- HashRouter的路径包含#,例如：localhost:3000/#/demo/test

1. 刷新后对路由state参数的影响

- BrowserRouter没有任何影响，因为state保存在history对象中。
- HashRouter刷新后会导致路由state参数的丢失！！！

1. 备注：HashRouter可以用于解决一些路径错误相关的问题。