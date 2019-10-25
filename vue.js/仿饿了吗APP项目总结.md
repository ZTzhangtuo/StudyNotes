### 仿饿了吗APP项目总结

#### 一、项目基本配置（使用Vue.CLI脚手架进行开发）

- UI库的使用

  ```js
  //配置时 npm i -- 进行下载
  //在main.js文件中引入
  //mintui
  import MintUI from 'mint-ui';
  import 'mint-ui/lib/style.css';
  Vue.use(MintUI);
  // 使用elementui
  import ElementUI from "element-ui"
  import "element-ui/lib/theme-chalk/index.css"
  Vue.use(ElementUI)
  ====================================
   //在使用相关组件时引入
   import { Swipe, SwipeItem,loadmore } from 'mint-ui';
  
  ```

#### 二、组件的封装

![1565178199227](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1565178199227.png)

比如上面这个登录界面，明显input输入框的样式一样，我们就可以封装一个通用的组件

```JS
//在脚手架的componens文件夹里封装通用组件
<template>
  <div class="text_group">
    <!-- 组件结构 -->
    <!-- 组件容器 -->
    <div class="input_group" :class="{'is-invalid':error}">
      <!-- 输入框 -->
      <input
        :type="type"
        :value="value"
        :placeholder="placeholder"
//输入内容后，失去焦点时，自动触发这个事件传递数据给父组件 
//$emit('input',$event.target.value) 第一个参数是事件对象,$event.target事件里面的信息
        @input="$emit('input',$event.target.value)"
      />
      <!-- 输入框后面的按钮 -->
      <button v-if="btnTitle" :disabled="disabled" @click="$emit('btnClick')">{{btnTitle}}</button>
    </div>
    <!-- 错误提醒 -->
    <div v-if="error" class="invalid-feedback">{{error}}</div>
  </div>
</template>
<script>
export default {
  name: "inputGroup",
  //props用来接收组件使用处传来的数据
  props: {
    type: { type: String, default: "text" },
    value: String,
    placeholder: String,
    btnTitle: String,
    disabled: Boolean,
    error: String
  }
};
</script>
//暂且忽略样式

```

```js
//在login组件里使用封装好的组件
<template>
    <div class="login">
      <InputGroup
      type="number"
      v-model="phone"
      placeholder="手机号"
      :btnTitle="btnTitle"
      :disabled="disabled"
      :error="errors.phone"
      @btnClick="getVerifyCode"
    > </InputGroup>
    </div>
</template>
<script>
  //引入组件
  import InputGroup from "../components/InputGroup";   
export default{
    data(){
     return{
        //传入数据
      phone: "",
      btnTitle: "获取验证码",
      disabled: false,
      errors: {}
    }
    },
    components:{
        //注册组件
        InputGroup
    }
}
</script>
```

#### login与register页面常见的一些问题

登录页与注册页是app项目必要的组件，而且里面的需求大多类似，实现功能的方法大多可以重复使用

- 验证信息与获取验证码

  ```js
  //比如验证一个手机号格式 
  validatePhone() {
        if (!this.phone) {
          this.errors = {
            phone: "手机号不能为空"
          };
          return false;
        } 
      else if (!/^1[345678]\d{9}$/.test(this.phone)) {
          this.errors = {
            phone: "请填写正确的手机号"
          };
          return false;
        } else {
          this.errors = {};
          return true;
        }
      },
   //获取验证码前，先验证手机号
      getVerifyCode() {
        if(this.validatePhone()){
          //if true  点击按钮执行60s倒计时
          this.validateBtn();
          //发出请求,获取验证码
          this.$axios.post("/api/posts/sms_send",{
            tpl_id:"177611",
            key:"7eec8444f69a4c3c7a2c3504dd2ae3bc",
            phone:this.phone
          }).then(res=>{
            console.log(res)
          })
        }
      },      
  // 点击验证按钮，实现一个60秒倒计时
     validateBtn(){
         let time=60,
             //setInterval() 间隔指定的毫秒数不停地执行指定的代码,setInterval() 第一个参数是函数,第二个参数间隔的毫秒数, clearInterval()结束执行
         let timer=setInterval(()=>{
             if(time==0){
                 clearInterval(timer)
                 this.btnTitle = "获取验证码"
                 this.disabled = false;
             }
             else{
            this.btnTitle = time+"秒后重试"
            this.disabled = true;
            time--; 
             }
         },1000) 
     } ，
    //点击按钮实现登录
       handleLogin(){
        this.errors = {}   //取消错误提醒
        // 发出请求  确定你的验证码是否OK
        this.$axios.post("/api/posts/sms_back",{
          phone:this.phone,
          code:this.verifyCode
        }).then(res=>{
          // 表示OK
          //将登录信息保存到 localStorage中
          localStorage.setItem("ele_login",true)
            //调转页面
          this.$router.push("/")
        }).catch(err=>{
          // 表示不OK
          console.log(err)
          this.errors = {
          }
        })
      }
  ```

  路由（router）相关的问题

  ```js
  //首先来复习一下vue路由相关的知识
  //路由的使用 
      1，创建我们需要的组件
      2，在router.js中设置匹配规则
      3，在App.vue中使用router-view用来放路由匹配的组件
      4，使用router-link通过点击实现匹配路由
  //分类
      1.
  //在router.js中配置路由规则
  import Router from 'vue-router'
  Vue.use(Router)
  //引入组件
  import Login from "./views/Login.vue"
  const router =new Router({
    mode: 'history',
    base: process.env.BASE_URL,
    linkActiveClass: 'active',
     routes:[
       //配置规则 
         {
            path:"/",
            redirecct:"/home"
             //重定向
         }，
         {
             path:"/login",
             name:"name",
             component:Login
             //component:()=>import("./views/Login.vue") 这种方式上边可以不用引入
             children:[
         //嵌套路由
                 {
                   path:"",
                   name:
                      }
                ]
         }
     ]
  })
  //设置路由守卫
  //只有在登录状态下才能访问之后的路由，这就需要路由守卫了
   router.beforeEach((to,from,next)=>{
       //判断token是否存在
       const isLogin= localStorage.ele_login?true:false
       if(to.path=='/login'){
           next()
           //访问登录之前的页面，直接放行
       }else{
           //判断token的状态
           isLogin?next():next('/login');
       }
   })
  ```

- ​    响应请求时的加载动画

     ![1565184912380](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1565184912380.png)

  ```js
  //实现上面的效果，过程：请求拦截>加载动画>响应拦截>结束动画
  //main.js中配置mintui库中的Indicator
  import MintUI from 'mint-ui';
  import 'mint-ui/lib/style.css';
  Vue.use(MintUI);
  
  import { Indicator } from 'mint-ui';
  //请求拦截
  axios.interceptors.requsest.use(
     config=>{
         Indicator.open();
         return config;
     },error=>{
         return Promise.reject(error);
     });
  //响应拦截
  axios.interceptors.response.use(
         response=>{
         Indicator.close();   
         },error=>{
         Indicator.close();
         return Promise.reject(error);  
      });
  
  ```

  

