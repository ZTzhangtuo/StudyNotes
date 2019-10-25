## Context(上下文)

### 使用场景

当你不想在组件树中通过逐层传递prop或者state的方式来传递数据时,可以使用Context,来实跨级的组件数据传递

### 核心概念

#### Provider(生产者)

通常是一个父节点,

#### Consumer(消费者)

通常是一个或者多个子节点,用于函数组件

### 使用

使用react提供的api创建一个上下文对象,

```jsx

import React from 'react'
import ReactDOM from 'react-dom'
let colorContext=React.createContext()//创建一个context对象
class Wrapper extends React.Component{
    state={
     color:"red"
    }
    changeColor=(color)=>{
        this.setState({color})
    }
    //顶层组件
    render(){
        let contextValue={color:this.state.color,changeColor:this.changeColor}
        return(
            //使用生产者,给根组件的子组件提供数据
            <colorContext.Provider value={contextValue}>
            <div style={{border:`8px solid ${this.state.color}`,padding:"10px"}}>
                <h1>wrapper</h1>
                <Main></Main>
                <Header></Header>
            </div>
            </colorContext.Provider>

        )
    }
}
====================================================
 class Main extends React.Component{
     //要想在子组件中使用这个上下文对象
     //需要给这个类加上一个静态属性 static contextType 值就是顶层定义的那个上下文对象
    static contextType=colorContext;
    render(){
        return(
         <div style={{border:`8px solid ${this.context.color}`,padding:"10px"}} >
               <h1>main</h1>
              <Title></Title>
            </div>
         )
    }
 }
 ===============================================================
//在函数组件中使用上下文
function Main (){
    return(
        //在函数组件使用消费者组件
        <colorContext.Consumer>
        {
        value=>(
            <div style={{border:`8px solid ${value.color}`,padding:"10px"}}>
            <h1>main</h1>
            <Title></Title>
            </div>
        )
        }
        </colorContext.Consumer>
    )
}
class Header extends React.Component{
    static contextType=colorContext;
    render(){
        return(
            <div  style={{border:`8px solid ${this.context.color}`,padding:"10px"}}>
                <h1>header</h1>
                <Content></Content>
            </div>
        )
    }
}
class Title extends React.Component{
    static contextType=colorContext;
    render(){
        return(
            <div  style={{border:`8px solid ${this.context.color}`,padding:"10px"}}>
                <h1>tltle</h1>
            </div>
        )
    }
}
class Content extends React.Component{
    static contextType=colorContext;
    render(){
        return(
            <div  style={{border:`8px solid ${this.context.color}`,padding:"10px"}}>
                <h1>content</h1>
                <button onClick={()=>this.context.changeColor("blue")}>变蓝</button>
                <button onClick={()=>this.context.changeColor("green")}>变绿</button>
            </div>
        )
    }
}
ReactDOM.render(<Wrapper></Wrapper>,document.getElementById("app"))
```

效果

![1571920154020](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1571920154020.png)

