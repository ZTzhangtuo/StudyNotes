### 简介

jsonp : json with packing 

将json数据包装起来并返回的一种方式-----协议

在使用jsonp实现跨域的时候,它是需要将json数据作为函数的参数,最终在服务器端将包装好的字符串作为结果返回给客户端

缺点:只能实现get一种请求

jsonp的实现原理

- 首先jsonp是利用script便签的src属性来实现跨域的
- 动态的添加script标签,url中传参一个回调函数名作为查询字符串给服务端,
- 服务区得到请求响应时：服务端将要返回的`数据`作为参数和前端传过来的`函数名称`拼接在一起(格式类似”`jsonpCallback({name: 'abc'})`”)返回。当浏览器接收到了响应数据，由于发起请求的是 `script`，所以相当于直接调用 `jsonpCallback` 方法，并且传入了一个参数

```js
//原生实现 
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';

    // 传参一个回调函数名给后端，方便后端返回时执行这个在前端定义的回调函数
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=handleCallback';
    document.head.appendChild(script);

    // 回调执行函数
    function handleCallback(res) {
        alert(JSON.stringify(res));
    }
 </script>
服务端返回如下
handleCallback({"status": true, "user": "admin"})
```

