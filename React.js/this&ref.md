```jsx
import React from 'react'
import ReactDOM from 'react-dom'
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.num1=React.createRef();
        this.num2=React.createRef();
        this.res=React.createRef();
    }
    state={number:99}
    handleClick=(event)=>{
       // this.setState({number:this.state.number+1})
        // this.setState({number:this.state.number+2})
        // this.setState({number:this.state.number+3})
        //结果只执行了加3 说明setstate更新状态可能是异步,内部会把多次更新合并成一次更新
        //要想实现每一次setstate是在上一次的基础上更新
        //第一种写法 在setState中写一个函数，函数的第1个参数代表上次的状态
        // this.setState((prevState)=>({number:prevState.number+1}))
        // this.setState((prevState)=>({number:prevState.number+100}))
         //第二种写法 嵌套xiefa 回调函数
         this.setState({number:this.state.number+1},()=>{
             this.setState({number:this.state.number+100},()=>{
                 this.setState({number:this.state.number+1000})
             })
         })
    }
    handleSubmit=(event)=>{
        event.preventDefault()
        console.log(this)//这个this就是指向组件
         //console.log(this.refs.num1.value) 
         //console.log(this.num2.value) 
         console.log(this.num1.current.value)
    }
    // handleSubmit(event){
    //     //阻止默认事件
    //     event.preventDefault()
    //     console.log(this)
    // }
    //ref 可以利用ref获取真实的dom元素
    //用法一 ,直接使用ref起xxx名字,利用this.refs.xxx来获取
    //用法二,利用ref函数,获取dom元素
    //用法三利用React.createRef() 给当前组件挂num1,num2
  //ref={this.num1}
  //获取值 this.num1.current.value
    render() {
        return (
            <div>
                <p>{this.state.number}</p>
                <button onClick={this.handleClick}>+</button>
                {/* 手动绑定this */}
                {/* <form action="" onSubmit={this.handleSubmit.bind(this)}> */}
                {/* 使用箭头函数 里面没有this */}
                {/* <form action="" onSubmit={(event)=>{this.handleSubmit(event)}}> */}
                {/* 外部的函数是箭头函数,这里可以直接调用 */}
                <form action="" onSubmit={this.handleSubmit}>
                    用户:<input ref={this.num1}></input>
                    <br/>
                    密码:<input ref={this.num2}></input>
                    其他:<input ref={this.res}></input>
                    <input type="submit" ref={input=>this.num2=input}/>
                </form>
                
            </div>
        )
    }
}
ReactDOM.render(<Counter></Counter>,window.app)
```

