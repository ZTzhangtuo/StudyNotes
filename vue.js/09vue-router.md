### 深入理解Vue-Router的实现方式及原理

#### 路由的三种模式

- hash

    1.URL的hash只是客户端的一种状态基于location.hash 向服务端发送请求时,hash部分不会发送
              2. hash值的改变,会在浏览器留下访问历史,因此可以通过浏览器的前进后退改变hash值
              3. a便签的href属性或者通过js对loaction.hash进行赋值,改变hash
              4.  通过事件hashchange来监听hash的变化,从而对页面进行调转

- history

  1.HTML5提供的History api来实现URL的变化 history.pushState()与history.replaceState()
   这两个api可以在不进行刷新的情况下,操作浏览器的历史记录,这两个api不会触发popstate            事件需要手动触发页面调转
   2.通过事件popstate来监听url的变化,从而对页面进行调转(渲染)

- abstrict

  支持所有的JavaScript运行环境,比如node.js服务端,如果发现没有浏览器的api 路由会自动强制进入这个模式

路由的原理
vue-router     

两种模式hash history

- hash  浏览器自带的方法location.hash  两个事件 load hashchange 
- history 浏览器自带的方法history.pushstate  两个事件 load popstate

mixin 给所有组件混入router对象 

router-link router-view

全局组件 ,本质上是利用a标签与jsx的render函数,实现路由跳转,并且将path对应得内容渲染出来