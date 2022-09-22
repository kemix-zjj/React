# React 面向编程

## 组件的定义

组件首字母必须大写，函数必须有返回值（ return），ReactDOM.render（）第一个参数必须以组件形式书写。

函数名即组件名。类名即组件名。

只有类式组件才有资格谈实例。

### 函数式组件

函数式组件 --- 适用于简单组件的定义

注意：函数可以有参数，也可以没有，但是必须要有返回值，返回一个虚拟DOM。

```js
<script type="text/babel">
	//1.创建函数式组件
	function MyComponent(){
		console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
		return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
	}
	//2.渲染组件到页面
	ReactDOM.render(<MyComponent/>,document.getElementById('test'))
	/* 
	 执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
	 1.React解析组件标签，找到了MyComponent组件。
	 2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。
	*/
</script>
```

### 类式组件

类式组件 --- 适用于复杂组件的定义

先理解一下类：

1. 类中的构造器不是必须要写的，要对实例进行一些初始化的操作，如添加指定属性时才写。
2. 如果A类继承了B类，且A类中写了构造器，那么A类构造器中的super是必须要调用的。
3. 类中所定义的方法，都放在了类的原型对象上，供实例去使用。

类式组件：

- 必须要有继承 extends React.Component
- 必须要有render() ，该方法一定有返回值，且返回一个虚拟 DOM【React 组件使用一个名为 render() 的方法，接收输入的数据并返回需要展示的内容。被传入的数据可在组件中通过 this.props 在 render() 访问。】

```js
<script type="text/babel">
	//1.创建类式组件
	class MyComponent extends React.Component {
		render(){
			//render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
			//render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
			console.log('render中的this:',this);
			return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>
		}
	}
	//2.渲染组件到页面
	ReactDOM.render(<MyComponent/>,document.getElementById('test'))
	/* 
	执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
		1.React解析组件标签，找到了MyComponent组件。
		2.发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
		3.将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。
	*/
</script>
```

## 组件实例的三大属性1：state

**状态（state）驱动页面的更新！**

1. 如何区别简单组件与复杂组件？

看是否具有状态 state。有则为复杂组件，无则简单组件。

1. state 是什么？

state是组件对象最重要的属性，值是对象（可以包含多个key-value的组合） 

1. 函数加括号与不加括号的区别：

```js
const {isHot,wind} = this.state；
//正确调用。将函数demo先赋值给onClick。等到点击后再进行调用demo()函数
return <h1 onClick={demo}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
//......
//错误调用。此时是将函数demo的返回值给onClick，但此时的函数demo的返回值是undefined
return <h1 onClick={demo()}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>

function demo(){
	alert('按钮3被点击了')
}
```

1. 小练习：页面显示【今天天气很炎热】，鼠标点击文字的时候，页面更改为【今天天气很凉爽】 

- render调用几次？

```js
<body>
	<!-- 准备好一个“容器” -->
	<div id="test"></div>

	<!-- 引入react核心库 -->
	<script type="text/javascript" src="../js/react.development.js"></script>
	<!-- 引入react-dom，用于支持react操作DOM -->
	<script type="text/javascript" src="../js/react-dom.development.js"></script>
	<!-- 引入babel，用于将jsx转为js -->
	<script type="text/javascript" src="../js/babel.min.js"></script>

	<script type="text/babel">
		//1.创建组件
		class Weather extends React.Component {

		//构造器调用几次？ ———— 1次 【new一次构造器就调用一次。这边是new是指在渲染组件到页面的时候，React自身帮我们进行了new操作】
			constructor(props) {
				console.log('constructor');
				super(props)
				//初始化状态
				this.state = { isHot: false, wind: '微风' }
				//解决changeWeather中this指向问题
				this.changeWeather = this.changeWeather.bind(this)
			}

			//render调用几次？ ———— 1+n次 1是初始化的那次 n是状态更新的次数
			render() {
				console.log('render');
				//读取状态
				const { isHot, wind } = this.state
				return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
			}

			//changeWeather调用几次？ ———— 点几次调几次
			changeWeather() {
				//changeWeather放在哪里？ ———— Weather的原型对象上，供实例使用
				//由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
				//类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined

				console.log('changeWeather');
				//获取原来的isHot值
				const isHot = this.state.isHot
				//严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
				this.setState({ isHot: !isHot })
				console.log(this);

				//严重注意：状态(state)不可直接更改，下面这行就是直接更改！！！
				//this.state.isHot = !isHot //这是错误的写法
			}
		}
		//2.渲染组件到页面
		ReactDOM.render(<Weather />, document.getElementById('test'))

	</script>
</body>
```

- state的使用：

```js
<script type="text/babel">
	//1.创建组件
	class Weather extends React.Component{
		//初始化状态
		state = {isHot:false,wind:'微风'}
		render(){
			const {isHot,wind} = this.state
			return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
		}
		//自定义方法————要用赋值语句的形式+箭头函数!!
		changeWeather = ()=>{
			const isHot = this.state.isHot
			this.setState({isHot:!isHot})
		}
	}
	//2.渲染组件到页面
	ReactDOM.render(<Weather/>,document.getElementById('test'))		
</script>
```

**tips**：

- 在类中，如果直接使用赋值语句的话，会将state和changeWeather直接绑定到类Weather自身身上的。
- 箭头函数没有自己的this。因此会向上进行寻找
- 自定义方法，要用赋值语句+箭头函数书写。

**总结**：

1. state 是组件实例对象最重要的属性，必须是对象的形式

2. 组件被称为状态机，通过更改 state 的值来达到更新页面显示（重新渲染组件）

3. 组件 render 中的 this 指的是组件实例对象

4. 状态数据不能直接赋值，需要用 setState（）

5. 组件自定义方法中的this为undefined，怎么解决？

   ① 将自定义函数改为表达式+箭头函数的形式（推荐）
   ② 在构造器中用bind（）强制绑定this

## 组件实例的三大属性2：props

Props主要用来传递数据，比如组件之间进行传值。

### 基本使用

```js
<body>
    <div id = "div">
        
    </div>

</body>
<script type="text/babel">
    class Person extends React.Component{
        render(){
            return (
                <ul>
                    //接受数据并显示
                    <li>{this.props.name}</li>
                    <li>{this.props.age}</li>
                    <li>{this.props.sex}</li>
                </ul>
            )
        }
    }
    //传递数据
    ReactDOM.render(<Person name="tom" age = "41" sex="男"/>,document.getElementById("div"));
</script>
```

如果传递的数据是一个对象，可以更加简便的使用 

```js
<script type="text/babel">
    class Person extends React.Component{
        render(){
            return (
                <ul>
                    <li>{this.props.name}</li>
                    <li>{this.props.age}</li>
                    <li>{this.props.sex}</li>
                </ul>
            )
        }
    }
    const p = {name:"张三",age:"18",sex:"女"}
//扩展运算符（...）
   ReactDOM.render(<Person {...p}/>,document.getElementById("div"));
</script>
```

[扩展运算符的作用：](https://juejin.cn/post/7129801371091468325)

- 展开数组
- 复制一个对象给另一个对象{...对象名}。此时这两个对象并没有什么联系了 
- 在复制的时候，合并其中的属性 

**注意！！** **{...P}并不能展开一个对象**

**props传递一个对象，是因为babel+react使得{..p}可以展开对象，但是只有在标签中才能使用**

### 对于 props 限制

- propTypes：类型检查，还可以限制不能为空
- defaultProps：默认值

```js
<script type="text/babel">
	//创建组件
	class Person extends React.Component{
		constructor(props){
			//构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props
			// console.log(props);
			super(props)
			console.log('constructor',this.props);
		}
        //使用static，将propTypes绑定到类Person上
		//对标签属性进行类型、必要性的限制
		static propTypes = {
			name:PropTypes.string.isRequired, //限制name必传，且为字符串
			sex:PropTypes.string,//限制sex为字符串
			age:PropTypes.number,//限制age为数值
		}
		//指定默认标签属性值
		static defaultProps = {
			sex:'男',//sex默认值为男
			age:18 //age默认值为18
		}
		
		render(){
			// console.log(this);
			const {name,age,sex} = this.props
			//props是只读的
			//this.props.name = 'jack' //此行代码会报错，因为props是只读的
			return (
				<ul>
					<li>姓名：{name}</li>
					<li>性别：{sex}</li>
					<li>年龄：{age+1}</li>
				</ul>
			)
		}
	}
	//渲染组件到页面
	ReactDOM.render(<Person name="jerry"/>,document.getElementById('test1'))
</script>
```

### 函数组件的使用

函数在使用props的时候，是作为参数进行使用的(props)； 

```js
<script type="text/babel">	
	//创建组件
	function Person (props){
		const {name,age,sex} = props
		return (
				<ul>
					<li>姓名：{name}</li>
					<li>性别：{sex}</li>
					<li>年龄：{age}</li>
				</ul>
			)
	}
	Person.propTypes = {
		name:PropTypes.string.isRequired, //限制name必传，且为字符串
		sex:PropTypes.string,//限制sex为字符串
		age:PropTypes.number,//限制age为数值
	}
	//指定默认标签属性值
	Person.defaultProps = {
		sex:'男',//sex默认值为男
		age:18 //age默认值为18
	}
	//渲染组件到页面
	ReactDOM.render(<Person name="jerry"/>,document.getElementById('test1'))
</script>
```

## 组件实例的三大属性3：refs

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。 

Refs主要提供了三种方式： 其中字符串形式在实际开发中最常用，但存在可忽略的的效率问题。

**官方提示我们不要过度的使用ref，如果发生时间的元素刚好是需要操作的元素，就可以使用事件去替代。** 

### **字符串形式**

在想要获取到一个DOM节点，可以直接在这个节点上添加ref属性。利用该属性进行获取该节点的值。 

```js
<script type="text/babel">
	//创建组件
	class Demo extends React.Component{
		//展示左侧输入框的数据
		showData = ()=>{
			const {input1} = this.refs
			alert(input1.value)
		}
		//展示右侧输入框的数据
		showData2 = ()=>{
			const {input2} = this.refs
			alert(input2.value)
		}
		render(){
			return(
				<div>
					<input ref="input1" type="text" placeholder="点击按钮提示数据"/>&nbsp;
					<button onClick={this.showData}>点我提示左侧的数据</button>&nbsp;
					<input ref="input2" onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>
				</div>
			)
		}
	}
	//渲染组件到页面
	ReactDOM.render(<Demo a="1" b="2"/>,document.getElementById('test'))
</script>
```

### **回调形式**

回调形式会在ref属性中添加一个回调函数。将该DOM作为参数传递过去。 

`ref={c => this.input1 = c} ` c 为 ref 所在位置的 html 标签。然后将 c 赋值给该实例上的 input1 属性。this.input1即代表给该实例原型上绑定了一个 input1属性。此时就代表原型上的 input1属性具体 ref 标识的标签。此时可以通过 input1.value 获得标签上的内容。

```js
<script type="text/babel">
	//创建组件
	class Demo extends React.Component{
		//展示左侧输入框的数据
		showData = ()=>{
			const {input1} = this
			alert(input1.value)
		}
		//展示右侧输入框的数据
		showData2 = ()=>{
			const {input2} = this
			alert(input2.value)
		}
		render(){
			return(
				<div>
					<input ref={c => this.input1 = c } type="text" placeholder="点击按钮提示数据"/>&nbsp;
					<button onClick={this.showData}>点我提示左侧的数据</button>&nbsp;
					<input onBlur={this.showData2} ref={c => this.input2 = c } type="text" placeholder="失去焦点提示数据"/>&nbsp;
				</div>
			)
		}
	}
	//渲染组件到页面
	ReactDOM.render(<Demo a="1" b="2"/>,document.getElementById('test'))
</script>
```

### **API形式**

React其实已经给我们提供了一个相应的API（React.createRef()），他会自动的将该DOM元素放入实例对象中 

```js
<script type="text/babel">
	//创建组件
	class Demo extends React.Component{
		/* 
		React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点,该容器是“专人专用”的
		 */
		myRef = React.createRef()
		myRef2 = React.createRef()
		//展示左侧输入框的数据
		showData = ()=>{
			alert(this.myRef.current.value);
		}
		//展示右侧输入框的数据
		showData2 = ()=>{
			alert(this.myRef2.current.value);
		}
		render(){
			return(
				<div>
					<input ref={this.myRef} type="text" placeholder="点击按钮提示数据"/>&nbsp;
					<button onClick={this.showData}>点我提示左侧的数据</button>&nbsp;
					<input onBlur={this.showData2} ref={this.myRef2} type="text" placeholder="失去焦点提示数据"/>&nbsp;
				</div>
			)
		}
	}
	//渲染组件到页面
	ReactDOM.render(<Demo a="1" b="2"/>,document.getElementById('test'))
</script>
```



## react 中的事件处理

- React的事件是通过onXxx属性指定事件处理函数
- React使用的都是自定义的时间，而不是原生的事件
- React中的事件是通过事件委托方式处理的（事件冒泡）
- 事件中必须返回的是函数
- 通过event.target得到发生事件的Dom元素对象

```js
<script type="text/babel">
	//创建组件
	class Demo extends React.Component {
	/* 
	(1).通过onXxx属性指定事件处理函数(注意大小写)
			a.React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 —————— 为了更好的兼容性
			b.React中的事件是通过事件委托方式处理的(委托给组件最外层的元素) ————————为了的高效
	(2).通过event.target得到发生事件的DOM元素对象（发生事件的元素正好是要操作的元———不要过度使用ref*/
		//创建ref容器
		myRef = React.createRef()
		myRef2 = React.createRef()
		//展示左侧输入框的数据
		showData = (event) => {
			console.log(event.target);
			alert(this.myRef.current.value);
		}
		//展示右侧输入框的数据
		showData2 = (event) => {
			alert(event.target.value);
		}
		render() {
			return (
				<div>
					<input ref={this.myRef} type="text" placeholder="点击按钮提示数据" />&nbsp;
					<button onClick={this.showData}>点我提示左侧的数据</button>&nbsp;
					// 当点击的事件对象与触发的的事件源是同一个的时候。
					<input onBlur={this.showData2} type="text" placeholder="失去焦点提示数据" />&nbsp;
				</div>
			)
		}
	}
	//渲染组件到页面
	ReactDOM.render(<Demo a="1" b="2" />, document.getElementById('test'))
</script>
```



## react 中收集表单数据

### 受控组件

使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。 巧记：必须用到 state

```js
<script type="text/babel">
	//创建组件
	class Login extends React.Component{
		//初始化状态
		state = {
			username:'', //用户名
			password:'' //密码
		}
		//保存用户名到状态中
		saveUsername = (event)=>{
			this.setState({username:event.target.value})
		}
		//保存密码到状态中
		savePassword = (event)=>{
			this.setState({password:event.target.value})
		}
		//表单提交的回调
		handleSubmit = (event)=>{
			event.preventDefault() //阻止表单提交
			const {username,password} = this.state
			alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
		}
		render(){
			return(
				<form onSubmit={this.handleSubmit}>
					用户名：<input onChange={this.saveUsername} type="text" name="username"/>
					密码：<input onChange={this.savePassword} type="password" name="password"/>
					<button>登录</button>
				</form>
			)
		}
	}
	//渲染组件
	ReactDOM.render(<Login/>,document.getElementById('test'))
</script>
```

### 非受控组件

非受控组件其实就是表单元素的值不会更新state。输入数据都是**现用现取**的。 巧记：没有用到 state

```js
<script type="text/babel">
	//创建组件
	class Login extends React.Component{
		handleSubmit = (event)=>{
			event.preventDefault() //阻止表单提交
			const {username,password} = this
			alert(`你输入的用户名是：${username.value},你输入的密码是：${password.value}`)
		}
		render(){
			return(
				<form onSubmit={this.handleSubmit}>
					用户名：<input ref={c => this.username = c} type="text" name="username"/>
					密码：<input ref={c => this.password = c} type="password" name="password"/>
					<button>登录</button>
				</form>
			)
		}
	}
	//渲染组件
	ReactDOM.render(<Login/>,document.getElementById('test'))
</script>
```

## 高阶函数—函数柯里化

**高级函数**

1. 如果函数的参数是函数
2. 如果函数返回一个函数

**函数的珂里化**

通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式

特点/优点：

- `参数复用`:需要输入多个参数，最终只需输入一个，其余通过 arguments 来获取
- `提前返回`:避免重复去判断某一条件是否符合，不符合则 return  不再继续执行下面的操作
- `延迟执行`:避免重复的去执行程序，等真正需要结果的时候再执行

### 高阶函数—函数柯里化

```js
<script type="text/babel">
	//#region  当无法折叠的时候，可以使用#region与#endregion
	/* 
		高阶函数：如果一个函数符合下面2个规范中的任何一个，那该函数就是高阶函数。
						1.若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数。
						2.若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数。如下面的savaFormData
						常见的高阶函数有：Promise、setTimeout、arr.map()等等
		函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。 
			function sum(a){
				return(b)=>{
					return (c)=>{
						return a+b+c
					}
				}
			}
		*/
	//#endregion
	//创建组件
	class Login extends React.Component {
		//初始化状态
		state = {
			username: '', //用户名
			password: '' //密码
		}
		//保存表单数据到状态中
		// 这边的 savaFormData就是高阶函数
		saveFormData = (dataType) => {
			return (event) => {
				this.setState({ [dataType]: event.target.value })
			}
		}
		//表单提交的回调
		handleSubmit = (event) => {
			event.preventDefault() //阻止表单提交
			const { username, password } = this.state
			alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
		}
		render() {
			return (
				<form onSubmit={this.handleSubmit}>
					// 必须保证onChange赋值的是一个函数，即函数为onChange的回调即可。至于加不加括号无所谓。
					用户名：<input onChange={this.saveFormData('username')} type="text" name="username" />
					密码：<input onChange={this.saveFormData('password')} type="password" name="password" />
					<button>登录</button>
				</form>
			)
		}
	}
	//渲染组件
	ReactDOM.render(<Login />, document.getElementById('test'))
</script>
```

### 不用函数柯里化实现

```js
<script type="text/babel">
	//创建组件
	class Login extends React.Component{
		//初始化状态
		state = {
			username:'', //用户名
			password:'' //密码
		}
		//保存表单数据到状态中
		//这里的savaFormData不用函数柯里化实现
		saveFormData = (dataType,event)=>{
			this.setState({[dataType]:event.target.value})
		}
		//表单提交的回调
		handleSubmit = (event)=>{
			event.preventDefault() //阻止表单提交
			const {username,password} = this.state
			alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
		}
		render(){
			return(
				<form onSubmit={this.handleSubmit}>
					用户名：<input onChange={event => this.saveFormData('username',event) } type="text" name="username"/>
					密码：<input onChange={event => this.saveFormData('password',event) } type="password" name="password"/>
					<button>登录</button>
				</form>
			)
		}
	}
	//渲染组件
	ReactDOM.render(<Login/>,document.getElementById('test'))
</script>
```

## 组件的生命周期

### 引出生命周期

```js
<script type="text/babel">
	//创建组件
	//生命周期回调函数 <=> 生命周期钩子函数 <=> 生命周期函数 <=> 生命周期钩子
	class Life extends React.Component {
		state = { opacity: 1 }
		death = () => {
			//卸载组件
			ReactDOM.unmountComponentAtNode(document.getElementById('test'))
		}
		//组件挂完毕
		componentDidMount() {
			console.log('componentDidMount');
			this.timer = setInterval(() => {
				//获取原状态
				let { opacity } = this.state
				//减小0.1
				opacity -= 0.1
				if (opacity <= 0) opacity = 1
				//设置新的透明度
				this.setState({ opacity })
			}, 200);
		}
		//组件将要卸载
		componentWillUnmount() {
			//清除定时器
			clearInterval(this.timer)
		}
		//初始化渲染、状态更新之后
		render() {
			console.log('render');
			return (
				<div>
					<h2 style={{ opacity: this.state.opacity }}>React学不会怎么办？</h2>
					<button onClick={this.death}>不活了</button>
				</div>
			)
		}
	}
	//渲染组件
	ReactDOM.render(<Life />, document.getElementById('test'))
</script>
```

### react 生命周期（旧）

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0297d1211714587aa02692c599e99ea~tplv-k3u1fbpfcp-watermark.image?)

四条路线：

- 挂载时（1条）：

  constructor --> componentWillMount --> render --> componentDidMount --> componentWillUnmount

- 更新时（3条）：

  - **setState() 触发**：最常用

    shouldComponentUpdate --> componentWillUpdate --> render --> componentDidUpdate --> componentWillUnmount

  - **forceUpdate() 触发**：最少用

    componentWillUpdate --> render --> componentDidUpdate --> componentWillUnmount

  - **父组件 render 触发**：次常用

    componentWillReceiveProps --> shouldComponentUpdate --> componentWillUpdate --> render --> componentDidUpdate --> componentWillUnmount



**最常用的三个钩子**，生命周期回调函数：

- componentDidMount：组件挂载完毕
- componentWillUnmount：组件将要卸载 
- render：组件渲染到页面

```js
<script type="text/babel">
	/* 
		1. 初始化阶段: 由ReactDOM.render()触发---初次渲染
					1.	constructor()
					2.	componentWillMount()
					3.	render()
					4.	componentDidMount() =====> 常用
						一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
		2. 更新阶段: 由组件内部this.setSate()或父组件render触发
					1.	shouldComponentUpdate()
					2.	componentWillUpdate()
					3.	render() =====> 必须使用的一个
					4.	componentDidUpdate()
		3. 卸载组件: 由ReactDOM.unmountComponentAtNode()触发
					1.	componentWillUnmount()  =====> 常用
								一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
	*/
	//创建组件
	class Count extends React.Component {
		//构造器
		constructor(props) {
			console.log('Count---constructor');
			super(props)
			//初始化状态
			this.state = { count: 0 }
		}
		//加1按钮的回调
		add = () => {
			//获取原状态
			const { count } = this.state
			//更新状态
			this.setState({ count: count + 1 })
		}
		//卸载组件按钮的回调
		death = () => {
			ReactDOM.unmountComponentAtNode(document.getElementById('test'))
		}
		//强制更新按钮的回调
		force = () => {
			this.forceUpdate()
		}
		//组件将要挂载的钩子
		componentWillMount() {
			console.log('Count---componentWillMount');
		}
		//组件挂载完毕的钩子
		componentDidMount() {
			console.log('Count---componentDidMount');
		}
		//组件将要卸载的钩子
		componentWillUnmount() {
			console.log('Count---componentWillUnmount');
		}
		//控制组件更新的“阀门”
		shouldComponentUpdate() {
			console.log('Count---shouldComponentUpdate');
			return true
		}
		//组件将要更新的钩子
		componentWillUpdate() {
			console.log('Count---componentWillUpdate');
		}
		//组件更新完毕的钩子
		componentDidUpdate() {
			console.log('Count---componentDidUpdate');
		}
		render() {
			console.log('Count---render');
			const { count } = this.state
			return (
				<div>
					<h2>当前求和为：{count}</h2>
					<button onClick={this.add}>点我+1</button>
					<button onClick={this.death}>卸载组件</button>
					<button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
				</div>
			)
		}
	}
	//父组件A
	class A extends React.Component {
		//初始化状态
		state = { carName: '奔驰' }
		changeCar = () => {
			this.setState({ carName: '奥拓' })
		}
		render() {
			return (
				<div>
					<div>我是A组件</div>
					<button onClick={this.changeCar}>换车</button>
					<B carName={this.state.carName} />
				</div>
			)
		}
	}
	//子组件B
	class B extends React.Component {
		//组件将要接收新的props的钩子
		componentWillReceiveProps(props) {
			console.log('B---componentWillReceiveProps', props);
		}
		//控制组件更新的“阀门”
		shouldComponentUpdate() {
			console.log('B---shouldComponentUpdate');
			return true
		}
		//组件将要更新的钩子
		componentWillUpdate() {
			console.log('B---componentWillUpdate');
		}
		//组件更新完毕的钩子
		componentDidUpdate() {
			console.log('B---componentDidUpdate');
		}
		render() {
			console.log('B---render');
			return (
				<div>我是B组件，接收到的车是:{this.props.carName}</div>
			)
		}
	}
	//渲染组件
	// ReactDOM.render(<Count />, document.getElementById('test'))
	ReactDOM.render(<A />, document.getElementById('test'))
</script>
```

### react 生命周期（新）

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f4a8ea49caff4294a2862ce38868d8a9~tplv-k3u1fbpfcp-watermark.image?)

在新版本中，下面的三个生命周期是过时的。必须在此三个钩子前面加个前缀：UNSAFE_

- componentWillMount ==> UNSAFE_componentWillMount

- componentWillUpdate ==> UNSAFE_componentWillUpdate

- componentWillReceiveProps ==> UNSAFE_componentWillReceiveProps

  巧记：在新版本中，只要有Will的钩子函数，都要加个前缀。除了将要卸载 componentWillUnmount。

  且在实际开发中，这三个生命周期无用且很少用的。

**新旧生命周期的对比**：

新版中废弃了三个带有will的生命周期函数（componentWillMount 、componentWillUpdate 、componentWillReceiveProps ），添加了两个getXxx函数（getDerivedStateFromProps、getSnapshotBeforeUpdate），**但其实实际上新添加的两个函数很少用的。**

```js
//删除了将要过时的三个生命函数
//添加了极其不常用的两个生命函数
<script type="text/babel">
	//创建组件
	class Count extends React.Component {
		/* 
			1. 初始化阶段: 由ReactDOM.render()触发---初次渲染
					1.	constructor()
					2.	getDerivedStateFromProps 
					3.	render()
					4.	componentDidMount() =====> 常用
							一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅
			2. 更新阶段: 由组件内部this.setSate()或父组件重新render触发
					1.	getDerivedStateFromProps
					2.	shouldComponentUpdate()
					3.	render()	=====> 必须使用！
					4.	getSnapshotBeforeUpdate
					5.	componentDidUpdate()
			3. 卸载组件: 由ReactDOM.unmountComponentAtNode()触发
					1.	componentWillUnmount()  =====> 常用
							一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
		*/
		//构造器
		constructor(props) {
		//加1按钮的回调
		add = () => {
		//卸载组件按钮的回调
		death = () => {
		//强制更新按钮的回调
		force = () => {
            
		//用途：若state的值在任何时候都取决于props，那么可以使用getDerivedStateFromProps
		static getDerivedStateFromProps(props, state) {  //得到一个派生是props
			console.log('getDerivedStateFromProps', props, state);
			return null
			// return {count:109} //初始化以109为准，且其他的更新操作都无法启用
			// 了解即可，实际开发中极少用
		}
		//在更新之前获取快照
		getSnapshotBeforeUpdate() {
			console.log('getSnapshotBeforeUpdate');
			return 'atguigu'  //传递给它下流的钩子：componentDidUpdate
		
		//组件挂载完毕的钩子
		componentDidMount() {
		//组件将要卸载的钩子
		componentWillUnmount() {
		//控制组件更新的“阀门”
		shouldComponentUpdate() {
		//组件更新完毕的钩子
		componentDidUpdate(preProps, preState, snapshotValue) {
		render() {
	}
	//渲染组件
	ReactDOM.render(<Count count={199} />, document.getElementById('test'))
</script>
```

### 总结

- **重要的勾子**

1. render：初始化渲染或更新渲染调用
2. componentDidMount：开启监听, 发送ajax请求
3. componentWillUnmount：做一些收尾工作, 如: 清理定时器

- **即将废弃的勾子**

1. componentWillMount
2. componentWillReceiveProps
3. componentWillUpdate

## DOM 的 diffing 算

```js
<script type="text/babel">
	/*
   经典面试题:
      1). react/vue中的key有什么作用？（key的内部原理是什么？）
      2). 为什么遍历列表时，key最好不要用index?
      
		1. 虚拟DOM中key的作用：
			1). 简单的说: key是虚拟DOM对象的标识, 在更新显示时key起着极其重要的作用。

			2). 详细的说: 当状态中的数据发生变化时，react会根据【新数据】生成【新的虚拟DOM】, 
				随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：
					a. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
						(1).若虚拟DOM中内容没变, 直接使用之前的真实DOM
						(2).若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM

					b. 旧虚拟DOM中未找到与新虚拟DOM相同的key
						根据数据创建新的真实DOM，随后渲染到到页面
									
			2. 用index作为key可能会引发的问题：
					1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作:
							会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。

					2. 如果结构中还包含输入类的DOM：
							会产生错误DOM更新 ==> 界面有问题。
												
					3. 注意！如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，
							仅用于渲染列表用于展示，使用index作为key是没有问题的。
					
			3. 开发中如何选择key?:
					1.最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
					2.如果确定只是简单的展示数据，用index也是可以的。
				即有id则用id，没有id的话，也不涉及增删改查的时候，可以使用index。！！
   */
	
	/* 
		慢动作回放----使用index索引值作为key

			初始数据：
					{id:1,name:'小张',age:18},
					{id:2,name:'小李',age:19},
			初始的虚拟DOM：
					<li key=0>小张---18<input type="text"/></li>
					<li key=1>小李---19<input type="text"/></li>

			更新后的数据：
					{id:3,name:'小王',age:20},
					{id:1,name:'小张',age:18},
					{id:2,name:'小李',age:19},
			更新数据后的虚拟DOM：
					<li key=0>小王---20<input type="text"/></li>
					<li key=1>小张---18<input type="text"/></li>
					<li key=2>小李---19<input type="text"/></li>

	-----------------------------------------------------------------

	慢动作回放----使用id唯一标识作为key

			初始数据：
					{id:1,name:'小张',age:18},
					{id:2,name:'小李',age:19},
			初始的虚拟DOM：
					<li key=1>小张---18<input type="text"/></li>
					<li key=2>小李---19<input type="text"/></li>

			更新后的数据：
					{id:3,name:'小王',age:20},
					{id:1,name:'小张',age:18},
					{id:2,name:'小李',age:19},
			更新数据后的虚拟DOM：
					<li key=3>小王---20<input type="text"/></li>
					<li key=1>小张---18<input type="text"/></li>
					<li key=2>小李---19<input type="text"/></li>


	 */
	class Person extends React.Component{

		state = {
			persons:[
				{id:1,name:'小张',age:18},
				{id:2,name:'小李',age:19},
			]
		}

		add = ()=>{
			const {persons} = this.state
			const p = {id:persons.length+1,name:'小王',age:20}
			this.setState({persons:[p,...persons]})
		}

		render(){
			return (
				<div>
					<h2>展示人员信息</h2>
					<button onClick={this.add}>添加一个小王</button>
					<h3>使用index（索引值）作为key</h3>
					<ul>
						{
							this.state.persons.map((personObj,index)=>{
								return <li key={index}>{personObj.name}---{personObj.age}<input type="text"/></li>
							})
						}
					</ul>
					<hr/>
					<hr/>
					<h3>使用id（数据的唯一标识）作为key</h3>
					<ul>
						{
							this.state.persons.map((personObj)=>{
								return <li key={personObj.id}>{personObj.name}---{personObj.age}<input type="text"/></li>
							})
						}
					</ul>
				</div>
			)
		}
	}

	ReactDOM.render(<Person/>,document.getElementById('test'))
</script>
```



