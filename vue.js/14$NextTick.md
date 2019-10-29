### Vue异步更新以及nextTick

问题

- 为什么Vue.js要异步更新视图?
- JavaScript的异步运行机制是怎么样的?
- 什么情况下要使用nextTick

#### 异步更新

**Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要显然**。

#### 事件轮询

Vue.js在修改数据的时候，不会立马修改数据，而是要等同一事件轮询的数据都更新完之后，再统一进行视图更新

```vue.js
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

