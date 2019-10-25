npm run serve是开发环境

npm run build是生产环境

```js
// 基于node的 node不支持import语法
// 默认环境变量 NODE_ENV production development
let path = require('path');
module.exports = {
    // baseURL
    publicPath:process.env.NODE_ENV === 'production'? 'http://www.abcd.cn':'/',
    assetsDir:'asserts',
    //assDir:''打包后的资源放入的文件夹
    outputDir:'./dist',
    // outputDir打包后的生成的文件夹，使用模版方式 一般不使用
    runtimeCompiler:false,
    //  runtimeCompiler true的话表示使用自己的内部模板，一般不使用
    productionSourceMap:false,
    //表示不再使用sourcemap
    chainWebpack:config=>{
        // 可以获取到webpack的配置 在增加一些自己的逻辑
        // 配置目录别名 别名叫+
        config.resolve.alias.set('_c',path.resolve(__dirname,'src/components'));
        config.resolve.alias.set('_v',path.resolve(__dirname,'src/views'));
    },
    configureWebpack:{ // webpack-merge
        //webpack配置项，到时候会合并到默认的webpack中
        plugins:[],
        module:{}
    },
    devServer:{ // 本地开发服务器 开发时需要，上线之后不需要
        //代理
        proxy:{
            '/api/getUser':{
                target:'http://localhost:3000',
                pathRewrite:{
                    '^/api':''
                }
            }
        }
    },
    pluginOptions: {
      'style-resources-loader': {
        preProcessor: 'less',
        patterns: [
            path.resolve(__dirname,'src/assets/common.less')
        ]
      }
    }
}

```

