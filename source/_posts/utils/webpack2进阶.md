---
title: webpack2进阶
type: "categories"
categories: 工具
---

### 指定静态资源的url路径前缀

现在我们的资源文件的url直接在根目录, 比如http://127.0.0.1:8100/index.js, 这样做缓存控制和CDN都不方便, 我们需要给资源文件的url加一个前缀, 比如 http://127.0.0.1:8100/assets/index.js这样. 我们来修改一下webpack配置:
```
{
  output: {
    publicPath: '/assets/'
  },

  devServer: {
    // 指定index.html文件的url路径
    historyApiFallback: {
      index: '/assets/'
    }
  }
}
```

### 常用库单独打包
dllPlugin()

### 简化import路径
```
resolve: {
  alias: {
    '~': resolve(__dirname, 'src')
  }
}
```

## 开发环境允许其他电脑访问

webpack配置devServer.host为'0.0.0.0'即可.

## 打包时自定义部分参数

default.js
```
module.exports = {
  publicPath: 'http://cdn.example.com/assets/'
}
```

dev.js: 默认开发环境
```
module.exports = {
  publicPath: '/assets/',

  devServer: {
    port: 8100,
    proxy: {
      '/api/auth/': {
        target: 'http://api.example.dev',
        changeOrigin: true,
        pathRewrite: { '^/api': '' }
      },

      '/api/pay/': {
        target: 'http://pay.example.dev',
        changeOrigin: true,
        pathRewrite: { '^/api': '' }
      }
    }
  }
}
```
local.js  个人本地环境, 在dev.js基础上修改部分参数.
```
const config = require('./dev')
config.devServer.port = 8200
module.exports = config
```
package.json修改scripts:

```
{
  "scripts": {
    "local": "npm run dev --config=local",
    "dev": "webpack-dev-server -d --hot --env.dev --env.config dev",
    "build": "rimraf dist && webpack -p"
  }
}

```

这里的关键是npm run传进来的自定义参数可以通过process.env.npm_config_*获得. 参数中如果有-会被转成_
--env.*传进来的参数可以通过options.*获得. 我们优先使用npm run指定的配置文件. 这样我们可以在命令行覆盖scripts中指定的配置文件:

```
npm run dev --config=CONFIG_NAME
```

config.devServer.proxy用来配置后端api的反向代理

config.devServer.proxy用来配置后端api的反向代理, ajax /api/auth/*的请求会被转发到 http://api.example.dev/auth/*, /api/pay/*的请求会被转发到 http://api.example.dev/pay/*.

changeOrigin会修改HTTP请求头中的Host为target的域名, 这里会被改为api.example.dev

pathRewrite用来改写URL, 这里我们把/api前缀去掉.


## 代码中插入环境变量
```
const pkgInfo = require('./package.json')

module.exports = (options = {}) => {
  const config = require('./conf/' + (process.env.npm_config_config || options.config || 'default')).default

  return {
    // ...
    plugins: [
      new webpack.DefinePlugin({
        DEBUG: Boolean(options.dev),
        VERSION: JSON.stringify(pkgInfo.version),
        CONFIG: JSON.stringify(config.runtimeConfig)
      })
    ]
  }
}
```
DefinePlugin插件的原理很简单, 如果我们在代码中写:
```
console.log(DEBUG) ==> console.log(true)

//代码压缩的时候进行判断
if (DEBUG) {
  console.log('debug mode')
} else {
  console.log('production mode')
}
```

## 编译前清空dist目录
本来可以采用rm -rf 目录，但是考虑到跨平台需要安装一个包**rimraf**
```
{
  "scripts": {
    "build": "rimraf dist && webpack -p --env.config production"
  },
}
```

## 在 Webpack 中使用公用 CDN
Webpack 是如此的强大，用其打包的脚本可以运行在多种环境下，Web 环境只是其默认的一种，也是最常用的一种。考虑到 Web 上有很多的公用 CDN 服务，那么 怎么将 Webpack 和公用的 CDN 结合使用呢？方法是使用 externals 声明一个外部依赖,这样指定的依赖不会被webpack解析，但会成为bundle里的依赖
```
 externals: {
    moment: true
  }
```
当然了 HTML 代码里需要加上一行
```
<script src="//apps.bdimg.com/libs/moment/2.8.3/moment-with-locales.min.js"></script>
```
[参考](https://segmentfault.com/a/1190000007914129#articleHeader24)