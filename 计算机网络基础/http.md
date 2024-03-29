### 0知识点

http .Tcp协议(tcp连接时的三次握手,四次挥手)

### HTTP相关

#### http版本

##### http1.0

最早的http只是使用在一些较为简单的网页上和网络请求上，所以比较简单，每次请求都打开一个新的TCP链接，收到响应之后立即断开连接。

##### http1.x(重点)

- 缓存控制策略
- 允许范围请求,在请求上加入Range头部
- 请求消息和响应消息都必须包含`Host`头部，以区分同一个物理主机中的不同虚拟主机的域名
- 默认开启持久连接,在一个TCB连接上可以传送多个http请求减少了建立和关闭连接的消耗和延迟。

##### http2.0

- 帧(frame)与流(stream)的概念,帧是数据传输的最小单位,流是多个帧组成的数据流
- 新的二进制格式,1.x的解析是基于文本的,二进制则不同，只识别0和1的组合。基于这种考虑HTTP/2.0的协议解析采用二进制格式，方便且强大。
- 多路复用:是http1.0持久连接的升级版,允许并发的发起多个请求
- 头部压缩:使用encoder来减少header的大小,通讯双方各自cache一份头部 fields表，既避免了重复头部的传输，又减小了需要传输的大小。
- 服务端推送:指的是将client需要的静态资源伴随着index.html一起发给server 省去了客户端重复请求的步骤

#### 请求方式

GET：get方法一般用于获取服务器资源

POST：post方法一般用于传输实体主体

PUT：put方法一般用于传输文件

DELETE：delete方法用于删除文件

HEAD：head方法用于获取报文首部，不返回报文主体

OPTIONS：options方法用于询问请求URI资源支持的方法

#### 状态码(记忆)

- 200 OK 请求被正确处理
- 204 No content  请求成功 但是响应报文中不含实体的主体部分
- 206  Partial content 范围请求成功
- 301 永久重定向 表示资源被分给了新的url
- 302 临时重定向
- 304 not modified 与缓存有关,未满足缓存机制的条件(缓存未过期,或者请求内容没有改变)
- 400 bad request 请求报文存在错误
- 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息(未认证)
- 402 forbidden 表示对请求资源的访问被服务器拒绝，(没有权限访问)
- 404 not found 
- 5xx 服务器错误
- 500 执行请求时出错
- 501 服务器不支持当前请求所需要的某个功能
- 503 服务器处于超负荷活正在停机维护,无法处理请求

#### Header与缓存机制

1. 请求头

   accept-encoding 告诉服务器我接受的数据支持压缩格式 gzip deflate br

   ```js
   //http优化策略:压缩 缓存
   //压缩 
   //将数据体积变小(服务端来进行压缩,响应给客户端,浏览器会自动解压)
   //服务端响应的文件如果浏览器可以识别就进行解析,如果不能识别就以下载的方式处理
   //缓存
   ```

   if-modified-since:   对比缓存 修改时间

   if-none-match :摘要缓存,和Etag配对使用

   user-agent 客户端信息

2. 响应头

   content-type 告诉浏览器 响应给浏览器的内容的类型

   content-encoding 响应给浏览器内容的压缩格式

   Expires 代表该资源的过期时间

   ```js
   //(Expires：Mon, 26 Sep 2018 05:00:00 GMT)
   //服务器告诉浏览器：你把我发给你的 a.js 文件缓存到你那里，在 2018年9月26日5点之前不要再发请求烦我，直接使用你自己缓存的 a.js 就行了。但是缓存过期之后,又会重新请求
   ```

   last-modified: 服务器告诉浏览器a.js文件最后修改内容的时间,对比缓存 ,和if-modified-since配对使用

   ```
   //当a.js过期时.浏览器带上if-modified-since(等于上次请求的last-modified)请求服务器,
   //服务请器比较req header中的if-modified-since与a.js上次修改的时间,
   //if一致 告诉浏览器继续使用本地缓存(304)
   //if不一致,服务器响应新的a.js给浏览器/同时告诉浏览器 a.js 的最近的修改时间 Last-Modified 以及过期时间 Expires。（1+10=11KB）
   //如此往复
   //====================================================
   //这种方案也有缺点
   Expirse控制的时间不稳定,因为浏览器端可以随意修改时间,对比缓存就不准确了
   Last-Modified 过期时间只能精确到秒。然后问题来了 如果 a.js在1秒内多次修改,时间在1秒内服务器不会给浏览器响应最新的a.js
   ```

   Cache-control  (强制缓存)有条件的响应, 例如res.setHeader("Cache-Control","max-age=20") 告诉浏览器 20s内不能再次请求

   ```js
   //基于上面的方案不足,继续改进引入Cache-control
   //为了兼容已经实现了上述方案的浏览器，同时加入新的缓存方案，服务器除了告诉浏览器 Expires ，同时告诉浏览器一个相对时间 Cache-Control：max-age=10秒。意思是在10秒以内，使用缓存到浏览器的 a.js 资源。
   //浏览器先检查 Cache-Control，如果有，则以 Cache-Control 为准，忽略 Expires。如果没有 Cache-Control，则以 Expires 为准。
   
   ```

   Etag 根据摘要做缓存和 if-none-match配对使用

   ```js
   //继续改进 引入res header 引入Etag 文件内容对比 解决文件修改时间只能精确到秒的问题
   //a.js内容变了etag才会变,可以理解etag是文件么内容的唯一id,同时req header中引入if-none-match(同理 它的值是上次请求服务器响应头中的etag)
   //同样服务器收到浏览器的if-modified-since和if-none-macth,则会去比较if-none-macth与etag,如果文件内容没有变化,则两者保持一致,此时服务器告诉浏览器继续使用本地缓存()
   ```

   #### http与https

   http本身没有任何保密性,所以http传输的数据相当于在以明文的方式在网上裸奔

   https基于http协议,通过SSL或TLS提供加密处理,验证对方身份以及数据完整性保护,特点是如下:

   - 内容加密:采用混合加密技术,中间者,无法查看铭文内容
   - 验证身份:通过证书认证客户端访问的是自己的服务器
   - 保护数据完整性：防止传输的内容被中间人冒充或者篡改

   HTTPS和HTTP的区别主要如下：

   1. HTTPS协议需要到CA（证书颁发机构）申请证书，一般免费证书很少，需要交费。
   2. HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
   3. HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
   4. http的连接很简单，是无状态的；HTTPS协议是由HTTP+SSL协议构建的可进行加密传输、身份认证的网络协议，可以有效的防止运营商劫持，解决了防劫持的一个大问题，比http协议安全。

   

####   cookie&session

http协议本身是无状态的 ，不会记录是各个客户端发送的请求访问服务端

会话（打开浏览器，访问一个url，关闭浏览器这个过程称为会话） cookie ，session就是用来做会话控制,会话控制离不开**token**这个概念:token简单来讲就是访问资源的凭证

##### cookie 

- 保存每次会话的标志，把标志留在浏览器上，是服务器种植给浏览器，在种植时，可能通过domain（允许访问的域）,path（路径）,exprix（保存时间）,size,httpOnly（是否只允许服务端来操作cookie）来设置cookie
- 解决http无状态的问题，但是每次请求都会在请求头带上cookie 浪费流量
- 而且可以在浏览器上被修改，不安全，储存大小为4k

##### session

- 基于cookie 保存在服务端（内存 或者数据库）在第一次请求时，服务器会把一个session_id以cookie的形式种植到浏览器，相对安全

##### localStorage

- 储存在本机浏览器上，最大存储体积为5m ，超过5m数据会丢失 不能跨域存取，数据默认永久保存

##### sessionStorge

- 类似localstorage ，区别是，关闭浏览器时，数据自动丢失

#### JWT

(json web token):顾名思义就是json结构的token

- header(用于描述元信息)
- payload(你希望向服务端传递的信息)
- signature(签名)

创建签名要分一下几个步骤

1. 从服务端拿到秘钥,假设为secret

2. 将header进行base64编码 假设为headerstr

   ```js
   jwt的header
   {
   "alg": "HS256",//算法
   "typ": "JWT"//token的类型
   }
   ```

   ]()

3. 将payload进行base64,假设结果为playloadstr

4. 拼接headerstr和palyloadstr字符串

5. 以data和secret作为参数,使用hash算法计算出签名

 

```js
// 伪代码
data = base64urlEncode( header ) + “.” + base64urlEncode( payload )
signature = Hash( data, secret );
```



