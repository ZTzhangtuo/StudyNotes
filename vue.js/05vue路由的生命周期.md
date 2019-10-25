### 路由的生命周期

![QQ图片20190814104625](D:\Front-end-files\代码仓库\QQ图片20190814104625.png)

#### 路由钩子

路由钩子主要是给使用者在路由发生变化时进行一些特殊的处理而定义的。

主要分为三大类

- 全局钩子
- 某个路由独享的钩子
- 组件内的钩子

三个参数

- to 即将要进入的目标，路由对象
- from 当前导航正要离开的路由
- next：Function
  - next （）进入该路由
  - next（false）取消进入路由，url地址重置为from路由地址(也就是将要离开的路由地址)。
  - next 跳转新路由，当前的导航被中断，重新开始一个新的导航。

#### 全局守卫（全局钩子）

- beforeEach全局前置路由进入路由之前
- beforeResolve全局看解析守卫,在beforRouteEnter调用之后调用
- afterEach全局后置钩子进入路由之后

```js
//使用router.beforeEach注册一个全局的前置守卫
//main.js入口文件
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
 // ...
 next()
})
//注意:使用全局路由钩子, 一定要调用next()!!!
```

#### 路由内的钩子

你可以在路由配置上直接定义beforeEnter守卫

```js
const router = new VueRouter({
 routes: [
  {
   path: '/foo',
   component: Foo,
   beforeEnter: (to, from, next) => {
    // ...
   }
  }
 ]
})
```

#### 组件内的钩子

最后, 你可以在路由组件中直接定义一下路由导航守卫:

1. beforeRouteEnter
2. beforeRouteUpdate (2.2 新增)
3. beforeRouteLeave

```

```

路由的生命周期：
    入口main.js中还有两个：beforeEach  beforeResolve
    离开一个组件时：  beforeRouteLeave 
    进入到一个新的页面：beforeEach
    router配置文件中也有路由钩子：beforeEnter
    进入新的组件：beforeRouteEnter 
    解析完成：beforeResolve 
    进入完毕： afterEach
    路径发生改变：beforeRouteUpdate 

```vue

<li><router-link to="/user/detail?id=1">用户1</router-link></li>
使用？号的形式传参：{{this.$route.query.id}}
        {
          path:"detail",
          name:'userDetail',
          component: () => import("./views/UserDetail")
        }
-----------------------------------------------------------------------------------------
<li><router-link to="/user/detail/2">用户2</router-link></li>
<li><router-link to="/user/detail/3">用户3</router-link></li>
        {
          path:"detail/:id",
          name:'userDetail',
          component: () => import("./views/UserDetail")
        }
 使用路径的形式传参：{{this.$route.params.id}}号的详情
```

