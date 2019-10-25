### node.js基础

js的运行环境,模块化,作为前端的工具 

作为服务器  runtime js

中间层:跨域处理 过滤数据

SSR:服务器渲染







#### global:

- process 进程 表示当前的运行环境
- Buffer 内存中的数据,二进制数据,缓存

#### buffer

缓冲区  内存中的一块区域

```js
//Buffer.from();  Buffer.alloc() ; Buffer.allocUnsafe()
let a =Buffer.from("中")
console.log(a)//  <Buffer e4 b8 ad> 一个汉三个字节
console.log(a.toString(utf-8)) //中
let b1 =Buffer.from([10])
/console.log(b1) // <Buffer 0a>  10 写数组表示放了数字

let b1 = Buffer.alloc(10) // 开一块内存空间，大小为10个字节，把这10个空间的垃圾数据全部清空
console.log(b1)
// <Buffer 00 00 00 00 00 00 00 00 00 00>
 let b1 = Buffer.allocUnsafe(10) // 开一块内存空间，大小为10个字节，只管分配空间，不管清空里面的垃圾数据
console.log(b1)
 // console.log(b1) <Buffer 90 d1 4f 97 35 00 00 00 00 00>

```

buffer.from()

#### path:

basename  得到文件名

```js

let path =require("path")
let name = path.basename("index.js",".js")
console.log(name);
//index 
```

extname 得到文件扩展名

```js
let path =require("path")
let name = path.extname("index.js",)
console.log(name);
//.js
```

dirname  得到目录名字

```js
let path =require("path")
let name = path.dirname("C/Index/index.js",)
console.log(name);
//.C/Index
```

join  把几个路径拼成一个路径

```js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// 返回: '/foo/bar/baz/asdf'
```

`path.resolve()` 方法将路径或路径片段的序列解析为绝对路径。

```js
path.resolve('/foo/bar', './baz');
// 返回: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// 返回: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// 如果当前工作目录是 /home/myself/node，
// 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

#### create a Server

```js
//引入 http url querystring  内置模块
const http = require( "http")
const url =require("url")
const querystring =require("querystring")

//假设 现在要从浏览器访问http://localhost:3000/shouji?name=xiaoming 这个路径
//搭建一个服务器
http.createServer((req,res)=>{
    //解决中文字符渲染时出现乱码的情况
     res.writeHead(200, {'Content-Type': 'text/html;charset=utf-8'})
    //结构赋值  
        //pathname 路径名d(str)
        //query 查询字符串
    let{ pathname,query}=url.parse(req.url)
     console.log(pathname,query)//  结果 /shouji name=xiaoming
   // querystring.parse()这个api可以将得到的字符串转化为对象
    let r =querystring.parse(query)
    conslog.log(r)   // { name: 'xiaoming' }
    if(path==="/abc"){xss
        //在数据流过程中,有两个事件 data&end
        req.on("data",(chunk)=>{
        })
        req.on("end",()=>{
        })
}).listen(3000)
```

#### create a client 

```js
//创建一个客户端
let http = require ("http")
http.require({
    hostname:"localhost",//域名
    port:"3000",//端口
    path:"/abc",//路径
    methods:"post",//请求方式 get post
    Headers:{
        //请求头
            "Content-Type":"application/x-www-from-urlencoded"//表单的形式
    }
}).end("")
```

#### events

node.js是基于事件驱动的

```js
let EventEmitter =require("events")
//事件发射器
let path =require("path")
let e =new EventEmitter()
let fun =function(){
    console.log("rap")
}
e.on("cxk",()=>{
    console.log("sing")
})
e.on("cxk",()=>{
    console.log("dance")
})
e.on("cxk",fun)
e.on("cxk",()=>{
    console.log("basketball")
})
//cxk 事件名
e.emit("cxk")//sing dance rap basketball
//想要取消订阅,订阅与取消订阅必须是同一个函数
e.off("cxk",fun)
e.emit("cxk")//sing dance basketball

//手写事件驱动
class EventEmitter{
    constructor(){
        this.myevents={}
    }
    on(eventName,fun){
        if(this.myevents[eventName]){
            this.myevents[eventName].push(fun)
        }else{
            this.myevents[eventName]=[fun]
        }
    }
    emit(eventName){
        this.myevents[eventName].forEach(fn=>fn())
    }
    off(eventName,fun){
        this.myevents[eventName]=this.myevents[eventName].filter(fn=>{
            return fn != fun
        })
    }
}
let e = new EventEmitter()
e.on("cxk",()=>{
    console.log("i can sing ")
})
e.on("cxk",()=>{
    console.log("i can rap ")
})
e.on("wyf",()=>{
    console.log("da wan kaun mian ")
})
e.emit()
```

#### stream(流)

数据流:简单分为可读流与可写流

```js
// fs.createReadStream(path[, options])
// fs.createWriteStream(path[, options])
const fs =require("fs")
const path=require("path")
let rs=fs.createReadStream(path.join(__drrname,"name.txt"),{
    flags:"r", // r read 
    highWaterMark:4, // 4*1012  4kb   4kb = 4*1024字节
    encoding:null,
    autoClose:true, // 读取完后，需要关毕
    start:0,
    end:6
} )
let arr=[];
//数据流过程有需要两个事件 data end
rs.on("data",(chunk)=>{
    arr.push(chunk)
    rs.pause()//暂停data事件
})
rs.on("end",()=>{
    //将接收的budder数据转化为字符串
    console.log(Buffer.concat(arr).toString())
})
setTimeout(()=>{
    re.resome()//恢复data事件
},5000)
```

#### http

Web使用一种名为HTTP（HyperText Transfer Protocol，超文本传输协议）的协议作为规范，完成从客户端到服务器等一系列运作流程。而协议是指规则的约定。可以说，Web是建立在HTTP协议上通信的。

在node.js上的使用

请求:request    

1. 请求头:浏览器添加,自己也可以添加
2. 请求行:方法 路径 协议
3. 请求正文 :给服务器的数据

 响应:response

1. 状态码:200 请求成功,301(永久重定向) 302(临时重定向) 304( 缓存)401(无权访问) 404(not found)  500(表示服务器端在执行请求时发生了错误)
2. 响应头
3. 响应正文:服务器给客户端真实的数据

```js
let server =http.createServer((req,res)=>{
     // req 表示请求，是一个可读流     res 表示响应，是 一个可写流
    // console.log(req) // IncomingMessage
    // console.log(req.url) // /  前端的路由：一个url对应一个组件   后端路由：一个url对应资源  /shop?name=%22zhangsan%22
    let arr=[]
    req.on("data",(chunk)=>{
       arr.push(chunk)
    })
    req.on("end",()=>{
        
    })

})
server.listen(3000,()=>{
    console.log("server is running in port 3000")
})
```

