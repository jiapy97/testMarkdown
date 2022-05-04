

## React调用回调函数，正确设置this指向的三种方法
1. 通过bind

```js
this.increment = this.increment.bind(this);
```

2. 通过箭头函数

```js
<button onClick={this.multi}>点我*10</button> 

multi = () => {
    this.setState({
        count: this.state.count * 10
    })
}
```

3. 箭头函数包裹

```js
<button onClick={() => {this.muti2()}}>点我*10</button>  
```

## 绑定事件传递参数
>通过箭头函数传递事件参数。

```js
<li onClick={(e) => {this.movie(item,index,e)}}>{item}</li>
```

## 条件渲染
1. 通过if进行条件判断

```js
const {isLogin} = this.state;
let welcome = null;
if (isLogin) {
    welcome = <h2>欢迎回来</h2>
} else {
    welcome = <h2>请先登录！</h2>
}
```

2. 使用三目运算符

```js
{isLogin ? <h2>欢迎回来</h2> : <h2>请先登录！</h2> }
```

3. 使用逻辑与

> 下面这种写法可以省略null。

```js
{isLogin && <h2>你哈欧亚</h2> }
```

## 列表渲染
1. 使用map高阶函数

```js
{
    this.state.movies.map((item,index) => {
        return (
            <li onClick={(e) => {this.movie(item,index,e)}}>
                {item}
            </li>
        )
    })
}
```

2. 使用filter进行过滤

```js
<ul>
    {
        this.state.scores.filter(item => {
            return item >= 60
        })
    }
</ul> 
```

3. 使用slice进行截取
> 区间是左闭右开。

```js
{
    this.state.scores.slice(0,3).map(item => {
        return <li>{item}</li>
    })
}
```

## 脚手架的基本使用
>使用脚手架创建项目

* 项目名称不能包含大写字母。

```js
create-react-app demo
```

## 组件通信
### 1. 父组件向子组件传递数据通过props

* 父组件

```js
export default class App extends Component {
  render() {
    return (
      <div>
          <Child  name ='张三' age="18" />
      </div>
    )
  }
}
```

* 子组件

```js
class Child extends Component {
    constructor(props) {
        super()
        this.props = props;
    }
    render() {
        const {name,age} = this.props;
        return (
            <div>子组件获取到的name是：{name},age是：{age}</div>
        )
    }
}
```

### 2. 子组件向父组件传递数据通过回调函数

```js
import React, { Component } from 'react';

class Btn extends Component {
    render() {
        const {increment} = this.props;
        return (
            <button onClick={increment}>+1</button>
        )
    }
}


class App extends Component {
    constructor() {
        super();
        this.state = {
            count: 0
        }
    }
    render() {
        const {count} = this.state;
        return (
            <div>
                <h1>当前求和为：{count}</h1>
                <Btn increment = {e => this.increment()} />
            </div>
        );
    }
    increment() {
        console.log(666);
        this.setState({
            count: this.state.count + 1
        })
    }
}

export default App;
```

### 3. 跨组件层级通信（Context）(类组件)

```js
import React, { Component } from 'react'

const UserContext = React.createContext({
    name: '张三',
    age: 20
})

class Sub extends Component {
    render() {
        return (
            <div>
                <h1>name是：{this.context.name }</h1>
                <h1>age是：{this.context.age}</h1>
            </div>
        )
    }
}

Sub.contextType = UserContext
function Profile() {
    return (
        <div>
            <Sub />
            <ul>
                <li>设置1</li>
                <li>设置2</li>
                <li>设置3</li>
                <li>设置4</li>
            </ul>
        </div>
    )
}

export default class App extends Component {
    constructor(){
        super();
        this.state = {
            name: '李四',
            age: 18
        }
    }
    render() {
        return (
            <div>
                <UserContext.Provider value = {this.state}>
                    <Profile />
                </UserContext.Provider>
            </div>
        )
    }
}
```

> 下面是函数式组件的写法

```js
function Sub(props) {
    return (
        <UserContext.Consumer>
            {
                value => {
                    return (
                        <div>
                            <h1>name是: {value.name}</h1>
                            <h1>age是: {value.age}</h1>
                        </div>
                    )
                }
            }
        </UserContext.Consumer>
    )
}
```

### 4. 任意组件通信（事件总线event bus）
1. 安装events库

```shell
npm install events
```

2. 创建eventBus对象

```js
const eventBus = new EventEmitter()
```

3. 通过emit发送消息

```js
<button onClick={e => eventBus.emit('sayHello','Hello Home')}>点击向Home组件发送消息</button>
```

4. 通过addListener来监听消息

```js
eventBus.addListener('sayHello',(args) => {
    this.setState({
        message: args
    })
})
```

* [在线CodeSandBox](https://codesandbox.io/s/reactzhong-de-shi-jian-zong-xian-tong-xin-vbbtjq)


## 参数验证
> 使用PropTypes进行参数验证。

```js
import React from 'react'
import PropTypes from 'prop-types'
export default function App() {
    const names = [1,2,3]
    return (
        <div>
            <Cpn name="张三" age={20} names={names} />
        </div>
    )
}

function Cpn(props) {
    const { name, age,names } = props;
    return (
        <div>
            <h1>{name} + {age} + </h1>
            {
                names.map(item => item)
            }
        </div>
    )
}

Cpn.propTypes = {
    names: PropTypes.array,
    age: PropTypes.number.isRequired
}
```

## React实现slot
> 通过props进行传递jsx。

* 父组件

```js
export default class App extends Component {
  render() {
    return (
      <div>
          <NavBar 
              leftSlot={<button>111</button>}
              centerSlot={<a href="/#">222</a>}
              rightSlot={<span>666</span>}
          />   
      </div>
    )
  }
}
```

* 子组件

```js
export default class NavBar extends Component {
    render() {
        const {leftSlot,centerSlot,rightSlot} = this.props;
        return (
            <div className='nav-bar'>
                <div className="left">
                    {leftSlot}
                </div>
                <div className="center">
                    {centerSlot}
                </div>
                <div className="right">
                    {rightSlot}
                </div>
            </div>
        )
    }
}
```

## 性能优化
1. 函数组件：使用memo
2. 类组件：使用pureComponent

## 使用ref操作DOM
> 在React的开发模式中，通常情况下不需要直接操作DOM，但是某些特殊情况，确实需要直接对DOM进行操作，此时就需要用到Ref。

> `注意：下面的几种方法都是在类组件中的`

1. 字符串形式的ref

```js
  <div>
    <div ref='titleRef'>Hello,React</div>
    <button onClick={e => console.log(this.refs.titleRef.innerHTML = 'Hello Ref')}>点击获取标题的DOM元素</button>
  </div>
```

2. 通过createRef

```js
class App extends Component {
  constructor(props) {
    super(props);
    this.titleRef = createRef();
  }
  render() {
    return (
      <div>
        <div ref={this.titleRef}>Hello,React</div>
        <button onClick={e => console.log(this.titleRef.current.innerHTML = '张三')}>点击获取标题的DOM元素</button>
      </div>
    );
  }
}
```

3. 回调函数形式的Ref

```js
class App extends Component {
  constructor(props) {
    super(props);
    this.titleRef = null;
  }
  render() {
    return (
      <div>
        <div ref={arg => this.titleRef = arg}>Hello,React</div>
        <button onClick={e => console.log(this.titleRef.innerHTML = '张三')}>点击获取标题的DOM元素</button>
      </div>
    );
  }
}
```

> `在函数组件中使用ref，可以通过useRef钩子函数`

```js
function App() {
  const titleRef = useRef();
  return (
    <div>
      <div ref={titleRef}>Hello,React</div>
      <button onClick={e => titleRef.current.innerHTML = '张三'}>点击获取标题的DOM元素</button>
    </div>
  );
}
```

## 受控组件和非受控组件
### 受控组件
> 将可变状态保存在组件的state属性中，并且只能通过使用setState来更新，这种组件叫做受控组件。

> 下面是一个受控组件的例子：

```js
function App() {
  const [msg,setMsg] = useState('');
  useEffect(() => {
   console.log(msg); 
  })
  return (
    <div>
      <form onSubmit={e => handleSubmit(e)}>
        <label htmlFor="username">
          用户：<input 
                  type="text" 
                  id="username" 
                  onChange={e => setMsg(e.target.value)} 
                  value={msg}
                />
        </label>
        <input type="submit" value="提交" />
      </form>
    </div>
  );
}
```

### 非受控组件
> 如果要使用非受控组件中的数据，需要使用ref来从DOM节点中获取表单数据。


## 高阶组件
> 高阶组件是一个接收参数为组件，返回值为新组件的函数。`注意：高阶组件是一个函数。`

> 下面是一个高阶组件的实例：

```js
class App extends PureComponent {
  render() {
    return (
      <div>
        App
        {this.props.name}
      </div>
    )
  }
}

function enhanceComponent(WrappedComponent) {
  return class newComponent extends PureComponent {
    render() {
      return <WrappedComponent {...this.props} />
    }
  }
}

const EnhanceComponent = enhanceComponent(App)

export default EnhanceComponent
```

### 高阶组件的应用一：增强props
```js
function Home(props) {
  return (
    <h1>昵称：{props.nickname}  等级: {props.level} 区域：{props.region}</h1>
  )
}

function enhanceProps(Cpn) {
  return props => {
    return <Cpn {...props} region="中国" />
  }
}

const EnhanceHome = enhanceProps(Home)

class App extends PureComponent {
  render() {
    return (
      <div>
        <EnhanceHome nickname="张三" level="99" />
      </div>
    )
  }
}
export default App
```

### 高阶组件的其他应用
> 高阶组件还可以用于登录鉴权、生命周期劫持（这里的生命周期劫持，我们可以理解为计算某个组件的渲染时间）、通过forwardRef高阶函数给函数式组件传递Ref，具体不再赘述。

## portals的使用
> portals存在的意义在于，有时候我们想要一个组件独立于父组件进行渲染，例如这样的一个场景：父组件的显示区域比较小，但是我们想要一个组件显示在屏幕的中间，此时就可以使用portals。

> 下面这个例子是将Modal组件渲染到屏幕的中间。

```js
function Modal(props) {
  return (
    ReactDOM.createPortal(props.children,document.querySelector('#modal'))
  )
}

function Home(props) {
  return (
    <div>
      <h1>Home</h1>
      <Modal>
        <h2>Title</h2>
      </Modal>
    </div>
  )
}

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <Home />
      </div>
    )
  }
}
```

## fragment
> 所谓的fragment就是使用空标签来代替div标签，防止出现不必要的标签。

```js
<>
  <h1>当前求和为：{count}</h1>
  <button onClick={e => setCount(count + 1)}>点我+1</button>
</>
```

> 下面这种写法，可以添加属性，上面的写法则不行。

```js
<Fragment>
  <h1>当前求和为：{count}</h1>
  <button onClick={e => setCount(count + 1)}>点我+1</button>
</Fragment>
```

## React中的CSS
### 内联样式
1. 不会有冲突。
2. 可以动态获取当前state中的动态。

```js
export default function App() {
  const pStyle = {
    color: 'pink'
  }
  return (
    <div>
      <h2 style={{color: 'red'}}>这是标题</h2>
      <p style={pStyle}>这是一段文字</p>
    </div>
  )
}
```

    









