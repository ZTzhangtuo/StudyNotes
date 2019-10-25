## 基于koa的bootstrap后台管理系统项目总结

### 中间件的配置与使用(app.js文件)

#### koa-session

```js
const session=require("session")
//配置
app.keys = ['some secret hurr'];
const CONFIG = {
  key: 'koa:sess',
  maxAge: 86400000,
  autoCommit: true,
  overwrite: true,
  httpOnly: true,
  signed: true,
  rolling: false,
  renew: false,
};
app.use(session(CONFIG, app));
```

#### koa-art-template

```js
 "art-template": "^4.13.2",
 "koa-art-template": "^1.1.1",
 //使用时注意上面的两个依赖都要引入
//模板引擎的配置
render(app, {
  root: path.join(__dirname, 'views'),
  extname: '.html',
  debug: process.env.NODE_ENV !== 'production',
  //格式化时间中间件
  //const sd =require("silly-datetime")//
  dateFormat:dateFormat=function(value){
    return sd.format(value,'YYYY-MM-DD HH:mm')
  }
});
//使用{{}} 类似vue中的插值表达式 将页面中一些复用的代码块 放进一个public文件夹  
 使用语句 {{include "admin/public/header.html"}} 插入
 循环语句
 {{each  }}
.....//需要循环的代码块
 {{/each}}
 条件语句
 {{if}}
  ....
  {{else}}
   ....
  {{/if}}

```

#### koa-router

```js
//app.js入口文件
const router=require("koa-router")()                           
//引入路由模块
let admin =require("./routes/admin")
let index =require("./routes/index")
let api =require("./routes/api")
//创建一级路由
app.use("/admin",admin)
let index =require("./routes/index")
let api =require("./routes/api")
//启动路由
app.use(router.routes())
app.use(router.alloweMethods())
//参照下图
```

路由结构

![1566911881217](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1566911881217.png)

#### koa-static

```js
//托管静态资源
const static =require("koa-static")
//托管静态资源到public文件夹下
app.use(static(__dirname +"/public"))
//得到资源的绝对路径
ctx.state.__HOST__="HTTP://"+ctx.request.header.host
```

二级路由

```js
//项目结构中 admin.js /   api.js / index.js 就是二级路由文件
//admin.js文件
 const router =require("koa-router")()
 const path =require("koa-path")
 const url =require("koa-url)
 //引入二级路由模块
 let login =require("./admin/login")
router.use(async(ctx,next)=>{
    //将得到资源的绝对路经保存到ctx的state中
	ctx.state.__HOST__="HTTP://"+ctx.request.header.host
    let pathname=url.parse(ctx.request.url).pathname.substring(1)
    //将得到pathname使用spilt方法分割再转化为一个数组
    let splitUrl=pathname.split("/")
    ctx.state.G={
        url:splitUrl,
        userinfo:ctx.session.userinfo,
        prePage:request.header['referer']//上一级路由
    }
})
 //创建二级路由
 router.use("/login",login)
//导出路由模块 
 module.exports=router.routes()
```

#### 工具函数(model下js文件)

```js
const DB =require(./db.js)
let md5 =require("md5")
let tools={
      //字符串加密
	md5(str){
 		return md5(str)},
    //将带有pid的数据转化为树状数据
    getToList(json){
        let cateList=[],
        //遍历数组,筛选出pid为0的元素
        json.forEach(
           function(item){
              if(item.pid==0){
              cateList.push(item)
        }
        });
        for( let i=0;i<cateList;i++){
        cataList[i].childList=[];
            for(let j=0;j<json.length;j++){
            	if(cateList[i]._id==json[j].pid){
                 cataList[i].childList.push(json[j])
                }
            }
        }
        return cateList
    }
}
```

#### 传输文件相关

```js
//中间件
const multer =require("koa-multer")
//配置图片上传的配置
ar storage = multer.diskStorage({
    //文件保存路径
    destination: function (req, file, cb) {
        cb(null, 'public/uploads/')  // 上传的文件 保存在什么地方
    },
    //修改文件名称
    filename: function (req, file, cb) {
        var fileFormat = (file.originalname).split(".");  //以点分割成数组，数组的最后一项就是后缀名
        cb(null, Date.now() + "." + fileFormat[fileFormat.length - 1]);
    }
})
//加载配置
var upload = multer({ storage: storage });
//页面中提交文件时注意一定要加上  enctype="multipart/form-data"
<form action="{{__HOST_}}/admin/article/doAdd"  methdos="post" enctype="multipart/form-data">
    <input  type="file" name="pic"/>
</form>
//后端接收
router.post("/doAdd",upload.single("pic"),async(ctx)=>{
    let pic=ctx.req.file
})
```

#### 分页实现

