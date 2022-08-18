## 1. setState

### setState更新状态的2种写法

### 对象式的setState

```react
state = { count: 0 };
add = () => {
// 1.获取原来的count值
    const { count } = this.state;
 //2. 更新状态
    //   this.setState({ count: count + 1 });
    //   console.log('----',this.setState.count); //输出0 因为React状态更新是异步的，在React还未更新完状态，这里就开始输出值了
    //正解如下
      this.setState({ count: count + 1 }, () => {
          console.log(this.state.count); //输出1 因为这个回调函数是在React更新完毕后才进行调用
      })
 }
```

### 函数式的setState

```react
state = { count: 0 };
add = () => {
// 函数式的setState
    // this.setState((state) => {
    // //   console.log(state);
    //   return { count: state.count + 1 };
    // });
    //简化
    this.setState((state) => ({ count: state.count + 1 }));
}
```

### 总结

1. 对象式的setState是函数式的setState的简写方式(语法糖)

2. 使用原则：

   (1). 如果新状态不依赖于原状态 ===> 使用对象方式
   (2). 如果新状态依赖于原状态 ===> 使用函数方式
   (3). 如果需要在setState()执行后获取最新的状态数据, 要在第二个callback函数中读取

```react
state = { count: 0 };
add = () => {
    // 上述总结也不一定。如下同样也可以实现add()方法
   this.setState({ count: this.state.count + 1 });
}
```

------



## 2. lazyLoad

### 路由组件的lazyLoad

```react
	//1.通过React的lazy函数配合import()函数动态加载路由组件 ===> 路由组件代码会被分开打包
	const Login = lazy(()=>import('@/pages/Login'))
	
	//2.通过<Suspense>指定在加载得到路由打包文件前显示一个自定义loading界面
	<Suspense fallback={<h1>loading.....</h1>}>
        <Switch>
            <Route path="/xxx" component={Xxxx}/>
            <Redirect to="/login"/>
        </Switch>
    </Suspense>
```

```react
import { lazy, Suspense } from 'react';

//当网速慢的时候，页面无法显示的时候，先显示Loading页面。因此不能放在懒加载中
import Loading from './Loading';

//1. 通过React的lazy函数配合import()函数动态加载路由组件 ===> 路由组件代码会被分开打包
const Home = lazy(() => import('./Home'));
const About = lazy(() => import('./About'));

//2.通过<Suspense>指定在加载得到路由打包文件前先显示一个自定义loading界面
<Suspense fallback={<Loading />}>
  <Route path="/about" component={About} />
  <Route path="/home" component={Home} />
</Suspense>
```

## 3. Hooks

#### 1. React Hook/Hooks是什么?

```
(1). Hook是React 16.8.0版本增加的新特性/新语法
(2). 可以让你在函数组件中使用 state 以及其他的 React 特性
```

目前函数式组件已经风靡天下啦！

#### 2. 三个常用的Hook

```
(1). State Hook: React.useState()
(2). Effect Hook: React.useEffect()
(3). Ref Hook: React.useRef()
```

#### 3. State Hook

```
(1). State Hook让函数组件也可以有state状态, 并进行状态数据的读写操作
(2). 语法: const [xxx, setXxx] = React.useState(initValue)  
(3). useState()说明:
        参数: 第一次初始化指定的值在内部作缓存
        返回值: 包含2个元素的数组, 第1个为内部当前状态值, 第2个为更新状态值的函数
(4). setXxx()2种写法:
        setXxx(newValue): 参数为非函数值, 直接指定新的状态值, 内部用其覆盖原来的状态值
        setXxx(value => newValue): 参数为函数, 接收原本的状态值, 返回新的状态值, 内部用其覆盖原来的状态值
```

```react	
// 函数式组件
function Demo() {
  // Demo被调用几次？1+n  n为页面更新的时候，即+1的时候
  console.log('Demo');
  // 数组也可以进行结构赋值
  const [count, setCount] = React.useState(0);
  const [name, setName] = React.useState('tom');
  function add() {
    //第一种写法
    //   setCount(count + 1);
    // 第二种写法
    setCount((count) => count + 1);
  }
  function changeName() {
    setName('jack');
  }
  return (
    <div>
      <h2>当前求和为:{count}</h2>
      <button onClick={add}>点我+1</button>
      <h2>{name}</h2>
      <button onClick={changeName}>点我改名</button>
    </div>
  );
}
export default Demo;
```

#### 4. Effect Hook

```
(1). Effect Hook 可以让你在函数组件中执行副作用操作(用于模拟类组件中的生命周期钩子)
(2). React中的副作用操作:
        发ajax请求数据获取
        设置订阅 / 启动定时器
        手动更改真实DOM
(3). 语法和说明: 
        useEffect(() => { 
          // 在此可以执行任何带副作用操作
          return () => { // 在组件卸载前执行
            // 在此做一些收尾工作, 比如清除定时器/取消订阅等
          }
        }, [stateValue]) // 如果指定的是[], 回调函数只会在第一次render()后执行
    
(4). 可以把 useEffect Hook 看做如下三个函数的组合
        componentDidMount()
        componentDidUpdate()
    	componentWillUnmount() 
```

```react
/**
 * 类式组件
 */
//初始值存储
state = { count: 0 };
//卸载组件函数
  unmount = () => {
    ReactDOM.unmountComponentAtNode(document.getElementById('root'));
  };
//定时器开启（在组件一挂载完毕）
  componentDidMount() {
    this.timer = setInterval(() => {
      this.setState((state) => ({ count: state.count + 1 }));
    }, 1000);
  }
//定时器关闭（在组件将要被卸载）
  componentWillUnmount() {
    clearInterval(this.timer);
  }
//渲染展示
  render() {
    return (
      <div>
        <h2>当前求和为{this.state.count}</h2>
        <button onClick={this.unmount}>卸载组件</button>
      </div>
    );
  }

/**
 * 函数式组件，没有this，没有生命周期
 */
 // 数组也可以进行结构赋值
  const [count, setCount] = React.useState(0);
  const [name, setName] = React.useState('tom');

  // 1.React.useEffect进行相关副作用。相当于两个钩子，看第二个参数是什么？
  // 若第二个参数没有值则代表监测所有，若写一个[]代表谁都不监测,若写[count]代表监测count的变化
    React.useEffect(() => {
      console.log('@'); //进行测试
    }, [count]);

  // 2.React.useEffect中的有返回值的时候，相当于componentWillUnmount
   //定时器的回调(第一个函数开启定时器，第二个函数关闭定时器【有return返回值】)
  React.useEffect(() => {
    let timer = setInterval(() => {
      setCount((count) => count + 1);
    }, 1000);
    return () => {
      //   console.log('#');
      clearInterval(timer);
    };
  }, []);

  // 加的回调
  function add() {
    setCount((count) => count + 1);
  }
  // 改名的回调
  function change() {
    setName('jack');
  }

  // 卸载组件的回调
  function unmount() {
    ReactDOM.unmountComponentAtNode(document.getElementById('root'));
  }

  return (
    <div>
      <h2>当前求和为:{count}</h2>
      <h2>当前名字为:{name}</h2>

      <button onClick={add}>点我+1</button>
      <button onClick={change}>点我改名</button>
      <button onClick={unmount}>卸载组件</button>
    </div>
  );
```

#### 5. Ref Hook

```
(1). Ref Hook可以在函数组件中存储/查找组件内的标签或任意其它数据
(2). 语法: const refContainer = useRef()
(3). 作用:保存标签对象,功能与React.createRef()一样
```

```react
//在类式组件中使用ref 
// 使用最新的ref
 myRef = React.createRef();
//点击按钮触发表单
 show = () => {
     alert(this.myRef.current.value);
  }
 //渲染展示页面
  render() {
    return (
      <div>
        <input type="text" ref={this.myRef} />
        <button onClick={this.show}>点我提示数据</button>
      </div>
    );
  }

//在函数式组件中使用ref（专人专用）
const myRef = React.useRef();
// 提示输入的回调
  function show() {
    alert(myRef.current.value);
  }
//函数返回展示页面
return (
    <div>
      <input type="text" ref={myRef} />
      <button onClick={show}>点我提示数据</button>
    </div>
  );
```

------



## 4. Fragment

`<Fragment>` 标签和`空标签` 都不会在页面显示，只是伪装成一个容器，先代替<div/> 

区别：Fragment可以具有一个key属性，当有遍历的时候，会使用到key，则此时需要Fragment标签 

### 使用

```react
<Fragment key={1}>
        <input type="text" />
        <input type="text" />
</Fragment>

<>
	<input type="text" />
     <input type="text" />
</>
```

### 作用

> 可以不用必须有一个真实的DOM根标签了

<hr/>

## 5. Context

### 理解

> 一种组件间通信方式, 常用于【祖组件】与【后代组件】间通信

### 使用

```js
1) 创建Context容器对象：放在最上方
	const XxxContext = React.createContext()  
	
2) 渲染子组时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：
	<xxxContext.Provider value={数据}>
		子组件
    </xxxContext.Provider>
    
3) 后代组件读取数据：

	//第一种方式:仅适用于类组件 
	  static contextType = xxxContext  // 声明接收context
	  this.context // 读取context中的value数据
	  
	//第二种方式: 函数组件与类组件都可以
	  <xxxContext.Consumer>
	    {
	      value => ( // value就是context中的value数据
	        要显示的内容
	      )
	    }
	  </xxxContext.Consumer>
```

C组件收到其祖组件的内容。

```react
// 创建Context对象
const MyContext = React.createContext();
const { Provider, Consumer } = MyContext;

export default class A extends Component {
  state = { username: 'tom', age: 19 };
  render() {
    const { username, age } = this.state;
    return (
      <div className="parent">
        <h3>我是A组件</h3>
        <h4>我的用户名是：{username}</h4>
        {/* 蠢 */}
        {/* <B username={this.state.username} /> */}
        {/* 机智：这样在B组件以及后代组件中都能收到所传递的用户名 */}
        <Provider value={{ username, age }}>
          <B />
        </Provider>
      </div>
    );
  }
}
class B extends Component {
  render() {
    return (
      <div className="child">
        <h3>我是B组件</h3>
        {/* 蠢的办法 */}
        {/* <C username={this.props.username} /> */}
        {/* 机智的办法 context */}
        <C />
      </div>
    );
  }
}

//类式C组件
class C extends Component {
  // 声明接收Context对象
  static contextType = MyContext;
  render() {
    // console.log(this); //输出C的实例
    console.log(this.context); //输出在A组件中声明的username
    const { username, age } = this.context;
    return (
      <div className="grand">
        <h3>我是C组件</h3>
        {/* 蠢 */}
        {/* <h4>我从A组件接收到的用户名是:{this.props.username}</h4> */}
        {/* 机智 */}
        <h4>
          我从A组件接收到的用户名是:{username},年龄是{age}
        </h4>
      </div>
    );
  }
}


// 函数式C组件
function C() {
  return (
    <div className="grand">
      <h3>我是C组件</h3>
      {/* 蠢 */}
      {/* <h4>我从A组件接收到的用户名是:{this.props.username}</h4> */}
      {/* 机智 */}
      <h4>
        我从A组件接收到的用户名是:
        <Consumer>
          {/* {(value) => {
            //   console.log(value);
            return `${value.username},年龄是${value.age}`;
          }} */}
          {(value) => `${value.username},年龄是${value.age}`}
        </Consumer>
      </h4>
    </div>
  );
}
```

### 注意

```
在应用开发中一般不用context, 一般都用它的封装react插件
```

<hr/>

## 6. 组件优化

### Component的2个问题

> 1. 只要执行setState(),即使不改变状态数据, 组件也会重新render() ==> 效率低
> 2. 只当前组件重新render(), 就会自动重新render子组件，纵使子组件没有用到父组件的任何数据 ==> 效率低

### 效率高的做法

> 只有当组件的state或props数据发生改变时才重新render()

### 原因

> Component中的shouldComponentUpdate()总是返回true

### 解决

```
办法1: 
	重写shouldComponentUpdate()方法
	比较新旧state或props数据, 如果有变化才返回true, 如果没有返回false
```

```react
//父组件
 shouldComponentUpdate(nextProps, nextState) {
    // console.log(this.props, this.state); //目前的props和state
    // console.log(nextProps, nextState); //接下来要变化的目标props，目标state
    // return false;
    //   更新阀门关掉，实际上是有变化，但只是不显示到页面中
    // if (this.state.carName === nextState.carName) return false;
    // else return true;
    //   简化
    return !this.state.carName === nextState.carName;
  }

//子组件
shouldComponentUpdate(nextProps, nextState) {
    console.log(this.props, this.state); //目前的props和state。子组件没有state
    console.log(nextProps, nextState); //接下来要变化的目标props，目标state
    // if (this.props.carName === nextProps.carName) return false;
    // else return true;
    //   简化
    return !this.props.carName === nextProps.carName;
  }
```

```
办法2:  
	使用PureComponent
	PureComponent重写了shouldComponentUpdate(), 只有state或props数据有变化才返回true
	注意: 
		只是进行state和props数据的浅比较, 如果只是数据对象内部数据变了, 返回false  
		不要直接修改state数据, 而是要产生新数据
项目中一般使用PureComponent来优化
```

```react
import React, {PureComponent } from 'react';

export default class Parent extends PureComponent {
 //案例一
changeCar = () => {
    //   产生新数据（正解）
    this.setState({ carName: '特斯拉' });
    // this.setState({});  //代表没有变化

    // 	 对原数据进行修改
    // 小瑕疵，PureComponent底层值对shouldComponentUpdate进行了浅对比
    // const obj = this.state;
    // obj.carName = '特斯拉';
    // console.log(obj === this.state); //true  此时阀门关闭,不会进行更新，触发render渲染输出
    // this.setState(obj);
  };
 render() {
    console.log('Parent --- render');
    const { carName } = this.state;
    return (
      <div className="parent">
        <h3>我是Parent组件</h3>
        {this.state.stus}&nbsp;
        <span>我的车名字是：{carName}</span>
        <button onClick={this.changeCar}>点我换车</button>
      </div>
    );
  }
}

//总结:PureComponent 它只是重写了 shouldComponentUpdate(nextProps, nextState) {}的比较逻辑。且只是进行浅对比

//案例二，
export default class Parent extends PureComponent {
state = { carName: '奔驰c36', stus: ['小张', '小粒', '小王'] };

addStu = () => {
    // 同一个对象，阀门关闭，无法更新
    // const { stus } = this.state;
    // stus.unshift('小流');   //unshift、push只是对原来的数组进行修改。
    // this.setState({ stus });

      //解决：多个状态属性进行修改
    const { stus } = this.state;
    this.setState({ stus: ['小流', ...stus] }); //此时用字面量，返回的是一个新的数组
  };

render() {
    console.log('Parent --- render');
    const { carName } = this.state;
    return (
      <div className="parent">
        {this.state.stus}&nbsp;
        <button onClick={this.addStu}>添加一个小流</button>
      </div>
    );
  }
}
```



<hr/>

## 7. render props

### 如何向组件内部动态传入带内容的结构(标签)?

即如何把第三方组件或第三个组件摆放在指定位置且要传递数据？

```
Vue中: 
	使用slot技术, 也就是通过组件标签体传入结构  <A><B/></A>
React中:
	使用children props: 通过组件标签体传入结构
	使用render props: 通过组件标签属性传入结构,而且可以携带数据，一般用render函数属性
```

### children props

```
<A>
  <B>xxxx</B>
</A>
{this.props.children}
问题: 如果B组件需要A组件内的数据, ==> 做不到 
```

```react
export default class Parent extends Component {
  render() {
    return (
      <div className="parent">
        <h3>我是Parent组件</h3>
        {/* 想要显示出标签体内容,必须在A组件中显示出this.props.children */}
       <A>hello!</A>
        {/* 这里可以隐式说明B组件是A组件的子组件*/}
         <A>
          <B />
        </A> 
      </div>
    );
  }
}
class A extends Component {
  render() {
    console.log(this.props);
    return (
      <div className="a">
        <h3>我是A组件</h3>
       {/* 这里将显示hello！与组件B*/}
        {this.props.children} 
      </div>
    );
  }
}
class B extends Component {
  render() {
    return (
      <div className="b">
        <h3>我是B组件</h3>
      </div>
    );
  }
}
```

### render props

```
<A render={(data) => <C data={data}></C>}></A>
A组件: {this.props.render(内部state数据)}
C组件: 读取A组件传入的数据显示 {this.props.data} 
```

```react
import React, { Component } from 'react';
import './index.css';
import C from '../1_setState';
export default class Parent extends Component {
  render() {
    return (
      <div className="parent">
        <h3>我是Parent组件</h3>
        {/* AB父子组件。若此时A想要给B组件传递东西  */}
        <A render={(name) => <B name={name} />} />
        {/* 这里可以动态的给A组件中加入带有内容的结构！  */}      
  		<A render={(name) => <C name={name} />} />
      </div>
    );
  }
}
class A extends Component {
  state = { name: 'tom' };
  render() {
    console.log(this.props);
    const { name } = this.state;
    return (
      <div className="a">
        <h3>我是A组件</h3>
        {this.props.render(name)}
      </div>
    );
  }
}
class B extends Component {
  render() {
    console.log('B --- render');
    return (
      <div className="b">
        <h3>我是B组件</h3>
        {this.props.name}
      </div>
    );
  }
}
```

<hr/>

## 8. 错误边界

#### 理解：

错误边界(Error boundary)：用来捕获**后代**组件错误，渲染出备用页面

#### 特点：

只能捕获**后代组件生命周期**产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误

##### 使用方式：

getDerivedStateFromError配合componentDidCatch

```js
// 生命周期函数，一旦后台组件报错，就会触发
static getDerivedStateFromError(error) {
    console.log(error);
    // 在render之前触发
    // 返回新的state
    return {
        hasError: true,
    };
}

componentDidCatch(error, info) {
    // 统计页面的错误。发送请求发送到后台去
    console.log(error, info);
}
```

```react
//父组件：进行捕获子组件的错误
export default class Parent extends Component {
  state = {
    hasError: '', //用于标识子组件是否产生错误
  };

  // 当Parent的子组件出现报错的时候，会触发getDerivedStateFromError调用，并携带错误信息
  static getDerivedStateFromError(error) {
    console.log('-----', error);
    return { hasError: error };
  }

  // 该生命周期函数不常用。
  componentDidCatch() {
    console.log(
      '渲染组件出错，此统计错误次数，反馈给服务器，用于通知编码人员进行bug解决'
    );
    //   一般在此统计错误次数，反馈给服务器，用于通知编码人员进行bug解决
  }

  render() {
    return (
      <div>
        <h2>我是Parent组件</h2>
        {this.state.hasError ? <h2>当前网络不稳定，稍后再试</h2> : <Child />}
      </div>
    );
  }
}
//子组件：出现错误
export default class Child extends Component {
  state = {
    // users: [
    //   { id: '001', name: 'tom', age: 18 },
    //   { id: '002', name: 'jack', age: 20 },
    //   { id: '003', name: 'whh', age: 21 },
    // ],
    //   故意错误
    users: 'abc',
  };
  render() {
    return (
      <div>
        <h2>我是Child组件</h2>
        {this.state.users.map((userObj) => {
          return (
            <h4 key={userObj.id}>
              {userObj.name}---{userObj.age}
            </h4>
          );
        })}
      </div>
    );
  }
}
```



## 9. 组件通信方式总结

#### 组件间的关系：

- 父子组件
- 兄弟组件（非嵌套组件）
- 祖孙组件（跨级组件）

#### 几种通信方式：

```
	1.props：
		(1).children props
		(2).render props
	2.消息订阅-发布：
		pubs-sub、event（了解）等等
	3.集中式管理：
		redux（知道）、dva等等
	4.conText:
		生产者-消费者模式
```

#### 比较好的搭配方式：

```
	父子组件：props
	兄弟组件：消息订阅-发布、集中式管理
	祖孙组件(跨级组件)：消息订阅-发布、集中式管理、conText(开发用的少，封装插件用的多)
```

