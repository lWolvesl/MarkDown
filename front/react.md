# REACT

## 一、JSX

### 1.1 防止注入

```jsx
const title = response.potentiallyMaliciousInput;
// 直接使用是安全的：
const element = <h1>{title}</h1>;
```

- React DOM 在渲染所有输入内容之前，默认会进行[转义](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

### 1.2 对象

- Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。

- React.createElement()
  - 第一个参数是必填，传入的是似HTML标签名称，eg: ul, li 
  - 第二个参数是选填，表示的是属性，eg: className 
  - 第三个参数是选填, 子节点，eg: 要显示的文本内容

```js
React.createElement(
  type,
  [props],
  [...children]
)
```

- 下列两种写法对等

```jsx
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

- 上述两种均会转化为以下代码

```jsx
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

## 二、元素

### 2.1 元素简介

- 元素 != 组件
- 组件是由元素构成的
- <font color="red">元素是构成 React 应用的最小砖块。</font>
- 元素描述了你在屏幕上想看到的内容。

```jsx
const element = <h1>Hello, world</h1>;
```

- 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

### 2.2 元素转化DOM

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

### 2.3 更新已渲染元素

- 时钟：

```js
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));}

setInterval(tick, 1000);  //HTML DOM自带组件 setInterval(code,millisec[,"lang"])
```

## 三、组件 & Props

- 组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

### 3.1 函数组件与class组件

- 函数组件

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

- ES6中的class

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### 3.2 渲染组件

- 如下会渲染“Hello, Sara”

```jsx
function Welcome(props) {  
  return <h1>Hello, {props.name}</h1>;
}
const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### 3.3 组合组件

- 可以用同一组件来抽象出任意层次的细节。
  - 按钮
  - 表单
  - 对话框
  - 甚至整个屏幕都内容

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

### 3.4 提取组件

- 将组件拆分成更小的组件

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

- 该组件用于描述一个社交媒体网站上的评论功能，它接收 `author`（对象），`text` （字符串）以及 `date`（日期）作为 props。

- 提取组件

```jsx
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

```jsx
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

- 整合

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

- 最初看上去，提取组件可能是一件繁重的工作，但是，在大型应用中，构建可复用组件库是完全值得的。根据经验来看，如果 UI 中有一部分被多次使用（`Button`，`Panel`，`Avatar`），或者组件本身就足够复杂（`App`，`FeedStory`，`Comment`），那么它就是一个**可复用组件的候选项**。

### 3.5 Props的只读

- 组件无论是使用函数声明还是通过 class 声明，都决不能修改自身的 props。

- 纯函数：不修改Props的函数

- **所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

- state：专门用来处理动态UI

## 四、State & 生命周期

### 4.1 封装真正可复用的 `Clock` 组件

- 如2.3中的时钟，本章将封装真正可复用的 `Clock` 组件

#### 4.1.1 外观

```jsx
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

- 加入state实现计时器，并且需要每秒更新 UI。

- state与props相似，但是state是私有的，完全受控于当前组件

#### 4.1.2 将函数组件转换成 class 组件

- 通过以下五步将 `Clock` 的函数组件转成 class 组件：
  1. 创建一个同名的 ES6 class，并且继承于 `React.Component`。
  2. 添加一个空的 `render()` 方法。
  3. 将函数体移动到 `render()` 方法之中。
  4. 在 `render()` 方法中使用 `this.props` 替换 `props`。
  5. 删除剩余的空函数声明。

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

- 现在的Clock组件被定义为class，而非函数
- 每次组件更新时 `render` 方法都会被调用，但只要在相同的 DOM 节点中渲染 `<Clock />` ，就仅有一个 `Clock` 组件的 class 实例被创建使用。这就使得我们可以使用如 state 或生命周期方法等很多其他特性。

#### 4.1.3 向class组件中添加局部的state

- 通过三步将data从props移动到state中

##### 4.1.3.1 把render方法中的this.props.date替换成this.state.date:

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>      </div>
    );
  }
}
```

##### 4.1.3.2 添加一个class构造函数，然后在this.state中赋初值：

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

- Class组件应始终使用props参数来调用父类的构造函数

#####3.移除\<Clock />中的date属性

```jsx
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

##### 4.1.3.3 小结

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

- 接下来，我们会设置 `Clock` 的计时器并每秒更新它。

#### 4.1.4 将生命周期方法添加到Class中

- 在具有许多组件的应用程序中，当组件被销毁时释放所占用的资源所非常重要的。
- 当Clock组件第一次被渲染到DOM中的时候，就为其设置一个计时器。者在React中被称为“挂载”
- 同时当DOM中Clock组件被删除的时候，应该清除计时器。这在React中被称为“卸载”
- 为class组件生命特殊方法，当组件挂载或卸载时就会去执行这些方法：

```jsx
class Clock extends React.Component {
    constructor(props) {
        super(props);
        this.state = {date: new Date()};
    }

    componentDidMount() {
    }

    componentWillUnmount() {
    }

    render() {
        return (
            <div>
                <h1>Hello,World!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
            </div>
        );
    }
}
```

- componentDidMount() 挂载方法
- componentWillUnmount() 卸载方法

- 这种方法叫做“生命周期方法”

![截屏2021-10-09 下午10.22.50](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-09%20%E4%B8%8B%E5%8D%8810.22.50.png)

- componentDidMount() 方法会在组件被渲染到DOM中后运行

```js
		componentDidMount() {
        this.timerID=setInterval(
            () => this.tick(),
            1000
        );
    }
```

- 将计时器ID保存在this.timerID中
- 尽管this.props和this.state是React本身设置的，且均含有特殊的含义，但是你可以想class中随意添加不参与数据流的额外字段

- 在componentWillUnmount()清除计时器

```jsx
componentWillUnmount() {
    clearInterval(this.timerID);
}
```

- 最后实现一个tick方法，Clock每秒都会调用它

```jsx
tick(){
    this.setState({
        date: new Date()
    });
}
```

- 现在时钟每秒都会刷新

#### 总结

- 概括方法调用顺序
  - 当\<Clock />被传给ReactDOM.render()的时候，React会调用Clock组件的构造函数。因为Clock需要显示当前的时间，所以他会用一个包含当前事件的对象来初始化this.state。
  - 之后React会调用组件的render()方法。这就是React确定该在页面上展示什么的方式。然后React更新DOM来匹配Clock渲染的输出
  - 当Clock的输出被插入到DOM中后，React就会滴哦啊用ComponentDidMount()生命周期方法。在这个方法中，Clock组件向浏览器请求设置一个计时器来每秒调用一次组件的tick()方法。
  - 浏览器每秒都会调用一次tick()方法。在这个方法之中，Clock组件会通过调用setState()方法来计划进行一次UI更新。得益于setState()的调用，React能够知道state已经改变了，然后就会重新调用render()方法来确定页面上该显示什么。这一次，render()的方法的this.state.date就不一样了，如此以来就会渲染输出更新过的时间。React也会相应的更新DOM。
  - 一旦Clock组件从DOM中被移除，React就会调用componentWillUnmount()生命周期方法，这样计时器就停止了。

### 4.2 正确地使用State

- 关于setState()应了解
  - **1.不要直接修改State**
    - 不能使用```this.state.comment='hello';```
    - 而是应该使用```this.setState({comment:'hello'});```
    - 构造函数是唯一给```this.state```赋值的地方
  - **2.State的更新可能是异步的**
    - 出于性能考虑，React可能把多个```setState()```调用合并成一个调用
    - 但因为```this.props```和```this.state```可能会异步更新，所以你不要依赖他们的值来更新下一个状态
    - 例如:

```jsx
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

- - - 此代码无法更新计数器
    - 要解决这个问题，可以让```setState()```接收一个函数而非一个对象
    - 这个函数用上一个state作为第一个参数，将此次被更新的props作为第二个参数

```jsx
this.setState((state,props)=>{
  counter: state.counter + props.increment
});
```

- - - 除lambda表达式外，普通函数也同样可以
  - **3.State的更新会被合并**
    - 当你调用```setState()```的时候，React会把你提供的对象合并到当前的state
    - 例如你的state包含几个独立的变量：

```jsx
constructor(props){
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}
```

- - - 然后你可以分别调用```setState()```来单独地更新它们：

```jsx
componentDisMount(){
  fetchPosts().then(response => {
    this.setState({
      posts: response.posts
    });
  });
  fetchComments().then(response => {
    this.setState({
      Comments: response.Comments
    });
  });
}
```

- - - 这里的合并是浅合并，所以```this.setState({comments})```完整保存了`this.state.posts`,但是完全替换了`this.state.comments`。

### 4.3 数据是向下流动的

- 无论是父组件或者是子组件都无法知道某个组件是有状态还是无状态，并且它们也并不管线它是函数组件还是class组件
- 这就是为什么成state为局部的或是封装的原因，除了拥有并设置了它的组件，其他组件均无法访问。
- 组件可以选择把它的state作为props向下传递给它的子组件中：

```jsx
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```

- 这对于自定义组件同样适用：

```jsx
<FormattedDate date={this.state.date} />
```

- FormattedDate组件会在其props中接收参数date,但是组件本身无法知道它是来自于Clock的state，或者是Clock的props，还是手动输入的：

```jsx
function FormattedDate(props){
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

- 上述概括为：**父组件的state在子组件中使用props调用**
- 这通常被叫做“自上向下”或是“单向”的数据流。任何的State总是所属于特定的组件，而且从该state派生的任何数据或UI只能影响DOM树中“低于”他们的组件。

- 在React应用中，组件是有状态组件还是无状态组件属于组件实现的细节，它可能会随着时间的推移而改变。你可以在有状态的组件使用无状态的组件，反之亦然。

## 五、事件处理

- React事件的命名采用小驼峰式，而非纯小写
- 使用JSX语法你需要传入一个函数作为事件处理器，而非字符串

- 如传统HTML：

```html
<button onclick="active">
  Active
</button>
```

- 在React中

```jsx
<button onClick={active}>
  active
</button>
```

- 在React中另一个不同点是你不能通过返回false的方式阻止默认行为。
- 你必须显式的使用preventDefault。
- 如HTML：

```html
<from onsubmit="console.log('You Clicked submit.');return false">
  <button type="submit">
 		submit
	</button>
</from>
```

- 而在React中

```jsx
function From(){
    function handleSubmit(e){
        console.log('You Clicked submit.');
        e.preventDefault();

    }

    return(
        <form onSubmit={handleSubmit}>
            <button type={"submit"}>submit</button>
        </form>
    );
}
```

- e是一个合成事件。根据W3C规范定义，无需担心浏览器兼容问题。
- 使用React一般不需要使用addEventListener为已创建的DOM元素添加监听器。事实上，你只需要在该元素初始化渲染的时候添加监听器即可

```jsx
//在构造器中使用，此处的this必不可少
this.hanleClick = this.handleClick.bind(this);
```

- 例：开关组件：让一个组件在ON和OFF之间来回跳转

```jsx
class From01 extends React.Component {
    constructor(props) {
        super(props);
        this.state = {isTogglenOn: true};

        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        console.log('You Clicked submit.');
        this.setState(prevState =>({
            isToggleOn: !prevState.isToggleOn
        }));
    }

    render() {
        return (
            <button onClick={this.handleClick}>{this.state.isToggleOn? 'ON': 'OFF'}</button>
        );
    }
}
```

```jsx
ReactDOM.render(
    <From01/>,
    document.getElementById('test')
)
```

- 必须谨慎对待JSX回调函数中的<font color="red">this</font>，在js中，class的方法默认不会绑定this。
- 如果你忘记绑定this.handleClick并把它穿入了onClick，让你调用函数的时候，this的值为undefined.
- 这与js函数工作原理有关，通常情况应bind（this）
- 试验性语法，publick class fields语法

```jsx
class LoggingButton extends React.Component {
  // 此语法确保 `handleClick` 内的 `this` 已被绑定。
  // 注意: 这是 *实验性* 语法。
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

- Create React App 默认启用此语法
- 若无class fields语法，可以在回调中使用箭头函数

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

- 注意：如果回调函数作为prop传入子组件时，这些组件可能会进行额外的渲染

## 六、向事件处理程序传递参数

- 例：删除某一行

```jsx
//方法一
<button onClick={(e)=>this.deleteRow(id,e)}>DeleteRow</button>
//方法二
<button onClick={this.deleteRow.bind(this,id)}>DeleteRow</button>
```

- 上述两种方法等效，分别使用箭头函数和bind实现
- 对象e会被作为第二参数传递
  - 如果是箭头函数则事件对象必须显示的进行传递
  - bind的方式事件对象以及更多的参数将会被隐式的进行传递

## 七、条件渲染

- 使用if或者条件运算符创建元素来表现当前状态，然后让React根据他们来更新UI

```jsx
function UserGreeting(props) {
    return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
    return <h1>Please login in</h1>;
}

function Greeting(props) {
    const isLoggedIn = props.isLoggedIn;
    if (isLoggedIn) {
        return <UserGreeting/>;
    }else {
        return <GuestGreeting/>;
    }
}

ReactDOM.render(
    <Greeting isLoggedIn={true}/>,
    document.getElementById("greet")
)
```

### 7.1 元素变量

- 使用变量储存元素。它可以帮助你有条件地渲染组件的一部分，而其他的渲染部分并不会因此而改变。

```jsx
class LoginControl extends React.Component {
    constructor(props) {
        super(props);
        this.handleLoginClick = this.handleLoginClick.bind(this);
        this.handleLogoutClick = this.handleLogoutClick.bind(this);
        this.state = {isLoggedIn: false};
    }

    handleLoginClick() {
        this.setState({isLoggedIn: true});
    }

    handleLogoutClick() {
        this.setState({isLoggedIn: false});
    }

    render() {
        const isLoggedIn = this.state.isLoggedIn;
        let button;

        if (isLoggedIn) {
            button = <LogoutButton onClick={this.handleLogoutClick} />;
        } else {
            button = <LoginButton onClick={this.handleLoginClick} />;
        }

        return (
            <div>
                <Greeting isLoggedIn={isLoggedIn} />
                {button}
            </div>
        );
    }
}

ReactDOM.render(
    <LoginControl/>,
    document.getElementById("greet")
)
```

### 7.2 与运算符&&

- 在{}中包裹JSX嵌入任何表达式。

```jsx
function Mailbox(props){
    const unreadMessages = props.unreadMessages;
    return(
        <div>
            <h1>Hello!</h1>
            {unreadMessages.length > 0 &&
                <h2>You have {unreadMessages.length} unread message.</h2>
            }
        </div>
    );
}

const messages = ['React','Re:React','Re:Re:React'];
ReactDOM.render(
    <Mailbox unreadMessages={messages}/>,
    document.getElementById('test1')
);
```

- 若messages中有值，则会渲染\<h2>
- 若message为空，则&&右侧的元素就会跳过

### 7.3 三目运算符

- 如第五章的动态button组件 三元表达式

```jsx
<button onClick={this.handleClick}>{this.state.isToggleOn? 'ON': 'OFF'}</button>
```

```jsx
{isLoggedIn
  ?<LogoutButton onClick={this.handleLogoutClick} />
  :<LoginButton onClick={this.handleLoginClick} />
}
```

### 7.4 阻止组件渲染

- 隐藏组件，即使它已经被其他组件渲染
- 让render方法直接返回null，而不进行任何渲染

```jsx
function WarningBanner(props) {
    if (!props.warn) {
        return null;
    }
    return (
        <div className="warning">
            Warning!
        </div>
    );
}

class Page extends React.Component {
    constructor(props) {
        super(props);
        this.state = {showWarning: true};
        this.handleToggleClick = this.handleToggleClick.bind(this);
    }

    handleToggleClick() {
        this.setState(state => ({
            showWarning: !state.showWarning
        }));
    }

    render() {
        return (
            <div>
                <WarningBanner warn={this.state.showWarning}/>
                <button onClick={this.handleToggleClick}>
                    {this.state.showWarning ? 'Hide' : 'Show'}
                </button>
            </div>
        );
    }
}

ReactDOM.render(
    <Page/>,
    document.getElementById('test2')
);
```

- 返回null并不会影响组件生命周期

## 八、列表&KEY

- 例如下列利用map（）函数让数组中每一项翻倍

```jsx
const numbers = [1,2,3,4,5]
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

- 在React中，把数组转换成元素列表的过程是类似的

### 8.1 渲染多个组件

- 通过{}在jsx中构建一个元素集合
- 例如使用map（）将每个元素编程\<li>标签

```jsx
const listItem=numbers.map((number)=>
    <li>{number}</li>
);

ReactDOM.render(
    <ul>{listItem}</ul>,
    document.getElementById('test3')
);
```

### 8.2 基础列表组件

- 8.1例

```jsx
function NumberList(props){
    const numbers = props.numbers;
    const listItem=numbers.map((number)=>
        <li>{number}</li>
    );

    return (
        <ul>{listItem}</ul>
    );
}

ReactDOM.render(
    <NumberList numbers={numbers}/>,
    document.getElementById('test3')
);
```

- 此处会报警告```需要一个Key```

```jsx
const listItem=numbers.map((number)=>
    <li key={number.toString()}>{number}</li>
);
```

### 8.3 KEY

- key帮助React识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。

- 一个key最好是这个元素列表中拥有的一个独一无二的字符串
- 尽量不要使用元素索引index作为key：{index}，此做法性能将变差，还可能引起组件状态的问题

### 8.4 用Key提取组件

- 元素key只有放在就近数组的上下文中才有意义

### 8.5 Key只是在兄弟节点之间必须唯一

- 不需要是全局唯一的。

```jsx
const posts=[id:1,title:'Hello World',context:'Welcome to learning React!']
```

- Key会传递信息给React，但不会传递给你的组件。如果你的组件中需要使用Key属性的值，可以用其他属性名直接显性输出，不能用props.key

### 8.6 在JSX中嵌入map（）

```jsx
function ListItem(props){
    return <li>{props.value}</li>
}

function NumberList02(props){
    const numbers = props.numbers;
    return(
        <ul>
            {numbers.map((number)=>
                <ListItem key={number.toString()}
                          value={number}/>
            )}
        </ul>
    );
}

ReactDOM.render(
    <NumberList02 numbers={numbers}/>,
    document.getElementById('test4')
);
```

## 九、表单

- 表单通常会保持一些内部的state

- 如

```html
<form>
  <label>
    名字:
    <input type="text" name="name"/>
  </label>
  <input type="submit" value="提交"/>
</form>
```

- 在React执行会很方便处理提交和访问数据。实现这种的效果的标准方式是使用“受控组件”。

### 9.1 受控组件

- 在HTML中，表单元素（如`<input>`、`<textarea>`和`<select>`）之类的表单元素通常会自己维护state，并根据用户输入进行更新。而在React中，可变状态通常保存在组件的state属性中，只能通过setState()来更新

- 可以把两者结合起来使React的state称为“唯一数据源”。渲染表单的React组件还控制着用户输入过程中表单发生的操作。被React以这种方式控制取值的表单输入元素就叫做**受控组件**。
- 例：提交表单时打印名称：

```jsx
class NameForm extends React.Component{
    constructor(props) {
        super(props);
        this.state = {value: ''};
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(event){
        this.setState({value: event.target.value});
    }

    handleSubmit(event){
        alert('提交的名字:'+ this.state.value);
        event.preventDefault();
    }

    render() {
        return(
            <form onSubmit={this.handleSubmit}>
                <label>
                    名字:
                    <input type="text" name="name" value={this.state.value} onChange={this.handleChange}/>
                </label>
                <input type="submit" value="提交"/>
            </form>
        );
    }
}
```

- 设置了value属性，因此显示值将始终为`this.state.value`这使得React的state称为唯一数据源。
- `handleChange`方法在每次输入框中内容发生改变时都会执行并更新`this.state.value`。
- 对于受控组件来说，输入值始终由React的state驱动。

### 9.2 textarea标签

- 在HTML中，`<textarea>`元素通过其子元素定义其文本
- 而在React中，<textarea>使用value属性代替，类似于input。

```jsx
<textarea value={this.state.value} onChange={this.handleChange}/>
```

### 9.3 select标签

- select下拉列表标签

```html
<select>
  <option value="grapfruit">葡萄柚</option>
  <option value="lime">酸橙</option>
  <option value="concount">椰子</option>
  <option selected value="apple">苹果</option>
</select>
```

- React不实用selected标签，直接在跟节点使用value即可

```jsx
class FlavorForm extends React.Component {
    constructor(props) {
        super(props);
        this.state = {value: 'apple'}
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(event) {
        this.setState({value: event.target.value});
        console.log("onchange");
    }

    handleSubmit(event) {
        alert('你喜欢的水果是' + this.state.value);
        event.preventDefault();
    }

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    <select value={this.state.value} onChange={this.handleChange}>
                        <option value="grapfruit">葡萄柚</option>
                        <option value="lime">酸橙</option>
                        <option value="concount">椰子</option>
                        <option value="apple">苹果</option>
                    </select>
                </label>
                <input type="submit" value="提交"/>
            </form>
        );
    }
}
```

- 直接使用`this.state.value`控制select标签中的选定值

- React支持多选

```jsx
<select multiple={true} value={['b','c']}></select>
```

### 9.4 文件input标签

- input的value只读，所以它是一个非受控组件

### 9.5 处理多个输入

- 当要处理多个input输入时，可以给每个元素添加name属性，并通过`event.target.name`进行操作

```jsx
handleInputChange(event){
  const target = event.target;
  const value = target.name === 'isGoing' ? target.checked:target.value;
  const name = target.name;
  this.setState({
    [name]: value
  });
}
```

- 这里使用了ES6计算属性名称更新给定输入名称对应的state值，在ES5中：

```jsx
var partialState() = {};
partialState[name] = value;
this.setState(partialState);
```



- 由于set State（）自动将部分state合并到当前state，只需要调用它更改部分的state即可

### 9.6 受控输入空值

- 在受控组件上指定value的prop会阻止用户更改输入。如果你指定了value，但输入仍可编辑，则可能是你意外的将value设定为undefined或null

## 十、状态提升

- 通常，多个组件需要反应相同的变化数据，这时可以将共享状态提升到最近的共同父组件中去。
- 计算温度是否能将水煮沸

```jsx
function BoilingVerdirt(props){
  if(props.celsius > 100){
    return <p>The water would boil.</p>
  }
  return <p>The water would not boil.</p>
}
```

- 接下来创建组件渲染input，并将值放在`this.state.temperature`中
- 它将根据输入值渲染上组件：

```jsx
class TemperatureInput extends React.Component {
    constructor(props) {
        super(props);
        this.state = {temperature: ''};
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(event) {
        this.setState({temperature: event.target.value});
    }

    render() {
        const temperature = this.state.temperature;
        return (
            <fieldset>
                <legend>
                    Enter temperature in Celsius:
                </legend>
                <input
                    type='number'
                    value={temperature}
                    onChange={this.handleChange}
                />
                <BoilingVerdirt celsius={parseFloat(temperature)}/>
            </fieldset>
        );
    }
}
```

![截屏2021-10-10 下午9.07.43](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-10%20%E4%B8%8B%E5%8D%889.07.43.png)

### 10.1 添加第二个输入框

```jsx
const scaleNames ={
    c: 'Celsius',
    f: 'Fahrenheit'
};
```

- 从类中抽取TemperatureInput组件，出现提供华氏度输入框

```jsx
class Calculator extends React.Component{
    render() {
        return(
            <div>
                <TemperatureInput scale="c"/>
                <TemperatureInput scale="f"/>
            </div>
        );
    }
}
```

![截屏2021-10-10 下午9.15.33](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-10%20%E4%B8%8B%E5%8D%889.15.33.png)

- 但是此时无法相互转换

### 10.2 编写转换函数

- 数值转换

```jsx
function toCelsius(fahrenheit) {
    return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
    return (celsius * 9 / 5) + 32;
}
```

```jsx
function tryConvert(temperature, convert) {
    const input = parseFloat(temperature);
    if (Number.isNaN(input)){
        return '';
    }
    const output = convert(input);
    const rounded = Math.round(output*1000)/1000;
    return rounded.toString();
}
```

状态提升

- 现在是两个组件分别拥有自己的温度，更改时需要触发上述方法
- 在React中，多个组件需要恭喜state向上移动到他们的最近共同父组件中，便可实现共享state，这就是**状态提升**
- 首先，将组件中的`this.state.temperature`替换为`this.props.temperature`
- props是只读的。当temperature存在于组件中的state时，使用this.state（）便可修改它。然而temperature是由父组件传入的prop，此时组件便失去了对它的控制权。
- 在React中，这个问题通常使用“受控组件”解决
- 现在更新温度时，调用`this.props.onTemperatureChange`来更新它，此方法名为自定义。
- 移除自身的state，用props读取数据，使用Calculator提供的onTemperatureChange()更改数据
- 现在开始更改Calculator组件
- 总：

```jsx
function BoilingVerdirt(props) {
    if (props.celsius > 100) {
        return <p>The water would boil.</p>
    }
    return <p>The water would not boil.</p>
}

const scaleNames = {
    c: 'Celsius',
    f: 'Fahrenheit'
};

function toCelsius(fahrenheit) {
    return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
    return (celsius * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
    const input = parseFloat(temperature);
    if (Number.isNaN(input)){
        return '';
    }
    const output = convert(input);
    const rounded = Math.round(output*1000)/1000;
    return rounded.toString();
}

class TemperatureInput extends React.Component {
    constructor(props) {
        super(props);
        // this.state = {temperature: ''};
        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(event) {
        //this.setState({temperature: event.target.value});
        this.props.onTemperatureChange(event.target.value);
    }

    render() {
        //const temperature = this.state.temperature;
        const temperature = this.props.temperature;
        const scale = this.props.scale;
        return (
            <fieldset>
                <legend>
                    Enter temperature in {scaleNames[scale]}:
                </legend>
                <input
                    value={temperature}
                    onChange={this.handleChange}
                />
                <BoilingVerdirt celsius={parseFloat(temperature)}/>
            </fieldset>
        );
    }
}

class Calculator extends React.Component {
    constructor(props) {
        super(props);
        this.state={scale: 'c',temperature: ''};
        this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
        this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    }

    handleCelsiusChange(temperature){
        this.setState({scale: 'c',temperature});
    }

    handleFahrenheitChange(temperature){
        this.setState({scale: 'f',temperature});
    }

    render() {
        const scale = this.state.scale;
        const temperature = this.state.temperature;
        const celsius = scale === 'f'?tryConvert(temperature,toCelsius):temperature;
        const fahrenheit = scale === 'c'?tryConvert(temperature,toFahrenheit):temperature;
        return (
            <div>
                <TemperatureInput scale="c" temperature={celsius} onTemperatureChange={this.handleCelsiusChange}/>
                <TemperatureInput scale="f" temperature={fahrenheit} onTemperatureChange={this.handleFahrenheitChange}/>
                <BoilingVerdirt celsius={parseFloat(celsius)}/>
            </div>
        );
    }
}

ReactDOM.render(
    <Calculator/>,
    document.getElementById('test7')
);
```

![截屏2021-10-10 下午9.45.56](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-10%20%E4%B8%8B%E5%8D%889.45.56.png)

- 此时将动态转换

### 10.3 总结

- 提升state的方式比双向绑定需要编写更多的代码，但是排查和隔离bug的工作量将会大大降低
- 流程
  - React 会调用 DOM 中\<input> 的 onChange 方法。在本实例中，它是 TemperatureInput 组件的 handleChange 方法。
  - TemperatureInput 组件中的 handleChange 方法会调用 this.props.onTemperatureChange()，并传入新输入的值作为参数。其 props 诸如 onTemperatureChange 之类，均由父组件 Calculator 提供。
  - 起初渲染时，用于摄氏度输入的子组件 TemperatureInput 中的 onTemperatureChange 方法与 Calculator 组件中的 handleCelsiusChange 方法相同，而，用于华氏度输入的子组件 TemperatureInput 中的 onTemperatureChange 方法与 Calculator 组件中的 handleFahrenheitChange 方法相同。因此，无论哪个输入框被编辑都会调用 Calculator 组件中对应的方法。
  - 在这些方法内部，Calculator 组件通过使用新的输入值与当前输入框对应的温度计量单位来调用 this.setState() 进而请求 React 重新渲染自己本身。
  - React 调用 Calculator 组件的 render 方法得到组件的 UI 呈现。温度转换在这时进行，两个输入框中的数值通过当前输入温度和其计量单位来重新计算获得。
  - React 使用 Calculator 组件提供的新 props 分别调用两个 TemperatureInput 子组件的 render 方法来获取子组件的 UI 呈现。
  - React 调用 BoilingVerdict 组件的 render 方法，并将摄氏温度值以组件 props 方式传入。
  - React DOM 根据输入值匹配水是否沸腾，并将结果更新至 DOM。我们刚刚编辑的输入框接收其当前值，另一个输入框内容更新为转换后的温度值。

## 十一、组合 vs 继承

- React有十分强大的组合模式，推荐使用组合而非继承

### 11.1 包含关系

- 有些组件无法提前知道他们子组件的具体内容。在侧边栏和对话框等展现通用容器特别容易遇到这种情况
- 使用`children`prop来将他们的子组件传递到渲染结果中：

```jsx
function FancyBorder(props){
  return(
  	<div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

- 通过JSX嵌套，将任意组件传递给它们：

```jsx
function WelcomeDialog(){
  return(
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you
      </p>
    </FancyBorder>
  );
}
```

- 组件JSX标签中的所有内容都会作为一个`children`prop传递给组件
- 因为组件将{props.children}渲染在一个\<div>中，被传递的组件最终都会出现在输出结果中。

- 少数情况下，你可能需要在一个组件中预留出几个“洞”。这种情况下，我们可以不使用 children，而是自行约定：将所需内容传入 props，并使用相应的 prop。

```jsx
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

- 在React可以将任何东西作为props进行传递

### 11.2 特例关系

- 有些时候，我们会把一些组件看作是其他组件的特殊实例，比如 `WelcomeDialog` 可以说是 `Dialog` 的特殊实例

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```

- 组合同样也适用于class形式定义的组件

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```

