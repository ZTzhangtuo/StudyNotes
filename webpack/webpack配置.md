# 从零开始配置webpack

## 一.webpack概念

webpack是一个现代js应用程序的静态模块打包器,它将根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。 

### 1.核心概念

- entry :入口,
- output :出口 指示webpack在哪了输出它创建的bundles,以及如何命名这些文件
- loader webpack自身只理解js,loader用来处理那些非js类型的文件
- plugins:插件 webpack的插件接口,可以用来处理各种各样的任务
- mode:模式 环境的配置可以使用mode参数 development 开发环境 production 生产环境 

基于对webpack.config.js文件的配置,执行打包是的工作原理,可以总结为:把页面逻辑当做一个整体,通过一个给定的入口文件,webpack从这个文件开始,找到所有的依赖文件,对其依次进行依赖分析,然后进行打包,编译,压缩,最后输出一个浏览器可以识别的js代码(commonJS规范)

### 2.案例配置

#### 2.1案例结构

![1570865142862](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\1570865142862.png)

在根目录下创建build文件夹存放wepack的配置文件

一般在配置的时候分为生产环境和开发环境

```js
//在配置的过程中根据环境将默认的webpack.config.js拆分为三个文件
 //webpack.base.js 
 //webpack.dev.js
 //webpack.prod.js
 //然后用插件webpack-merge来合并配置文件
     "scripts": {
        "dev":"webpack --env.development",
        "build":"webpack --env.production",
      },
 =========== //webpack.base.js=====
      module.exports = (env)=>{
        console.log(env)  // { development: true }  { production: true }
     let isDev=env.development
     ========================
     //这里配置两种环境共同需要的代码 
         const base={
             entry:{//入口}
             output:{//出口}
             module:{//配置loader}
             plugins:{//插件}
         }
     =======================
       if(isDev){
      //开发环境  打包生成的bundle.js未压缩
      return merge(base,dev)
  }else{
      //生产环境 bundle.js压缩压缩
      return merge(base,prod)
  }
      }
  
```

#### 2.2.entry

```js
entry:path.resolve(__dirname,"../src/index.js")
```

#### 2.3output

```js
output:{
    filename:"bundle.js"
    path:path.resolve(__dirname.,"../dist")
}
```

#### 2.4module

```js
module:{
rules:[
    //处理css
  {test:/\(.css|.scss)$/,
   use:["style-loader","css.loader","sass.loader"]
   //style-loader会将解析css 变成style标签插入到页面
  }
]
}
```

#### 2.5plugins

```js
plugins:[
    //使用插件首先要在这里new一下 实例化
  new HtmlWebpackPlugin({
  template:path.resolve(__dirname,"../public/index.html"),
                  //根据模板生成内容中的html
 filename:"index.html",
 minify:!isDev && {
 removeAttributeQuotes:true,//删除属性的引号
 collapseWhitespace:true//去除空白空格
 }
})
]
```

### 3.常用插件

配置时所用的插件保存到开发依赖中

npm i xxx --save-dev (-D)

#### 3.1.html-webpack-plugin

根据模板生成内容中的html,模板处于src文件夹下的index.html文件,并且在dist文件下生成打包过的html文件

#### 3.2mini-css-extract-plugin

单独打包css文件,生成到指定文件夹dist/css/

#### 3.3postcss-loader

```js
对于一些css3中的属性,是需要兼容各种浏览器的,
cover 95% 表示覆盖多少浏览器 
//新建postcss.config.js
//新建.browserslistrc   // 属性 cover 95%
module.exports={
    plugins:[
       require("autoprefixer")//css加前缀
    ]
}
```

#### 3.4optimize-css-assets-webpack-plugin 

压缩css文件 但是使用这个插件后 js就不会压缩了 需要再装一个手动压缩js代码的插件terser-webpack-plugin

```js
//生产环境 webpack.prod.js
var  OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin")
const TerserPlugin=require("terser-webpack-plugin")
module.exports={
    mode:"production",
    optimization:{
        minimizer:[
            new OptimizeCssAssetsPlugin(),
            new TerserPlugin()//手动压缩js文件
        ]
    }
}
```



#### 3.3 webpack-dev-server

```js
// 开发环境下配置本地服务器,使打包后的代码可以运行到浏览器上
//package.json
"script":{
 "serve": "webpack-dev-server --env.development --config ./build/webpack.base.js"
}
npm run serve 运行(仿vue-cli中)
===============webpack.dev.js==================
const path =require("path")
module.exports={
    mode:"development",
    //配置开发环境服务器
    devServer:{
        port:3000,
        compress:true,//gzip压缩
        contentBase:path.resolve(__dirname,"../dist")
    }
}
```

