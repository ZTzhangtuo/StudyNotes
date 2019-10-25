### 利用路由元信息(meta)和jwt实现路由权限验证

在实际项目中,一些页面需要登录之后才可以访问,这就需要我们对路由跳转实现权限验证

在配置路由时,可以给路由添加元信息(meta)

```js
{
        path:"/index",
        name:"index",
        component:Index,
        //权限路由 访问这个路径需要登录,我们可以将这个状态保存到vuex中,以方便更改
        meta:{needLogin:true}
      },
      {
        path:"/about",
        name:"about",
        component:About,
      },
  //然后通过router.beforEach 这个路由钩子函数,它在每次切换页面时执行
   //路由守卫
 router.beforeEach((to,from,next)=>{
     //访问路由元信息,获得哪些路由需要在登录的条件下才能访问
     let needLogin=to.matched.some(match=>{match.meta.needLogin})//true
     if(needLogin){
      //在此之前要先判断是否已经登录
         if(isLogin){
             //isLogin 的状态根据jwt验证token是否过期得到
             next()//已经登录直接行
         }else{
             next("/index")//重定向到首页
         }
     }else{
         //不需要登录就可以访问
         next()
     }
 })         
  
```

