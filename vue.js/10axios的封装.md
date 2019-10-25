```js
import axios from 'axios';
import QS from 'qs';
import { Toast } from "vant";

//环境的切换 通过node的环境变量来匹配我们默认的接口url前缀
if(process.env.NODE_ENV=='development'){
    axios.defaults.baseURL="http://www.baidu.com";
    //测试环境
}else if(process.env.NODE_ENV=='debug'){
    axios.defaults.baseURL="http://www.sina.com";
  //生产环境
}else if(process.env.NODE_ENV=='production'){
    axios.defaults.baseURL = 'https://www.production.com';
}
//通过axios.defaults.timeout 设置默认的请求超时时间 超过设定时间 告知用户请求超时
//重新刷新
axios.defaults.timeout=10000;//10s
//post请求头的设置
//post请求的时候需要加上一个请求头
axios.defaults.headers.post['Content-type']='application/x-www-form-urlencoded;charset=UTF-8/x-www-form-urlencodes'
//请求拦截 和请求响应 

//get方法的封装
//定义一个get函数 ,这个函数有两个参数,第一个是我们要请求的url地址 第二个是我们携带的请求餐胡
//get函数返回的是一个promise对象 请求成功resolve服务器返回值 失败时返回reject错误是

export function get(url,params){
    return new Promise((resolve,reject)=>{
        axios.get(url,{
        params:params
        }).then(res=>{
            resolve(res.data)
        }).catch(err=>{
            reject(err.data)
        })
    })
}
//post方法与get原理基本一样 但是post方法必须要使用对提交的参数进行序列化操作
//不进行序列化后台是拿不到数据的
export function post(url,params){
    return new Promise((resolve,reject)=>{
    axios.post(url,QS.stringify(params))
    .then(res=>{
        resolve(res.data)
    }).catch(err=>{
        reject(err.data)
    })
    })
}
```

```js
// 自己封装axios 
//首先引入axios
import axios from "axios"
import store from "../store"
import { getLocal } from "./local";

//创建一个类 然后导出
class AjaxRequest{
  //构造器
  constructor(){
      this.baseURL=process.env.NODE_ENV=="production"?"/":"http://localhost:3000";
      //请求超时时间
      this.timeout=5000;
      //存放每一次请求
      this.queue={};
  }
  //
  merge(options){
      return{...options,baseURL:this.baseURL,time:this.timeout}
  }
  //请求拦截 每次请求都加上一个loading效果
  setInterceptor(instance,url){
      instance.interceptors.request.use((config)=>{
        //把token放到请求头上,每次请求都会带上
          //console.log(getLocal("token"))
          config.headers.Authorization=getLocal("token")
          //只有第一次请求时，显示loading动画
          if(Object.keys(this.queue).length===0){
            store.commit('showLoading')
          }
          this.queue[url]=url;
         
          return config;
      });
      //请求响应
      instance.interceptors.response.use((res)=>{
          //响应后关闭loading
          delete this.queue[url]
          if(Object.keys(this.queue).length===0){
            store.commit('hideLoading')
          }
          return res.data;
      })
  }
  request(options){
      //实例一个axios对象
      let instance =axios.create();
      this.setInterceptor(instance,options.url);
      let config =this.merge(options)
      //axios执行后 返回promise
      return instance(config)
  }
}
//导出
export default new AjaxRequest
```

