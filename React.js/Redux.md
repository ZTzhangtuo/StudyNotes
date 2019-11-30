## Redux的认识以及实现原理

### 核心概念

#### 什么是redux

Redux 是 JavaScript 状态容器(独立状态管理库)，提供**可预测化的状态管理**。存在的动机就是让state的变化变得可预测,同时便于集中管理

本身是一种架构模式,它不关注你到底用什么库，你可以把它应用到React 和 Vue，甚至跟 jQuery 结合都没有问题。

#### 三大原则

1. 单一数据源, 整个应用的state被储存在一棵object tree中，并且这个 objecttree只存在于唯一一个store中。
2. state 是只读的,唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。
3. 使用纯函数来执行修改,为了描述 action 如何改变 state tree ，你需要编写 reducers。Reducer只是一些纯函数，它接收先前的state 和 action，并返回新的 state。

### 基本使用

Redux只是提供了规则和api，store的创建需要程序员来完成，创建store之后，才可以使用之。

![1571899435626](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571899435626.png)

#### state

state对应应用的状态,,就是一个普通的对象,Redux应用中,所有的状态都保存在单一的对象中

#### action

表示要执行的动作,本质是js对象,里面必须把包含一个用来描述动作的type属性

```js
//计数器案例+ -的action
export  increment={
        type:"INCREMENT",
}
export  decrement={
        type:"DECREMENT",
}
```

#### reducer

三大原则中的第三条,reducer的职责是根据不同的action来完成state的变化

它是有个纯函数,接收旧的state与action ,返回新的state与action

```js
function counter(state=0,action){
    switch(action.type){
       case "INCREMENT":
              return state+1;
        case "DECREMENT":
              return state-1;
         default :
            return state
    }
}
```

注意

1. state参数，表示旧的状态，可以赋默认值，也可以直接写。

   2  针对return，必须要返回一个和初始状态具备相同结构的对象，不要直接修改state。

  3  必须要写上default，并且在default的时候，就直接返回state本身。

#### store

当我们定义好了reducer之后，就可以调用Redux对象的createStore方法来创建store对象了。

```js
//store/index.js
import reducer from  "..."//引入reducer
import { createStore } from "redux"//引入Redux对象的createStore方法
//初始化状态
const state={
    ...
}
const store=Redux.createStore(reducer,[initstate])//reducer是比必须传的
```

![1571899520673](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571899520673.png)

ü  getState：用于获取状态

ü  dispatch：用于派发（触发）某个action ,执行这个api时,就根据action的type值,调用相应的reducer

ü  subscribe：订阅，一旦状态发生变化，就会执行回调函数

#### action creator

计数器案例中,每次加,减的值都是固定的,开发中的需求一般不会是这样,更多的时候需要动态的传参,这个时候就需要同步action创建函数了

```js
//action
export function increment(flag){
    return{
        type:"INCREMENT",
        flag
    }
}
//reduser
function counter(state=0,action){
    switch(action.type){
       case "INCREMENT":
              return state+action.flag;
        case "DECREMENT":
              return state-action.flag;;
         default :
            return state
    }
}
//调用
store.dispatch(increment(999))
```

注意

- action 和 action creator是两个概念,前者是一个普通对象,后者是函数
- 除了type 属性，action 对象的结构完全由你自己决定。

### react-redux

![1571842804244](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571842804244.png)

将React和Redux连接起来，需要使用react-redux，这是redux作者为react封装的专用库

react.js  react-redux  redux 三者是完全独立的库

![1571842868257](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571842868257.png)

react-redux提出了**“智能”**组件和**“笨拙”**组件相分离的开发思想。

![1571842954893](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571842954893.png)

通常情况下，智能组件就是指我们平常所说的页面。笨拙组件就是页面中某个部分（通常是通用的部分）。

#### provider组件

Provider组件的作用，就是给后代组件提供store对z象。通常需要放到入口的js文件中。

```js
import React from 'react'
import ReactDOM from 'react-dom'
import {Provider} from "react-redux"
import store from './store'
import App from "./App"
ReactDOM.render(
<Provider store={store}>
    <App></App>
    </Provider>
,document.getElementById("app"));
```

#### connect

```js
//将redux中的状态和dispatch方法映射到组件上
import React, { Component } from 'react'
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import * as actions from '../actions/counter'
class Counter extents Component{
    render{
        return{
            
        }
    }
} 
fcuntion mapStateToProps(state){
    return {
        counter:state.counter
    }
}
//bindActionCreators 将一个action creator 与dispatch绑到一起 简化代码
//把dispatch方法也映射到counter组件上
function mapDispatchToProps(dispatch){
      return bindActionCreators(actions,dispatch)
}
export default connect(mapStateToProps,mapDispatchToProps)(Counter)
```

