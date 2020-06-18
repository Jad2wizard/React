# React  
     用于构建用户界面的JavaScript库
     声明式编程、组件化开发、多平台适配、
## 三个库
##### 1、react.js：React的核心库，包含react所必须的核心代码
##### 2、react-dom.js：提供操作DOM的react扩展库，react渲染在不同平台所需要的核心代码
##### 3、babel.min.js：将jsx转换成React代码的工具
#### 添加依赖
##### 1、直接CDN引入：
     react依赖：https://unpkg.com/react@16/umd/react.development.js
     react-dom依赖：https://unpkg.com/react-dom@16/umd/react-dom.development.js
     babel依赖：https://unpkg.com/babel-standalone@6/babel.min.js
##### 2、下载，添加本地依赖
##### 3、通过npm管理
## React生命周期
#### 广义上：挂载、渲染、卸载
![react lifeCycle](https://github.com/cqujlj/React/blob/master/img/react.jpeg)
#### 1、初始化阶段 initialization
##### (1) constructor()
     完成react数据的初始化，可接受两个参数：props和context；
     在函数内部使用这两个参数时需要使用super()传入
       constructor(props) {
                super(props);   //必须使用super，否则会导致this指向错误
            }
### 2、挂载阶段 Mounting
##### (1) componentWillMount()
       一般在服务端渲染时使用。代表的过程时：组件已由constructor()初始化数据后，但是DOM还没有渲染
##### (2) componentDidMount()
      组件第一次渲染完成，DOM节点已经生成；在这里可以调用ajax请求，返回数据setState后组件会重新渲染
### 3、更新阶段 Update
##### (1) componentWillReceiveProps (nextProps)   //新版本是： getDerivedStateFromProps(nextProps, prevState)
      1、在接受父组件改变后的props需要重新渲染组件时用得比较多
      2、接受一个参数：nextProps
      3、通过对比nextProps和this.props，将nextProps的state为当前组件的state，从而重新渲染组件
      代码实现：  componentWillReceiveProps (nextProps) {
                  nextProps.openNotice !== this.props.openNotice&&this.setState({
                  openNotice:nextProps.openNotice
                          }，() => {
                   console.log(this.state.openNotice:nextProps)
                  //将state更新为nextProps,在setState的第二个参数（回调）可以打印出新的state
                 })
             }
            // before
               componentWillReceiveProps(nextProps) {
                 if (nextProps.isLogin !== this.props.isLogin) {
                   this.setState({ 
                     isLogin: nextProps.isLogin,   
                   });
                 }
                 if (nextProps.isLogin) {
                   this.handleClose();
                 }
               }

             // after
               static getDerivedStateFromProps(nextProps, prevState) {
                 if (nextProps.isLogin !== prevState.isLogin) {
                   return {
                     isLogin: nextProps.isLogin,
                   };
                 }
                 return null;
               }

##### (2) shouldComponentUpdate(nextProps,nextState)
      1、主要用于性能优化；唯一用于控制组件重新渲染的生命周期。
      2、由于setState之后，state会发生变化，组件会进入重新渲染的流程，return false可以组织组件的更新；
      3、父组件的重新渲染会导致其子组件的重新渲染，但不需要所有子组件都渲染，因此需要在子组件的该生命周期中做判断
##### (3) componentWillUpdate (nextProps,nextState)  //新版本：getSnapshotBeforeUpdate(prevProps, prevState)
      当shouldComponentUpdate返回true；组件进入重新渲染阶段
##### (4) componentDidUpdate(prevProps,prevState)
      组件更新完毕后，react在第一次初始化成功会进入会进入componentDidmount,之后每次重新渲染后都会进入这个生命周期
      参数prevProps：更新前的props；prevState：更新前的state
      代码实现：
      componentDidUpdate(prevProps, prevState) {
          if (!prevState.isLogin && this.props.isLogin) {
           this.handleClose();
            }
       }
##### (5) render()
      render函数会插入jsx生成的dom结构，react会生成一份虚拟DOM树，在每一次组件更新时，在此react会通过其diff算法比较更新前后的新旧DOM树，
      比较以后，找到最小的有差异的DOM节点，并重新渲染。
### 4、卸载阶段 Unmounting
##### (1) componentWillUnmount ()
      完成组件的卸载和数据的销毁
      1、clear在组件中所有的setTimeout、setInterval
      2、移除所有组件中监听 removeEventlistener
      3、在组件中的ajax请求返回setState,而你组件销毁的时候，请求还未完成，报warning
## React JavaScript XML（JSX）: 用于申明react当中的元素 
## 基本用法：
##### 1、创建VDOM元素对象(JSX的顶层只能有一个根元素)
      const element = <h1> hello, react </h1>    //note： JSX语法，不是字符串、不是HTML
##### 2、元素渲染
      ReactDOM.render(VOM,containerDOM）   //会覆盖挂载到的containerDOM原生中的所有内容，引入组件可解决这个问题
例1：[firstReact](https://github.com/cqujlj/React/blob/master/html/01-firstReact.html)
例2：[展示列表数据](https://github.com/cqujlj/React/blob/master/html/02-listData.html)
#### 3、JSX嵌入表达式
     若jsx的内容时动态的，可在JSX中使用JavaScript表达式，
     书写规则：{表达式}  表达式可以是变量、字符串、数组、函数调用等任意js表达式
###### 注释方式：{/* 我是一段注释 */}  
##### 3.1 嵌入变量
###### 情况1：当变量是Number、String、Array类型时，可以直接显示
      <h2>{this.state.name}</h2>  //name:"Jack"
      <h2>{this.state.age}</h2>   //age:10
      <h2>{this.state.hobbies}</h2>  //hobbies:["篮球", "唱跳", "rap"]
###### 情况2：“当变量是null、undefined、Boolean,则需要转换成字符串；
     比如toString方法、和空字符串拼接，String(变量)等方式
     <h2>{this.state.flag}</h2>   //flag:true   不显示
        <h2>{this.state.flag + ""}</h2>   //变成字符串后，显示
###### 情况3：对象类型不能作为子元素
       <h2>123{this.state.friend}</h2>  // friend: { name: "kobe", age: 40}  报错
##### 3.2 嵌入表达式
###### 运算表达式 ：<h2>{this.state.firstName + " " + this.state.lastName}</h2>
###### 三元表达式 ：<h2>{this.state.age >= 18 ? "成年人": "未成年人"}</h2>
###### 执行一个函数 ： <h2>{this.sayHello("kobe")}</h2>
##### 3.3 jsx绑定属性
###### title属性：
      <h2 title={this.state.title}>Hello World</h2>  *title: "你好啊"*/,
###### src属性：
     <img src={this.state.imgUrl} alt=""/>  /*imgUrl: "https://users/upload_avatars/1102036/c3628b478f06.jpeg"*/,
###### href属性：
     <a href={this.state.link} target="_blank">百度一下</a>   /*link: "https://www.baidu.com"*/
###### class属性：jsx中不允许直接写class，使用className替代
     <div className={"message " + (this.state.active ? "active": "")}>你好啊</div>  //active: false
###### style属性：style后面跟的是一个对象类型，对象中是样式的属性名和属性值；属性名转成驼峰标识，而不是连接符-
     <div style={{fontSize: "30px", color: "red", backgroundColor: "blue"}}>我是文本</div>
#### 4、JSX监听事件
     React 事件的命名采用小驼峰式（camelCase），而不是纯小写
     通过{}传入一个事件处理函数，这个函数会在事件发生时被执行
     例：<button onClick={this.btnClick}>点我一下(React)</button>
##### 4.1 this绑定问题
     由于btnClick不是我们主动调用的，而是当onClick发生时，react内部调用了btnClick，这种方式调用函数不知道如何正确绑定this
###### 解决方案
     方法一：bind给btnClick显示绑定this  -->  <button onClick={this.btnClick.bind(this)}>点我</button>
     方法二：通过在构造方法中直接给this.btnClick绑定this --> 在constructor中：this.btnClick = this.btnClick.bind(this);
     方法三：使用箭头函数 -->  btnClick = () => { console.log(this); } } 
     方案四：（推荐使用）事件监听时传入箭头函数  -->   <button onClick={() => this.btnClick()}>点我</button>
##### 4.2 事件参数传递
     情况一：获取event对象 --> 拿到event对象来做一些事情（比如阻止默认行为)
      btnClick(e) {   /*如果用不到this，直接传入函数就可以获取到event对象*/
          e.preventDefault();
           console.log(e);
       }
     情况二：获取更多参数 --> 传入一个箭头函数，主动执行的事件函数，并且传入相关的其他参数
      <a href="#" onClick={e => this.aClick(e, item, index)}>{item}</a>
      aClick(e, item, index) {
          e.preventDefault();
          console.log(item, index);
          console.log(e);
     }
#### 5、模块和组件
##### 5.1 模块： 向外提供特定功能的js程序，就是一个js文件
##### 5.2 组件：用来实现特定（局部）功能效果的代码集合（html/css/js）
#### 6、定义组件
##### 方式1：工厂函数组件（简单组件）
      接受带有数据的单个“ props”（代表属性）对象参数并返回React元素,使用参数：{props.属性名}
##### 方式2：ES6类组件  （复杂组件）
     使用参数：{this.props.属性名}
代码示例：[使用工厂函数组件和ES6类组件](https://github.com/cqujlj/React/blob/master/html/03-components.html)

#### 7、组件的3个属性
##### 7.1 state
     组件 --> 状态机，通过与用户交互，实现不同状态，然后渲染UI，使得用户界面和数据保持一致
代码示例：[state的基本用法](https://github.com/cqujlj/React/blob/master/html/04-components-state.html)
###### 组件中的数据：
     1、参与界面更新的数据（数据流）：当数据变量时，需要重新渲染组件；定义在当前对象的state
           定义：在构造函数中：this.state = {定义的数据}
           更新状态：this.setState()
     2、不参与界面更新的数据：当数据变化时，不需要重新渲染
###### (1)初始化state,通过一个类的构造函数来初始化this.state，类组件始终使用props调用基础构造函数
     constructor(props) {
       super(props);
           this.state = {
           isShowText: true
           }
     };
###### (2)获取stat
     this.state.isShowText
###### (3)修改state
     this.setState({ isShowText : !this.state.isShowText})
##### 7.2 props 
     props不可变，子组件只能通过props来传递参数
代码示例：[props的基本用法](https://github.com/cqujlj/React/blob/master/html/05-components-props.html)
###### 在函数组件中使用：props.属性名
    function PersonMsg(props) {
            return (
                <ul>
                    <li>姓名：{props.name}</li>
                    <li>年龄：{props.age}</li>
                </ul>
            )
        }
###### 在ES6类组件中使用  this.props.属性名
     class Person extends React.Component {
            render(){
                return(
                    <ul>
                        <li>姓名：{this.props.name}</li>
                        <li>年龄：{props.age}</li>
                    </ul>
                    )
              }
        }
###### 指定属性值的类型和必要性 --> 可以声明prop为指定的基本数据类型
     PersonMsg.prototype = {
          name : PropTypes.string.isRequired,   //name是必填项
          age : PropTypes.number,
          //requiredAny: React.PropTypes.any.isRequired,    // 不可空的任意类型
       };
###### 指定默认值
     PersonMsg.defaultProps = {
        age:18,
     };
###### 
######  将对象的所有属性通过props传递：    <Person{...person}>
###### 在父组件中设置 state， 并通过在子组件上使用 props 将其传递到子组件上
     ReactDOM.render(<PersonMsg {...person}/>, document.getElementById('idName'))
代码示例：[父子组件传值](https://github.com/cqujlj/React/blob/master/html/06-state$$props.html)
##### 7.3 refs --> 事件处理
代码示例：[refs事件处理](html/06-components-refs.html)
######  组件内的标签都可以定义ref属性来标识自己
     方式1：<input type="text" ref="content"/>
     方式2：<input type="text" ref={input=>this.input=input}/>   //将当前的input 赋值给组件里面的input
###### 事件处理
     通过onXxxx属性来指定组件内的事件处理函数，如onClick、onBlur -->  <input type="text"  onBlur={this.handleBlur}/>
     react中的事件是通过委托方式处理的（委托给最外层元素）
     通过event.target可以得到发生事件的DOM元素 --》 handleBlur(event){  alert(event.target.value) }
     note：要在constructor中给处理函数强制绑定this --> this.handleBlur=this.handleClick.bind(this)
代码实例：[组件的组合使用](html/componentCombine.html)
            
      
      
      
