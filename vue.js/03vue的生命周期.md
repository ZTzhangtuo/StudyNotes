### 如何解释清楚vue的生命周期

谈到vue的生命周期，脑子首先就要想到8个钩子函数，他们分别是：

```JS
beforeCreate,created;
beforeMount,mounted;
beforeUpdate,updated;
beforeDestroy,destroyed;
过程：创建=>挂载=>更新=>销毁
```

那什么又是钩子函数呢

钩子函数，其实和回调是一个概念，当系统执行到某处时，检查是否有hook，有则回调。说的更直白一点，每个组件都有属性，方法和事件。所有的生命周期都归于事件，在某个时刻自动执行。

但是只知道这些是完全不够的。。。先上一张官网原图，懵逼都会是暂时的。。。

![img](https://user-gold-cdn.xitu.io/2018/4/14/162c087d49e9be5d?imageslim)

一切要先从创建一个Vue实例开始说起

```js
<script>
        let vm = new Vue({
            el:"#app",
            data:{
                num:1,
            },
            beforeCreate(){
  // 拿不到数据，初始化生命周期，在init（初始化）的过程中首先调用了beforeCreate，并且绑定自己的事件，然后在injections（注射）和reactivity（反应性）的时候，它才会去调用created。所以不能在beforeCreate的时候修改data中的数据
                //console.log(this.num) 拿不到数据
            },
            created(){
                console.log(this.num)
            },
//当created完成之后，他会先去判断实例中是否含有"el"选项，如果没有则会调用vm.$mount(el)方法，执行下一步
//接着判断是否含有"template"挂载项，有的话，把它解析成一个render function （template的编译过程）
            beforeMount(){
 //beforeMount在有了render function的时候才会执行，此时还没有将编译完的模板渲染到页面上,执行完render function后,创建vm.$el并用它来替换el,之后就会调用mounted这个钩子函数，mounted挂载完毕后，实例就算走完流程了
            mounted(){
                // 发起ajax 获取真实dom
            },
=========================================================================================
//以下的钩子函数的执行，都需要外部的触发才会执行，有数据的变化时，会调用beforeUpdate,然后经过虚拟DOM（Virtual DOM）,最后 updated更新完毕，当组件被销毁的时候，它会调用beforeDestory，以及destoryed。
            beforeUpdate(){
        },
            updated(){
                // 不能在updated中操作数据
            },
            beforeDestroy(){
                // 销毁之前做一些清理工作，比如细一些异步函数
            },
            destoryed(){
            },
//另外在这个过程中，Vue为我们提供了renderError的方法，这个方法只有在开发的时候它才会被调用，在正式打包上线的过程当中，它是不会被调用的。它主要是帮助我们调试render里面的一些错误。有且只有当render方法里面报错了，才会执行renderError方法。
        })
       </script>
```

