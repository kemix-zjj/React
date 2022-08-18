# React Router 6 快速上手

## 一、概述

1.  React Router 以三个不同的包发布到 npm 上，它们分别为：

- react-router： 路由的核心库，提供了很多的：组件、钩子。
- **react-router-dom**：包含react-router所有内容，并添加一些专门用于 DOM 的组件，例如 `<BrowserRouter>`等。
- react-router-native：包括 react-router 所有内容，并添加一些专门用于 ReactNative 的API，例如:`<NativeRouter>`等。

2. 与React Router 5.x 版本相比，改变了什么？

- 内置组件的变化：移除`<Switch/>` ，新增 `<Routes/>`等。
- 语法的变化：`component={About}` 变为 `element={<About/>}`等。
- 新增多个hook：`useParams`、`useNavigate`、`useMatch`等。
- 官方明确推荐使用函数式组件了！！！
- ......

- react-router： 路由的核心库，提供了很多的：组件、钩子。
- **react-router-dom**：包含react-router所有内容，并添加一些专门用于 DOM 的组件，例如 `<BrowserRouter>`等。
- react-router-native：包括 react-router 所有内容，并添加一些专门用于 ReactNative 的API，例如:`<NativeRouter>`等。

1. 与React Router 5.x 版本相比，改变了什么？

- 内置组件的变化：移除`<Switch/>` ，新增 `<Routes/>`等。
- 语法的变化：`component={About}` 变为 `element={<About/>}`等。
- 新增多个hook：`useParams`、`useNavigate`、`useMatch`等。
- 官方明确推荐使用函数式组件了！！！
- ......

## 二、Component

### 1. `<BrowserRouter>`

1. 说明：`<BrowserRouter> `用于包裹整个应用。

2. 示例代码：

   ```jsx
   import React from "react";
   import ReactDOM from "react-dom";
   import { BrowserRouter } from "react-router-dom";
   
   ReactDOM.render(
     <BrowserRouter>
       {/* 整体结构（通常为App组件） */}
     </BrowserRouter>,root
   );
   ```

### 2. `<HashRouter>`

1. 说明：作用与`<BrowserRouter>`一样，但`<HashRouter>`修改的是地址栏的hash值。
2. 备注：6.x版本中`<HashRouter>`、`<BrowserRouter> ` 的用法与 5.x 相同。
3. 实际开发中，`<BrowserRouter>` 更常用。

### 3. `<Routes/> 与 <Route/>`

1. v6版本中移出了先前的`<Switch>`，引入了新的替代者：`<Routes>`。

2. `<Routes>` 和 `<Route>`要配合使用，且**必须**要用`<Routes>`包裹`<Route>`。

3. `<Route>` 相当于一个 if 语句，如果其路径与当前 URL 匹配，则呈现其对应的组件。

4.  `<Route caseSensitive>` 属性用于指定：匹配时是否区分大小写（默认为 false）。

5. 当URL发生变化时，`<Routes> `都会查看其所有子` <Route>` 元素以找到最佳匹配（一般是首先出现的）并呈现组件 。

6.  `<Route>` 也可以嵌套使用，且可配合`useRoutes()`配置 “路由表” ，但需要通过 `<Outlet>` 组件来渲染呈现其子路由。

7. 示例代码：

   ```jsx
   <Routes>
       /*path属性用于定义路径，element属性用于定义当前路径所对应的组件*/
       <Route path="/login" element={<Login />}></Route>
   
   		/*用于定义嵌套路由，home是一级路由，对应的路径/home*/
       <Route path="home" element={<Home />}>
          /*test1 和 test2 是二级路由,对应的路径是/home/test1 或 /home/test2*/
           /* 记住不要再添加/ 因为再添加/是代表从根路径下寻找该路径地址*/
         <Route path="test1" element={<Test/>}></Route>
         <Route path="test2" element={<Test2/>}></Route>
   	</Route>
   	
   		//Route也可以不写element属性, 这时就是用于展示嵌套的路由 .所对应的路径是/users/xxx
       <Route path="users">
          <Route path="xxx" element={<Demo />} />
       </Route>
   </Routes>
   ```

### 4. `<Link>`

1. 作用: 修改URL，且不发送网络请求（路由链接）。

2. 注意: 外侧需要用`<BrowserRouter>`或`<HashRouter>`包裹，一般我们选择在最外层即 App.jsx 中进行包裹。

3. 示例代码：

   ```jsx
   import { Link } from "react-router-dom";
   
   function Test() {
     return (
       <div>
       	<Link to="/路径">按钮</Link>
       </div>
     );
   }
   ```

### 5. `<NavLink>`

1. 作用: 与`<Link>`组件类似，且可实现导航的“**高亮**”效果。

2. 示例代码：

   ```jsx
   // 注意: NavLink默认类名是active，下面是指定自定义的class。注意此时className是函数形式
   
   //自定义样式
   <NavLink
       to="login"
       className={({ isActive }) => {
           console.log('home', isActive)
           return isActive ? 'base one' : 'base'
       }}
   >login</NavLink>
   
   /*
   	默认情况下，当Home的子组件匹配成功，Home的导航也会高亮，
   	当NavLink上添加了end属性后，若Home的子组件匹配成功，则Home（父组件）的导航没有高亮效果。
   */
   <NavLink to="home" end >home</NavLink>
   ```

### 6. `<Navigate>`

1. 作用：只要`<Navigate>`组件被渲染，就会修改路径，切换视图。替换了`Redirect`常用于**重定向**。

2. `replace`属性用于控制跳转模式（push 或 replace，默认是push）。

3. 示例代码：

   ```jsx
   import React,{useState} from 'react'
   import {Navigate} from 'react-router-dom'
   
   export default function Home() {
   	const [sum,setSum] = useState(1)
   	return (
   		<div>
   			<h3>我是Home的内容</h3>
   			{/* 根据sum的值决定是否切换视图 */}
   			{sum === 1 ? <h4>sum的值为{sum}</h4> : <Navigate to="/about" replace={true}/>}
   			<button onClick={()=>setSum(2)}>点我将sum变为2</button>
   		</div>
   	)
   }
   ```

### 7. `<Outlet>`

1. 当`<Route>`产生嵌套时，渲染其对应的后续子路由。

2. 示例代码：

   ```jsx
   //根据路由表生成对应的路由规则
   const element = useRoutes([
     {
       path:'/about',
       element:<About/>
     },
     {
       path:'/home',
       element:<Home/>,
       children:[
         {
           path:'news',
           element:<News/>
         },
         {
           path:'message',
           element:<Message/>,
         }
       ]
     }
   ])
   
   //Home.js
   import React from 'react'
   import {NavLink,Outlet} from 'react-router-dom'
   
   export default function Home() {
   	return (
   		<div>
   			<h2>Home组件内容</h2>
   			<div>
   				<ul className="nav nav-tabs">
   					<li>
   						<NavLink className="list-group-item" to="news">
                               News
                           </NavLink>
   					</li>
   					<li>
   						<NavLink className="list-group-item"  to="message">
                               Message
                           </NavLink>
   					</li>
   				</ul>
   				{/* 指定路由组件呈现的位置 */}
   				<Outlet />
   			</div>
   		</div>
   	)
   }
   
   ```

## 三、Hooks

### 1. useRoutes()

1. 作用：根据路由表，动态创建`<Routes>`和`<Route>`。

2. 示例代码：

   ```jsx
   //路由表配置：src/routes/index.js
   import About from '../pages/About'
   import Home from '../pages/Home'
   import {Navigate} from 'react-router-dom'
   
   export default [
   	{
   		path:'/about',
   		element:<About/>
   	},
   	{
   		path:'/home',
   		element:<Home/>
   	},
   	{
   		path:'/',
   		element:<Navigate to="/about"/>
   	}
   ]
   
   //App.jsx
   import React from 'react'
   import {NavLink,useRoutes} from 'react-router-dom'
   import routes from './routes'
   
   export default function App() {
   	//根据路由表生成对应的路由规则
   	const element = useRoutes(routes)
   	return (
   		<div>
   			......
         {/* 注册路由 */}
         {element}
   		  ......
   		</div>
   	)
   }
   
   ```

### 2. useNavigate()

1. 作用：返回一个函数用来实现编程式导航。

2. 携带两个参数（replace/push，state对象）

3. 示例代码：

   ```jsx
   import React from 'react'
   import {useNavigate} from 'react-router-dom'
   
   export default function Demo() {
     const navigate = useNavigate()
     const handle = () => {
       //第一种使用方式：指定具体的路径
       navigate('/login', {
         replace: false,
         state: {a:1, b:2}
       }) 
       //第二种使用方式：传入数值进行前进或后退，类似于5.x中的 history.go()方法
       navigate(-1)
     }
     
     return (
       <div>
         <button onClick={handle}>按钮</button>
       </div>
     )
   }
   ```

### 3. useParams()

1. 作用：返回当前匹配路由的`params`参数，类似于5.x中的`match.params`。

2. 示例代码：

   ```jsx
   import React from 'react';
   import { Routes, Route, useParams } from 'react-router-dom';
   import User from './pages/User.jsx'
   
   function ProfilePage() {
     // 获取URL中携带过来的params参数
     let { id } = useParams();
   }
   
   function App() {
     return (
       <Routes>
         <Route path="users/:id" element={<User />}/>
       </Routes>
     );
   }
   ```

### 4. useSearchParams()

1. 作用：用于读取和修改当前位置的 URL 中的查询字符串。

2. 返回一个包含两个值的数组，内容分别为：当前的search参数、更新search的函数。

3. 用 search.get() 进行获取相关search参数值

4. 示例代码：

   ```jsx
   import React from 'react'
   import {useSearchParams} from 'react-router-dom'
   
   export default function Detail() {
   	const [search,setSearch] = useSearchParams()
   	const id = search.get('id')
   	const title = search.get('title')
   	const content = search.get('content')
   	return (
   		<ul>
   			<li>
   				<button onClick={()=>setSearch('id=008&title=哈哈&content=嘻嘻')}>点我更新一下收到的search参数</button>
   			</li>
   			<li>消息编号：{id}</li>
   			<li>消息标题：{title}</li>
   			<li>消息内容：{content}</li>
   		</ul>
   	)
   }
   
   ```

### 5. useLocation()

1. 作用：获取当前 location 信息，对标5.x中的路由组件的`location`属性。

2. 示例代码：

   ```jsx
   import React from 'react'
   import {useLocation} from 'react-router-dom'
   
   export default function Detail() {
   	const x = useLocation()
   	console.log('@',x)
     // x就是location对象: 
   	/*
   		{
         hash: "",
         key: "ah9nv6sz",
         pathname: "/login",
         search: "?name=zs&age=18",
         state: {a: 1, b: 2}
       }
   	*/
       //连续解构赋值
       const { state: { id, title, context } } = useLocation();
   	return (
   		<ul>
   			<li>消息编号：{id}</li>
   			<li>消息标题：{title}</li>
   			<li>消息内容：{content}</li>
   		</ul>
   	)
   }
   ```

### 6. useMatch()

1. 作用：返回当前匹配信息，对标5.x中的路由组件的`match`属性。

2. 示例代码：

   ```jsx
   <Route path="/login/:page/:pageSize" element={<Login />}/>
   <NavLink to="/login/1/10">登录</NavLink>
   
   export default function Login() {
     const match = useMatch('/login/:x/:y')
     console.log(match) //输出match对象
     //match对象内容如下：
     /*
     	{
         params: {x: '1', y: '10'}
         pathname: "/LoGin/1/10"  
         pathnameBase: "/LoGin/1/10"
         pattern: {
         	path: '/login/:x/:y', 
         	caseSensitive: false, 
         	end: false
         }
       }
     */
     return (
     	<div>
         <h1>Login</h1>
       </div>
     )
   }
   ```

### 7. useInRouterContext()

作用：如果组件在路由器`<Router>` 的上下文中呈现，则 `useInRouterContext` 钩子返回 true，否则返回 false。

```react
//Demo组件
import React from 'react';
import { useInRouterContext } from 'react-router-dom';

export default function Demo() {
  console.log('测试是否存在于上下文中', useInRouterContext()); //返回false！！
  return <div>Demo</div>;
}

// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import App from './App';
import Demo from './components/Demo';

ReactDOM.render(
  <div>
    <Demo />
    <BrowserRouter>
      <App />
    </BrowserRouter>
    ,
  </div>,
  document.getElementById('root'),
);
```

### 8. useNavigationType()

1. 作用：返回当前的导航类型（用户是如何来到当前页面的）。

2. 返回值：`POP`、`PUSH`、`REPLACE`。

3. 备注：`POP`是指在浏览器中直接打开了这个路由组件（刷新页面）。

4. 示例代码：

   ```react
   import React from 'react';
   import { useNavigationType } from 'react-router-dom';
   
   export default function News() {
     console.log('当前路由导航类型', useNavigationType());
     return (
       <ul>
         <li>news001</li>
         <li>news002</li>
         <li>news003</li>
       </ul>
     );
   }
   ```

### 9. useOutlet()

1. 作用：用来呈现当前组件中渲染的嵌套路由。

2. 示例代码：

   ```jsx
   import React from 'react';
   import { NavLink, Outlet, useOutlet } from 'react-router-dom';
   
   //Home组件中是嵌套路由
   export default function Home() {
     console.log('显示出嵌套路由', useOutlet());
   // 如果嵌套路由没有挂载,则result为null
   // 如果嵌套路由已经挂载,则展示嵌套的路由对象
     return (
       <div>
         <h2>Home组件内容</h2>
         <div>
           <ul className="nav nav-tabs">
             <li>
               <NavLink className="list-group-item" to="news">
                 News
               </NavLink>
             </li>
             <li>
               <NavLink className="list-group-item" to="message">
                 Message
               </NavLink>
             </li>
           </ul>
           {/* 指定路由组件呈现的位置 */}
           <Outlet />
         </div>
       </div>
     );
   }
   ```

### 10.useResolvedPath()

1. 作用：给定一个 URL值，解析其中的：path、search、hash值。

2. 示例代码：

   ```react
   import React from 'react';
   import { useNavigationType, useResolvedPath } from 'react-router-dom';
   
   export default function News() {
     console.log('当前路由导航类型', useNavigationType());
     console.log(
       '给定一个URL值，解析其中的path,search,hash值',
       useResolvedPath('/user?id=001&name=tom#qwe'),
     );
     return (
       <ul>
         <li>news001</li>
         <li>news002</li>
         <li>news003</li>
       </ul>
     );
   }
   ```

## 四、使用场景

### 1. 一级路由

```react
//App.js
import { NavLink, Route, Routes } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
export default function App() {
  return (
    //...
     {/* 路由链接 */}
    <NavLink className="list-group-item" to="/about">
      About
    </NavLink>
    <NavLink className="list-group-item" to="/home">
      Home
    </NavLink>
      {/* 注册路由 */}
    {/* Routes相当于之前的Switch。但此时是必须添加的 */}
    <Routes>
      <Route path="/about" element={<About />} />
      <Route path="/home" element={<Home />} />
    </Routes>
  );
}
```

### 2. 重定向

```react
import { Navigate } from 'react-router-dom';
//用法一
{/* 注册路由 */}
{/* Routes相当于之前的Switch。但此时是必须添加的 */}
<Routes>
  <Route path="/about" element={<About />} />
  <Route path="/home" element={<Home />} />
  {/* 重定向 Navigate */}
  <Route path="/" element={<Navigate to="/about" />} />
</Routes>

//用法二
const [sum, setSum] = useState(1);
return (
  <div>
    <h3>我是Home的内容</h3>
    {/* Navigate只要渲染就会引起视图的变化*/}
    {/* Navigate 可以添加push与replace，默认为push */}
    {sum === 2 ? (<Navigate to="/about" replace={false} />) : (<h4>当前sum的值是:{sum}</h4> )}
    <button onClick={() => setSum(2)}>点我将sum变成2</button>
  </div>
);
```

### 3. NavLink高亮显示

```react
import { NavLink, Route, Routes, Navigate } from 'react-router-dom';
export default function App() {
  // NavLink高亮的函数
  function computedClassName({ isActive }) {
    return isActive ? 'list-group-item atguigu' : 'list-group-item';
  }
return (
    //...
    {/* 路由链接 */}
    {/* Router6中要自定义样式的话，必须在className中是以函数形式体现 */}
    <NavLink className={computedClassName} to="/about">
      About
    </NavLink>
    <NavLink className={computedClassName} to="/home">
      Home
    </NavLink>
    
    //...
    {/* 注册路由 */}
    //...
   )
}
```

### 4. useRoutes路由表

```react
<Routes>
    /*path属性用于定义路径，element属性用于定义当前路径所对应的组件*/
    <Route path="/login" element={<Login />}></Route>

		/*用于定义嵌套路由，home是一级路由，对应的路径/home*/
    <Route path="home" element={<Home />}>
       /*test1 和 test2 是二级路由,对应的路径是/home/test1 或 /home/test2*/
        /* 记住不要再添加/ 因为再添加/是代表从根路径下寻找该路径地址*/
      <Route path="test1" element={<Test/>}></Route>
      <Route path="test2" element={<Test2/>}></Route>
	</Route>
	
		//Route也可以不写element属性, 这时就是用于展示嵌套的路由 .所对应的路径是/users/xxx
    <Route path="users">
       <Route path="xxx" element={<Demo />} />
    </Route>
</Routes>
```

但一般在实际开发中，路由表是单独封装到一个文件中 routes 【src/routes/index.js】

```react
import About from '../pages/About';
import Home from '../pages/Home';
import { Navigate } from 'react-router-dom';
export default [
  {
    path: '/about',
    element: <About />,
  },
  {
    path: '/home',
    element: <Home />,
  },
  {
    path: '/',
    element: <Navigate to="/about" />,
  },
];
```

 src/App.jsx ---> 路由表的使用

```react
import { NavLink, useRoutes } from 'react-router-dom';
import routes from './routes';
export default function App() {
  // 路由表element
  // 根据路由表生成对应的路由规则
  const element = useRoutes(routes);
  return (
      //...
      
      {/* 路由链接 */}
      //...
      
      {/* 注册路由 */}
      <div>
     	 {element}
      </div>	
    )
}
```

### 5. 嵌套路由

routes/index.js

```react
import Home from '../pages/Home'
import Message from '../pages/Message'
import News from '../pages/News'
import {Navigate} from 'react-router-dom'
export default[
    {
		path:'/home',
		element:<Home/>,
        //children:子组件路由且子组件的路由路径不用加父级路由，也不用加 /
		children:[
			{
				path:'news',
				element:<News/>
			},
			{
				path:'message',
				element:<Message/>
			}
		]
	},
	{
		path:'/',
		element:<Navigate to="/about"/>
	}
]
```

pages/Home.jsx

```react
import React from 'react'
import {NavLink,Outlet} from 'react-router-dom'

export default function Home() {
	return (
		<div>
			<h2>Home组件内容</h2>
			<div>
				<ul className="nav nav-tabs">
					<li>
						<NavLink className="list-group-item" to="news">News</NavLink>
					</li>
					<li>
						<NavLink className="list-group-item" to="message">Message</NavLink>
					</li>
				</ul>
				{/* 指定路由组件呈现的位置 */}
				<Outlet />
			</div>
		</div>
	)
}
```

### 6. 携带params参数

父组件 Message：给路由链接中携带 params 参数

```react
import React, { useState } from 'react';
import { Link, Outlet } from 'react-router-dom';

export default function Message() {
  const [messages] = useState([
    { id: '001', title: '消息1', context: '晴天' },
    { id: '002', title: '消息2', context: '雨天' },
    { id: '003', title: '消息3', context: '阴天' },
  ]);
  return (
    <div>
      <ul>
        {messages.map((m) => {
          return (
            //   路由链接
            <li key={m.id}>
              {/* 携带params参数 */}
              <Link to={`details/${m.id}/${m.title}/${m.context}`}>
                {m.context}
              </Link>
            </li>
          );
        })}
      </ul>
      <hr />
      {/* 指定路由组件展示的位置 */}
      <Outlet />
    </div>
  );
}
```

子组件 Details：引入 Hooks函数：**useParams**

```react
import React from 'react';
import { useParams } from 'react-router-dom';
export default function Details() {
    //解构赋值
  const { id, title, context } = useParams();
  console.log(id, title, context);
  return (
    <ul>
      <li>id:{id}</li>
      <li>title:{title}</li>
      <li>context:{context}</li>
    </ul>
  );
}
```

路由表 routes : path中携带params参数

```react
 {
    path: '/home',
    element: <Home />,
    children: [
      {
        path: 'message',
        element: <Message />,
        children: [
          {
              //路径中携带params参数
            path: 'details/:id/:title/:context',
            element: <Details />,
          },
        ],
      },
      {
        path: 'news',
        element: <News />,
      },
    ],
  },
```

### 7. 携带search参数

父组件 Message：给路由链接中携带 search 参数

```react
import React, { useState } from 'react';
import { Link, Outlet } from 'react-router-dom';

export default function Message() {
  const [messages] = useState([
    { id: '001', title: '消息1', context: '晴天' },
    { id: '002', title: '消息2', context: '雨天' },
    { id: '003', title: '消息3', context: '阴天' },
  ]);
  return (
    <div>
      <ul>
        {messages.map((m) => {
          return (
            //   路由链接
            <li key={m.id}>
              {/* 携带search参数 */}
              <Link
                to={`details?id=${m.id}&title=${m.title}&context=${m.context}`}
              >
                {m.context}
              </Link>
            </li>
          );
        })}
      </ul>
      <hr />
      {/* 指定路由组件展示的位置 */}
      <Outlet />
    </div>
  );
}
```

子组件 Details：引入 Hooks函数: **useSearchParams**

```react
import React from 'react';
import { useSearchParams } from 'react-router-dom';
export default function Details() {
   //解构赋值
  const [search, setSearch] = useSearchParams();
    //利用search.get()进行获取
  // console.log(search.get('id'));
  const id = search.get('id');
  const title = search.get('title');
  const context = search.get('context');
  console.log();
  return (
    <ul>
      {/* setSearch实际开发中很少用 */}
      <li className={{ backgroudColor: 'red' }}>
        <button onClick={() => setSearch(`id=008&title=哈哈&context=嘻嘻`)}>
          点我更新一下收到的search参数
        </button>
      </li>
      <li>id:{id}</li>
      <li>title:{title}</li>
      <li>context:{context}</li>
    </ul>
  );
}
```

路由表 routes：path中无需携带任何参数

```react
{
    path: '/home',
    element: <Home />,
    children: [
      {
        path: 'message',
        element: <Message />,
        children: [
          {
            path: 'details',
            element: <Details />,
          },
        ],
      },
      {
        path: 'news',
        element: <News />,
      },
    ],
  },
```

### 8. 携带state参数

父组件 Message：给路由链接中携带 state 参数

```react
import React, { useState } from 'react';
import { Link, Outlet } from 'react-router-dom';

export default function Message() {
  const [messages] = useState([
    { id: '001', title: '消息1', context: '晴天' },
    { id: '002', title: '消息2', context: '雨天' },
    { id: '003', title: '消息3', context: '阴天' },
  ]);
  return (
    <div>
      <ul>
        {messages.map((m) => {
          return (
            //   路由链接
            <li key={m.id}>
              {/* 携带search参数 */}
              <Link
                to="details"
                state={{ id: m.id, title: m.title, context: m.context }}
              >
                {m.context}
              </Link>
            </li>
          );
        })}
      </ul>
      <hr />
      {/* 指定路由组件展示的位置 */}
      <Outlet />
    </div>
  );
}
```

子组件 Details：引入 Hooks函数: **useLocation**

```react
import React from 'react';
import { useLocation } from 'react-router-dom';
export default function Details() {
  // 解构赋值的连续写法
  const { state: { id, title, context }} = useLocation();
  return (
    <ul>
      <li>id:{id}</li>
      <li>title:{title}</li>
      <li>context:{context}</li>
    </ul>
  );
}
```

路由表 routes：path中无需携带任何参数

```react
{
    path: '/home',
    element: <Home />,
    children: [
      {
        path: 'message',
        element: <Message />,
        children: [
          {
            path: 'details',
            element: <Details />,
          },
        ],
      },
      {
        path: 'news',
        element: <News />,
      },
    ],
  },
```

### 9. 编程式路由导航

useNavigate 使用场景一：在父组件中，点击**按钮**跳往子组件详情页。

```react
import React, { useState } from 'react';
import { Link, Outlet, useNavigate } from 'react-router-dom';

export default function Message() {
  const navigate = useNavigate();
  const [messages] = useState([
    { id: '001', title: '消息1', context: '晴天' },
    { id: '002', title: '消息2', context: '雨天' },
    { id: '003', title: '消息3', context: '阴天' },
  ]);
  function showDetails(m) {
    console.log(m);
    // NavLink与Link是渲染到页面以a标签的形式渲染。
    //必须要点击a标签;Navigate是要渲染到页面才会触发，在函数中不会进行触发。
     //所以此时需要用到useNavigate
    navigate('details', {
      replace: false,
      state: {
        id: m.id,
        title: m.title,
        context: m.context,
      },
    });
  }
  return (
    <div>
      <ul>
        {messages.map((m) => {
          return (
            //   路由链接
            <li key={m.id}>
              {/* state */}
              <Link
                to="details"
                state={{ id: m.id, title: m.title, context: m.context }}
              >
                {m.title}
              </Link>
              <button onClick={() => showDetails(m)}>查看详情</button>
            </li>
          );
        })}
      </ul>
      <hr />
      {/* 指定路由组件展示的位置 */}
      <Outlet />
    </div>
  );
}
```

useNavigate 使用场景二：在组件中设置按钮前进与后退

```react
import React from 'react';
import { useNavigate } from 'react-router-dom';

export default function Header() {
  const navigate = useNavigate();

  function back() {
    navigate(-1);
  }
  function forward() {
    navigate(1);
  }
  return (
    <div>
      <div className="row">
        <div className="col-xs-offset-2 col-xs-8">
          <div className="page-header">
            <h2>React Router Demo</h2>
            <button onClick={back}>后退</button>
            <button onClick={forward}>前进</button>
          </div>
        </div>
      </div>
    </div>
  );
} 
```
