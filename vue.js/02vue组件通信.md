###  vue中组件通信的八种方式

#### 一、props & $emit

#### 1.父传子

简单来讲就是：父组件中有数据，传递给子组件。步骤如下

- 首先确定父组件中有数据
- 在父组件的模板中通过属性绑定把数据绑到子组件上
- 在子组件中定义props属性，用来接收父传递过来的基础
- 在子组件的模板就可以使用接收过来的数据了

```vue
<body>
    <div id="app">
        我是父组件中的数据{{num}}
        <son :sonvalue="num"></son>
        //属性绑定
    </div>
    <template id="son">
        <div>
            <h2>我是子组件,我想用父组件中的数据{{sonvalue}}</h2>
            //这里就可以拿到父子组件的数据了
        </div>
    </template>
</body>
 <script>
    let Son ={
        template:"#son",
        data(){
             return{
             }
        },
        //props属性用来接收数据
        props:{
            sonvalue:Number,
        }
    }
    let app = new Vue({
         el:"#app",
         data:{
             num:100,
         },
         components:{
             Son
         }
    })
</script>
```

总结: prop 只可以从上一级组件传递到下一级组件（父子组件），即所谓的单向数据流。而且 prop 只读，不可被修改，所有修改都会失效并警告。

#### 2.子传父

```vue
<body>
    <div id="app">
        <h1>父组件</h1>
        <!-- 给子组件添加一个事件监听 -->
        <!-- submitmsg是一个事件 -->
        <!-- 子组件会发射一个事件，事件发生时会触发addmsg函数 -->
        <!-- addmsg是父组件的函数 -->
        <son @submitmsg="addmsg"></son>
        <h4>从子组件中传过来的数据{{a}}</h4>
    </div>
    <template id="son">
        <div>
            <h3>子组件</h3>
            <button @click="fashe">发射</button>
        </div>
    </template>
</body>
<script>
    let Son = {
        template: "#son",
        data() {
            return {
            }
        },
        methods: {
            fashe() {
                this.$emit("submitmsg",66666)
            }
        }
    }
    let vm = new Vue({
        el: "#app",
        data: {
            a:0},
        components: {
            Son
        },
        methods: {
            addmsg(info) {
                // console.log(info)
                this.a = info            }
        }
    })
</script>
```

对于`$emit` 我自己的理解是这样的: `$emit`绑定一个自定义事件, 当这个语句被执行时, 就会将参数arg传递给父组件,父组件通过v-on监听并接收参数。 通过一个例子，说明子组件如何向父组件传递数据。

#### 二、$parent & $children

```vue
<body>
    <div id="app">
        我是父组件中的数据{{num}}
        <son1></son1>
        <son2></son2>
        <button @click="changeSon1">点击改变子组件1的值</button>
    </div>
    <template id="son1">
        <div>
            <h2>我是子组件1:{{message1}}</h2>
            <p>获取父组件的值为:{{parentVal}}</p>
        </div>
    </template>
    <template id="son2">
        <div>
            <h2>我是子组件2</h2>
        </div>
    </template>
</body>
<script>
    let Son1 = {
        template: "#son1",
        data(){
            return{
                message1:'im son1'
            }
        },
        props:{},
        computed:{
           parentVal(){
            //    获取父组件中的数据
               return this.$parent.num
           }
        }
    }
    let Son2 = {
        template: "#son2"
    }
    let vm = new Vue({
        el: "#app",
        data: {
           num:'100'
        },
        components: {
            Son1,
            Son2
        },
        methods:{
            changeSon1(){
                //获取到子组件son1中的数据
                this.$children[0].message1='this is new value'
            }
        }
    })
</script>

```

总结：以上两种用于父子组件的通信，使用props进行父子组件通信的比较普遍，二者都不可以用于非父子间组件的通信

#### 三、provide & inject

`provide`/ `inject` 是`vue2.2.0`新增的api, 简单来说就是父组件中通过`provide`来提供变量, 然后再子组件中通过`inject`来注入变量。

`注意: 这里不论子组件嵌套有多深, 只要调用了`inject` 那么就可以注入`provide`中的数据，而不局限于只能从当前父组件的props属性中回去数据`

```vue
//举个例子，假设有三个组件

<body>
    <div id="app">
        我是父组件中的数据{{num}}
        <son1></son1>
    </div>
    <template id="son1">
        <div>
            <h2>我是子组件1:{{message1}}</h2>
            <p>{{demo}}</p>
            <son2></son2>
        </div>
    </template>
    <template id="son2">
        <div>
            <span>我现在是孙组件2</span>
            <p>{{name}}</p>
        </div>
    </template>
</body>
<script>
    // 孙组件
    let Son2 = {
        template: "#son2",
        inject: ['for'],
        data() {
            return {
                name: this.for
            }
        }
    }
    //子组件，
    let Son1 = {
        template: "#son1",
        inject: ['for'],
        data() {
            return {
                message1: 'im son1',
                demo: this.for
            }
        },
        props: {},
        components: {
            Son2
        }
    }
    // 根组件
    let vm = new Vue({
        el: "#app",
        provide: {
            for: "这是上级组件提供的变量"
        },
        data: {
            num: '100'
        },
        components: {
            Son1,
        },
    })
</script>
</html>
```

#### 四、ref & refs

```vue
<body>
    <div id="app" >
        我是父组件中的数据{{num}}
        //ref用在子组件上
        <son1 ref="son1"> </son1>
    </div>
    <template id="son1">
        <div>
        </div>
    </template>
</body>
<script>
    let Son1 = {
        template: "#son1",
        data(){
            return{
                message1:'im son1'
            }
        },
        methods:{
            sayHello(){
                alert("hello world")
            }
        }
    }
    let vm = new Vue({
        el: "#app",
        data: {
           num:'100'
        },
        mounted(){
            //通过$refs来获取子组件的属性以及方法
              const son1=this.$refs.son1;
              console.log(son1.message1);
              son1.sayHello();
        },
        components: {
            Son1,
        },
    })
</script>
</html>
```

总结:`ref`：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例，可以通过实例直接调用组件的方法或访问数据

#### 五、eventBus

eventBus有称为事件总线，在vue中可以使用它来作为沟通桥梁的概念, 就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件， 所有组件都可以通知其他组件。

`注意:eventBus也有不方便之处, 当项目较大,就容易造成难以维护的灾难`

```vue
<body>
    <div id="app" >
        我是父组件中的数据{{num}}
        <son1> </son1>
        <button @click="additionHandle">点一下</button>
    </div>
    <template id="son1">
        <div>
            <p>我是子组件{{count}}</p>
        </div>
    </template>
</body>
<script>
        //创建一个事件总线
    const EventBus=new Vue(
)
    let Son1 = {
        template: "#son1",
        data(){
            return{
                count:0
            }
        },
        mounted(){
            // 在组件中使用$on接收事件，保持数据在各组件之间的通讯
            EventBus.$on('addition',param=>{
                this.count=this.count+param.num
            })
        }
        }
    let vm = new Vue({
        el: "#app",
        data: {
           num:'100'
        },
        methods:{
            // 通过$emit 发送事件
            additionHandle(){
                EventBus.$emit('addition',{num:this.num})
            }
        },
        components: {
            Son1,
        },
    })
</script>
</html>
//移除事件监听
EventBus.$off('decreased',{})
你也可以使用 EventBus.$off(‘decreased’) 来移除应用内所有对此事件的监听。或者直接调用EventBus.$off() 来移除所有事件频道， 注意不需要添加任何参数 。
```

#### 六、localStorage/sessionStorage

这种通信比较简单,缺点是数据和状态比较混乱,不太容易维护。
通过`window.localStorage.getItem(key)`获取数据
通过`window.localStorage.setItem(key,value)`存储数据

//例子，页面登录时，用来保存token

注意用`JSON.parse()` / `JSON.stringify()` 做数据格式转换
`localStorage` / `sessionStorage`可以结合`vuex`, 实现数据的持久保存,同时使用vuex解决数据和状态混乱问题

#### 七、$attrs & $listeners

```vue
<body>
    <div id="app">
        我是父组件中的数据
        <son1 :name="name" 
             :age="age"
             :gender="gender" 
             :height="height">
        </son1>
    </div>
    <template id="son1">
        <div>
            <h2 >我是子组件 来自根组件的数据{{$attrs.height}}</h2>
            <son2 v-bind="$attrs">
            </son2>
        </div>
    </template>
    <template id="son2">
        <div>
            <span>我现在是孙组件</span>
            <p>{{$attrs.height}}</p>
        </div>
    </template>
</body>
<script>
    // 孙组件
    let Son2 = {
        template: "#son2",
        props:{
            age:Number
        }
        created(){
            console.log(this.$attrs)
            //{ gender: "男", height: "180"}
        },
    }
    //子组件，
    let Son1 = {
        template: "#son1",
        inheritAttrs: false,
        props: {
            name:String
        },
        created(){
            console.log(this.$attrs)
                  //{age: "18", gender: "男", height: "180"}
        },
        components: {
            Son2
        }
    }
    // 根组件
    let vm = new Vue({
        el: "#app",
        data: {
        name:"tom",
        age:"18",
        gender:"男",
        height:"180",
        },
        components: {
            Son1,
        },
    })
</script>
</html>
```

总结:

当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。简单来说：`$attrs` 与 `$listeners` 是两个「对象」，`$attrs` 里存放的是父组件中绑定的非 Props 属性，`$listeners`里存放的是父组件中绑定的非原生事件。

#### 八、vuex

##### vuex的各个模块

1. `state`：用于数据的存储，是store中的唯一数据源
2. `getters`：如vue中的计算属性一样，基于state数据的二次包装，常用于数据的筛选和多个数据的相关性计算
3. `mutations`：类似函数，改变state数据的唯一途径，且不能用于处理异步事件
4. `actions`：类似于`mutation`，用于提交`mutation`来改变状态，而不直接变更状态，可以包含任意异步操作
5. `modules`：类似于命名空间，用于项目中将各个模块的状态分开定义和操作，便于维护

![1566039560741](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1566039560741.png)

```js
  <template>
  <div id="childA">
    <p>来自仓库的数据{{name}}</p>
    <span>{{count}}</span>
    <P>我是子组件A,这是来自vuex中B组件的数据{{msg}}</P>
    <button @click="transfrom">点我让B组件接收我的数据</button>
    <button @click="addcount">点击加1</button>
    <button @click="addAsync">异步加1</button>
  </div>
</template>
<script>
        //...扩展运算符映射
import {mapState,mapActions,mapMutations} from "vuex"
export default {
  data() {
    return {
      Amessage: "hello,im A ",
    };
  },
  methods:{
    ...mapActions(["addAsync"]),
    ...mapMutations(["addcount"]),
    transfrom() {
        //把A组件的数据传过去
      this.$store.commit("receiveAmsg", this.Amessage)
    //   调用vuex中的方法
    // this.$store.commit("addcount")
    },
  },
  computed: {
      ...mapState(["name","count"]),
      //手动将muations中的数据映射过来
    msg() {
      return this.$store.state.Bmsg;
    },
    // counter(){
    //    return this.$store.state.count;
    // }
  }
};
</script> 

```

```js
<template>
  <div id="childB">
    <P>我是子组件B 这是vuex中的来自A组件的数据{{msg}}</P>
    <button @click="transfrom">点我让A组件接收我的数据</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      Bmessage: "hello,im B"
    };
  },
  computed: {
    msg() {
      return this.$store.state.Amsg;
    }
  },
  methods: {
    transfrom() {
      this.$store.commit("receiveBmsg", {
        Bmsg: this.Bmessage
      });
    }
  }
};
</script>
```



```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
export default new Vuex.Store({
  state: {
    Amsg:'',
    Bmsg:'',
    count:0,
    name:"xaioming "
  },
  mutations: {
    receiveAmsg(state,msg){
     state.Amsg=msg
     }, 
    receiveBmsg(state,payload){
      state.Bmsg=payload.Bmsg  
     },
     addcount(state){
       this.state.count++
     }
  },
    //处理异步 ,本质是显示的提交mutations
  actions: {
    addAsync(context){
      setTimeout(function(){
        context.commit('addcount')
      },3000) 
    },
  }
})

```



#### 九、总结

这八种组件通信使用的场景可分为三类

- 父子组件通信：props；$parent/$children;provide/inject;ref;  $attrs/$listeners
- 兄弟组件通信：eventBus ；vuex
- 跨级通信：eventBus ；vuex；provide` / `inject` 、`$attrs` / `$listeners

