### 概念

#### 基本认识

1. Vuex是一个专门为vue.js应用程序开发的状态管理模式,它可以集中储存管理所有组件的状态
2. Vuex的状态储存是响应式的,就是说,当组件从store中读取状态的时候,若store中状态发生改变,对应组件的组件也会同步更新
3. 改变store中的状态的唯一途径就是显式的提交(commit)mutations,方便我们可以跟踪每一个状态的改变
4. Vuex更适合于大型单页应用,简单项目store模式就已经够用

#### 核心模块

- state 设置默认的初始状态,是vuex的唯一数据源
- Getters:允许组件从Store中获取数据,通过辅助函数将getter映射到局部计算属性
- Mutation;唯一可以改变状态的方法,而且里面必须是同步的函数
- Action:用来显式的提交mutation,来改变状态,而不是直接变更状态,可以包含任意异步函数

#### 实现原理

先上图

![1570968120768](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1570968120768.png)

手写一个vuex

- 定义一个install函数,Vuex是一个插件,当Vue.use(Vuex)是就会触发这个install这个函数
- 利用mixin这个方法,将store对象,混入到所有创建的vue实例中
- 利用beforeEach分别遍历store类中的getters ,mutations,以及actions  定义每一个挂载项的方法
- 同时在store这个类,new上一个Vue,来时state中的数据变为响应式

代码

```js
import { types } from "util"
let Vue
const forEach = (obj, callback) => {
    Object.keys(obj).forEach(key => {
        callback(key, obj[key])
    })
}
class Store {
    constructor(options) {
        //在vuex中状态变了,视图也会改变 所以在这里要New一个Vue
        //把组状态变成响应式
        this._s = new Vue({
            data: {
                state: options.state
            }
        })
        //this._s=options.state
        //getters 有可能不写  
        let getters = options.getters || {}
        this.getters = {};
        forEach(getters, (getterName, value) => {
            Object.defineProperty(this.getters, getterName, {
                //当你要获取getterName 会自动调用get方法
                get: () => {
                    return  value(this.state)
                }
            })
        })
        //实现mutations
        let mutations = options.mutations || {}
        this.mutations = {};
        // Object.keys(mutations).forEach(mutationName => {
        //     this.mutations[mutationName] = (payload) => {
        //         mutations[mutationName](this.state, payload)
        //     }
        // })
        forEach(mutations,(mutationName,value)=>{
            this.mutations[mutationName]=(payload)=>{
                value(this.state,payload)
            }
        })
        //actions 原理
        let actions =options.actions || {}
        this.actions={};
        forEach(actions,(actionName,value)=>{
            this.actions[actionName]=(payload)=>{
                value(this,payload)
            }
        })
    }
    //actions 中的dispatch方法
     dispatch=(type,payload)=>{
         this.actions[type](payload)
     }
    commit=(type, payload)=> {
        this.mutations[type](payload)
    }
    get state() {
        return this._s;
    }

}
const install = _vue => {
    Vue = _vue
    Vue.mixin({
        beforeCreate() {
            //console.log(this.$options.name)
            //保证组所有的组件都有store对象
            if (this.$options && this.$options.store) {
                this.$store = this.$options.store
            } else {
                this.$store = this.$parent && this.$parent.$store
            }
        }
    })
}1

export default {
    install,
    Store
}
```

