### Express简介

Express是一个基于 Node.js 平台，快速、开放、极简的 web 开发框架。主要有路由**、**中间件**、**模板引擎**、** 错误处理等功能

实例

```js
const express =require("express")
const bodyparser =require("bodyparser")
const path =require("path")
const app =express();
//对所有请求使用bodyparser中间件,bodyparser.urlencoded,处理表单
app.use(bodyparser.urlencoded({extend:false}))
//bodyparser.json直接将表单中的数据转化成一个对象
ap.use(bodyparser.json())
//====================================
//处理get请求
app.get("/login",(req,res)=>{
    res.sendFile(path.join(__dirname,"index.html"))
})
app.get("/",(req,res)=>{
    res.send("hello world");
})
//处理post请求
app.post("/login",(req,res)=>{
    console.log(req.body)
})
let post=3000
app.listen(port,()=>{
    console.log(`server is running on ${port}`)
})
```

