---
title: React 学习01
date: 2022-10-15 16:37:35
tags: React
---

学习链接：

1. ****[Learn React for free](https://scrimba.com/learn/learnreact#)****
2. B站****[尚硅谷*React教程*（2022加更，B站超火*react教程*）](https://www.bilibili.com/video/BV1wy4y1D7JT)**

两个学习链接一个是英文，一个是中文。两个我都在用，对比了一下，国内的教程一般时间很长，但是基本都是干货，知识是直接投喂的，重难点也会讲得很清楚。国外的教程更侧重趣味性，看的过程需要自主思考，边看边操练，讲的过程中是对CSS和JS的整个运用，不仅仅是学习react，除此之外，国外的教学视频一般更短一点，一般是通过做小demo学习框架知识，哦！对了，没有做笔记的过程。

react 17文档：[https://17.reactjs.org/docs/getting-started.html](https://17.reactjs.org/docs/getting-started.html)

react 18文档：[https://reactjs.org/docs/getting-started.html](https://reactjs.org/docs/getting-started.html)

### 组件定义的两种方式

1. 函数：适用于简单组件的定义（简单组件就是没有state响应式）
   
    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

2. 类：适用于有state的组件
   
    class Welcome extends React.Component {
      render() {
   
        return <h1>Hello, {this.props.name}</h1>;
   
      }
    }

### 利用Vite工具工程化

1. `npm create vite@latest`
2. `npm install`

### react脚手架创建react app

    npx create-react-app my-app
    cd my-app
    npm start

### 组件实例的三大核心属性

1. **props**

理解：组件使用是的自定义属性，有点像vue的子组件的props属性

* props属性是只读的，组件内部无法修改props属性值（和Vue一样，父组件到子组件的数据是单向流动的）

* [props类型限制的方式](https://17.reactjs.org/docs/typechecking-with-proptypes.html)
  
    import PropTypes from 'prop-types';
    class Greeting extends React.Component {
  
      render() {
        return (
          <h1>Hello, {this.props.name}</h1>
        );
      }
  
    }
    Greeting.propTypes = {
  
      name: PropTypes.string
  
    };
    Greeting.defaultProps = {
  
      name: "Lucus"
  
    };
  
    // 可以写成静态方法/属性
    class Greeting extends React.Component {
  
        static propTypes = {
          name: PropTypes.string
        };
        static defaultProps = {
          name: "Lucus"
        };
        render() {
          return (
            <h1>Hello, {this.props.name}</h1>
          );
        }
  
    }

* 扩展属性
  
    const person = {
  
          name: 'Jace',
          age: 18,
          sex: "男"
      }
  
    ReactDOM.render(<Person {...person} />, root)
1. **state**

理解：响应式状态，是一个对象，里面的数据是响应式的，有点像Vue的data

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
    
    // 简写形式，不写构造函数
    class Clock extends React.Component {
      state = {date: new Date()}
    
      render() {
        return (
          <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
          </div>
        );
      }
    }

* state中的数据不能直接改变，而是通过setState({})

* 组件中自定义方法中的this为undefined，
  
  解决方式1：bind强制绑定；
  
  解决方式2：箭头函数（写成赋值语句+箭头函数的形式`tick=()⇒{}`）
  
  类里面可以直接写赋值语句，无需关键字声明，相当于给类添加一个属性；并不是所有组件的自定义方法中的this为undefined，要看谁调用，如果是事件处理回调的，那就是Undefined，因为类默认会开启严格模式，这时候This不指向window，而是undefined.
1. **refs**

理解：标签可以用ref属性标识自己，换句话说，可以通过访问refs属性获取节点

使用方法：

* `creatRef`创建：使用`this.myRef.current` 获取
  
    this.myRef = React.createRef();
  
  <div ref={this.myRef} /> // 已有标签可以添加ref
    <CustomTextInput ref={this.textInput} /> // 通过类创建的自定义组件也可以添加ref，但是函数创建的组件不行，因为没有实例

* 回调函数形式
  
    this.inputElement = null
    <input
  
        type="text"
        ref={el => this.inputElement = el}
      />
  
    // 上述的写法为内联形式，在页面更新的时候会调用两次，第一次是null，第二次才是节点el
    // 这是因为react每次render的时候，都要重新创建新的实例，所以react要先清空ref的值，然重新赋值
    // 可以通过绑定函数的形式避免这个问题，但是影响不大
    this.inputElement = null
    setTextInputRef = element => {
  
      this.textInput = element;
  
    };
    <input
  
      type="text"
      ref=setTextInputRef
  
    />

* 字符串形式（不推荐）
  
  ```
  `<input ref="input1"/>
    this.refs.input1获取`
  ```

## 事件处理

`onXxx` 的形式，如`onClick, onChange, onFoucs` 。为了更好的兼容，所以没有直接使用原生JS事件。

通过`event.target` 获取该事件的DOM元素对象，能不使用ref，则不使用。

## 生命周期

17版本以下的声明周期（18版本有些不一样）

![](![](https://github.com/janice143/myblog.github.io/blob/master/images/react_life.png?raw=true))

1. **初始化阶段:** 由ReactDOM.render()触发---初次渲染
   1. constructor()
   2. componentWillMount() // 即将挂载
   3. render()
   4. componentDidMount() // 挂载完毕
2. **更新阶段:** 由组件内部this.setSate()或父组件重新render触发
   1. shouldComponentUpdate() // 返回true或者false控制组件是否更新（是否继续后面的流程）
   2. componentWillUpdate() //组件即将更新
   3. render()
   4. componentDidUpdate() // 组件已经更新
3. **卸载组件:** 由ReactDOM.unmountComponentAtNode()触发
   1. componentWillUnmount()
