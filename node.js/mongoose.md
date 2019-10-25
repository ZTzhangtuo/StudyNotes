### mongoose使用

#### 基本使用

```js

    // 1，安装并引入
		   // npm install mongoose
            //const mongoose = require("mongoose")
    // 2，建立连接  mongoose.connect("mongodb://127.0.0.1/myapp")
    //     如果在数据库没有myapp这个数据库，连接时，并不会自动创建这个数据库 
    // 3，定义一个Schema，它需要和你最终在数据库中创建的字段保持一样
    //     Schema创建完后，也不会在数据库自动创建这个数据库
    // 4，根据Schema创建model 
    // 5，实例化model
    // 6，通过save保存数据到数据库,如果数据库中没有这个库,到save()调用完成才创建
//引入中间件
const mongoose=require("mongoose")
//连接数据库,这个时并未创建myapp这个数据库
mongoose.connect("mongodb://127.0.0.1:27017/myapp")
//定义表的结构 Schema(架构,结构,概要)
const UserSchema =mongoose.Schema({
    name:String,
    age:Number,
    status:Number,
    gender:String
})
//根据设计好的结构,创建model,第一个参数最好大写,这里并没有指定第三个参数,
//默认创建的集合名为users(第一个参数小写后的复数形式)
let User =mongoose.model("User",UserSchema)
//let user =mongoose.model("User",userSchema,"user")//第三个参数可以定义集合名
//实例化这个model
let u = new User({
    name:"jacklove",
    age:100,
    status:1,
    gender:"male"
})
//调用save时,才能往数据库中保存数据
u.save((err,date)=>{
    if(err){
        console.log(err)
    }
    console.log("数据保存成功")
})

```

#### 操作数据库

```js
const mongoose=require("mongoose")
mongoose.connect("mongodb://127.0.0.1:27012/myapp")
const c=mongoose.Schema({
    name:String,
    age:Number,
})
let MyModel=mongoose.model("News",NewSchema,"news")
let u=new MyModel({
    name:"jackMa",
    age:60
})
//findOne updateOne deleteOne 
//注意这三个方法都是model上的方法 第一个参数是条件,第二个参数是回调函数
MyModel.findOne({},(err,data)=>{
    if(err) console.log(err)
    console.log(data)
})
```

#### 写一个注册的接口

```js
const express=require("express")
const bcrypt =require("bcrypt")//,密码加密
const router=express.Router()
router.get("/register",(req,res)=>{
    model.findOne({email:req.body.email}).then(data=>{
        if(data){
            //邮箱已被注册
            return res.json({mag:"邮箱已被注册"})
        }else{
            const newModel=new model({
                name:req.body.name
                email:req.body.email,
                password:req.body.password })
            //密码加密处理
            bcrypt.genSalt(10,(err,salt)=>{
                bcrypt.hash(newModel.password,salt,(err,hash)=>{
                    if(err)
                       res.json(err)
                    else
                     newModel.save().then(user=>res.json(user)).catch(err=>res.json(err))   
                })
            })
        }
    }
)
})
```

