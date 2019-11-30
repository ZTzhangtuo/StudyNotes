### Vue异步更新以及nextTick

问题

- 为什么Vue.js要异步更新视图?
- JavaScript的异步运行机制是怎么样的?
- 什么情况下要使用nextTick

#### 异步更新

**Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要显然**。

#### 事件轮询

Vue.js在修改数据的时候，不会立马修改数据，而是要等同一事件轮询的数据都更新完之后，再统一进行视图更新

```js
vm.message = 'changed'

//想要立即使用更新后的DOM。这样不行，因为设置message后DOM还没有更新
console.log(vm.$el.textContent) // 并不会得到'changed'

//这样可以，nextTick里面的代码会在DOM更新后执行
Vue.nextTick(function(){
    console.log(vm.$el.textContent) //可以得到'changed'
})


```

![img](https://user-gold-cdn.xitu.io/2018/8/29/165821ca4d06f6c1?imageslim)

#### nextTick

**在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。**

#### 应用场景

在需要视图更新之后,基于新的视图进行操作

```js

<body>
    <div id="app">
        <input ref="input" v-show="inputShow">
        <button @click="show">show</button>
    </div>
    <script>
        new Vue({
            el: "#app",
            data() {
                return {
                    inputShow: false
                }
            },
            //点击按钮显示input框后,直接获得输入框的焦点
            methods: {
                show() {
                    this.inputShow = true
                    this.$nextTick(() => {
                        this.$refs.input.focus()
                    })
                }
            }
        })
    </script>
</body>

```

vue通常鼓励开发人员沿着“数据驱动”的方式思考，避免直接接触 DOM。Vue的dom更新是异步的，当数据发生变化，vue并不是里面去更新dom，而是开启一个队列。跟JavaScript原生的同步任务和异步任务相同。

比如我们调用一个方法，同时涉及多个数据的操作改变，vue会把这一些列操作推入到一个队列中，相当于JavaScript的同步任务，在执行过程中可能会出现一些产生任务队列的异步任务，比如定时器、回调等。

在vue里面任务队列也叫事件循环队列。我们都知道JavaScript是循环往复的执行任务队列。Vue也一样，在一个同步任务过程中是不会去更新渲染视图，而是在同步任务（事件循环队列）执行完毕之后，在主线程的同步执行完毕，读取任务队列时更新视图。

这个机制对于页面性能是非常重要的，试想一下，我们要是每操作一个数据就更新一次视图，那么在性能上会非常差。而如果是在一次任务执行完毕之后更新视图，可以避免特别多的重复操作。

在开发过程中，我们很容易遇见需要先渲染数据然后操作dom，这时候就要使用vue提供的nextTick函数。

对于这个函数有这样两句话：

Vue.nextTick(callback)，当数据发生变化，更新后执行回调。
 Vue.$nextTick(callback)，当dom发生变化，更新后执行的回调。

我测试了很多例子，最后还是只实现了dom的变化，对于第一句没有实现：