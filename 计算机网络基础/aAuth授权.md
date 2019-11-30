### OAuth实现第三方登录

所谓第三方登录，实质就是 OAuth 授权。用户想要登录 A 网站，A 网站让用户提供第三方网站的数据，证明自己的身份。获取第三方网站的身份数据，就需要 OAuth 授权。

参考文章

http://www.ruanyifeng.com/blog/2019/04/github-oauth.html

举例来说，A 网站允许 GitHub 登录，背后就是下面的流程。

> 1. A 网站让用户跳转到 GitHub。
> 2. GitHub 要求用户登录，然后询问"A 网站要求获得 xx 权限，你是否同意？"
> 3. 用户同意，GitHub 就会重定向回 A 网站，同时发回一个授权码。
> 4. A 网站使用授权码，向 GitHub 请求令牌。
> 5. GitHub 返回令牌.
> 6. A 网站使用令牌，向 GitHub 请求用户数据。

图解。

![图片加载失败!](https://gitee.com/gitee_fanjunyang/JueJin/raw/master/images/OAuth%E7%9A%84%E4%BD%BF%E7%94%A8_2.png)

代码实现

```js
//前端
<body>
    <div id="app">
        <h1>使用github登录</h1>
        <a href="/github/login">login with github</a>
    </div>
</body>
//后端(node服务器)
const koa = require("koa")
const router = require("koa-router")()
const static = require('koa-static')
const axios =require("axios")
const querystring=require('querystring')

const app = new koa()
//托管前台静态资源 
app.use(static(__dirname + "/"))
const config = {
    client_id: "fa441c12c7bd0816aa13",
    client_secret:"dfcf5e6eee70cc5d0237ae8b004ae48763371814"
}
router.get("/github/login", (ctx) => {
    //访问先前在github注册的地址
    //重定向到github的认证页面
    let path = "https://github.com/login/oauth/authorize?client_id="+config.client_id
    ctx.redirect(path)
    // ctx.body="hello"
})
router.get("/github/callback", async (ctx) => {
    // console.log("callback url")
    //github 服务器认证并带回认证的code
    const code = ctx.query.code
    const params = {
        client_id: config.client_id,
        client_secret: config.client_secret,
        code:code
    }
    //拿到code后 本地服务器向github服务器发送请求,申请令牌
    //console.log(code)
    let res = await axios.post("https://github.com/login/oauth/access_token", params)
    // console.log(res.data)//access_token=171e122984ca7866ac54e2337e27f9b98fbdf632&scope=&token_type=bearer
    //令牌已经拿到 queryString转一下
    const access_token = querystring.parse(res.data).access_token
    // console.log(access_token)
    //有了令牌就可以得到用户的信息了
    res = await axios.get("https://api.github.com/user?access_token=" + access_token)
    ctx.body = `<h1>hello${res.data.login}</h1>
   <img src=${res.data.avatar_url}/> `
})
app.use(router.routes())
app.use(router.allowedMethods());
app.listen(3000, () => {
    console.log("server is running")
})
```





