---
title: webpack2
type: "categories"
categories: 工具
---


## 为什么要有webpack
- 依赖加载混乱
- 引用重复的代码
- 等等
本来他是个打包工具，但是现在它俨然成了一个管理工具

## webpack2如何运行
- 从context 文件夹
- 查找entry对应文件
- 找到文件夹，执行该文件的所有的依赖
- 输出到对应的目录
```
const path = require('path');
const webpack = require('webpack');
module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    app: './app.js',
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
    libraryTarget: 'umd' //以什么样的模式导出代码
  },
};
```
## 命令行输入 webpack 命令,发生了什么
- 首先npm会把包的可执行文件安装到./node_modules/.bin/目录下, 所以我们要在这个目录下执行命令.
- 执行 bin 目录下的 webpack.js 脚本，解析命令行参数以及开始执行编译。
- 调用 lib 目录下的 webpack.js 文件的核心函数 webpack ，实例化一个 Compiler，继承 Tapable 插件框架，实现注册和调用一系列插件。
- 调用 lib 目录下的 /WebpackOptionsApply.js 模块的 process 方法，使用各种各样的插件来逐一编译 webpack 编译对象的各项。
- 在3中调用的各种插件编译并输出新文件。

## 打包方式
- webpack -p //生产打包
- webpack -d --hot//开发打包
-d参数是开发环境(Development)的意思, 它会在我们的配置文件中插入调试相关的选项, 比如打开debug, 打开sourceMap, 代码中插入源文件路径注释.
--hot开启热更新功能, 参数会帮我们往配置里添加HotModuleReplacementPlugin插件, 虽然可以在配置里自己写, 但有点麻烦, 用命令行参数方便很多.

运行命令之后, Webpack 会输出一个 dist/app.bundle.js 文件, 同时在控制台输出当前日期. 需要注意的是, Webpack 会自动找到 moment 的指向(即使你有一个 moment.js 存在于目录中, 但 Webpack 默认会优先去寻找 moment 的Node模块).


## 多文件
- 多个入口文件打包出一个出口文件
```
module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    app: ['./home.js', './events.js', './vendor.js'],
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
  },
};

```

- 多个入口文件打包出多个出口文件

```
module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    home: './home.js',
    events: './events.js',
    contact: './contact.js',
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
  },
};
```

## 自动打包

如果你将应用分开打包到多个 output 文件里(如果你的应用有非常多的 JavaScript 文件不需要在前期加载, 这样做是非常有效的), 有可能会出现很多冗余的代码, 因为 Webpack 是独立解析每个文件的依赖的. 幸运的是, Webpack 已经内置了 CommonsChunk 插件来处理这个问题:

```
    new webpack.optimize.CommonsChunkPlugin({
      name: 'commons',
      filename: 'commons.js',
      minChunks: 2,
    }),
```

加上 CommonsChunk 插件后, 任何一个模块在你的 output 文件中被加载 2 次(该值由 minChunks 设置)及以上, 该模块就会被打包在 common.js 中, 你可以在客户端缓存这些模块. 虽然这会增加额外的请求, 但这能防止客户端多次下载同一个模块.

## 开发环境
为启动服务器, 仅需要在 webpack.config.js 中添加一个 devServer 对象:
```
module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    app: './app.js',
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, './dist/assets'),
    publicPath: '/assets',                          // New
  },
  devServer: {
    contentBase: path.resolve(__dirname, './src'),  // New
    //historyApiFallback用来配置页面的重定向
    //配置为true, 当访问的文件不存在时, 返回根目录下的index.html文件
    historyApiFallback: true
  },
};
```
在 src 目录下创建一个带如下标签的 index.html 文件:

```
<script src="/assets/app.bundle.js"></script>
```
在终端运行如下命令:
```
	webpack-dev-server
```
## 全局方法调用
需要使用在全局作用域下的函数? 只需在 output.library 进行简单的设置就行:
```
module.exports = {
 output: {
   library: 'myClassName',
 }
};
```
它会把你的打包文件捆绑在 window.myClassName 实例上. 设置了作用域之后, 你可以在文件的入口处进行调用

## 总结1
到目前为止, 我只介绍了怎么使用 Webpack 处理 JavaScript 文件. 从处理 JavaScript 文件开始是非常重要的, 因为这是 Webpack 唯一能识别的语言. 实际上, Webpack 可以使用 Loaders 来处理各种通过 JavaScript 传递的任何类型的文件。

## Loaders
loader 可以是像 Sass 这样的预处理器, 也可以是像 Babel 这样的编译器. 在 NPM 里, 它们通常被命名为 *-loader, 例如: sass-loader 或者 babel-loader.
- 先安装包 yarn install
- 通过正则匹配文件路径，然后进行编译加载处理
### Babel+ES6
### CSS+Style Loader
```
module.exports = {
  // …
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      // …
    ],
  },
};
```
Loaders会按照数组的逆序运行, 也就是说, 会先运行 css-loader, 后运行 style-loader.

你可能会注意到, 在生产环境下, CSS 会被打包到 JavaScript 文件里, style-loader 则会把样式写在 style 标签中. 此外, Webpack 通过将这些文件打包成一个文件来自动地解析所有的 @import 查询(而不是依赖 CSS 的默认 import 功能, 这会导致额外的 header 请求, 并且加载资源非常慢).

从 JavaScript 中加载 CSS 是非常神奇的, 因为这样你可以用新的方式将 CSS 模块化. 也就是说, 可以仅通过 button.js 来加载 button.css, 这意味着如果 button.js 没有用到, 其对应的 CSS 也不会被构建到生产环境中.

### CSS+Node 模块
我们可以使用 Webpack 里的 ~ 前缀来引入 Node 模块
```
@import "~normalize.css";
```
这样就可以充分利用 NPM 管理第三方样式库的优点---版本更新和避免复制和粘贴. 更近一步, 和 CSS 默认的 import 功能相比, 用 Webpack 打包 CSS 有明显的优势, 因为它可以为客户端减少头部请求以及缓慢的加载时间.

### 将 CSS 分开打包

你可能需要处理渐进增强的情况, 也可能因某些原因需要分离 CSS 文件. 这个也很简单, 只需将配置文件中的 style-loader 用 extract-text-webpack-plugin 代替就行.
```
const ExtractTextPlugin = require('extract-text-webpack-plugin');
module.exports = {
  // …
  module: {
    rules: [
      {
        test: /\.css$/,
        loader:  ExtractTextPlugin.extract({
          loader: 'css-loader?importLoaders=1',
        }),
      },
    
      // …
    ],
    noParse: /node_modules\/(jquey|moment|chart\.js)/   //这些库没有必要去解析他们的依赖
  },
  plugins: [
    new ExtractTextPlugin({
      filename: '[name].bundle.css',
      allChunks: true,
    }),
  ],
};
```
运行 webpack -p 之后, 你会发现在 output 指定的目录中会有一个 app.bundle.css 文件. 最后, 在 HTML 文件中通过 <link> 标签正常引用.

## 实现ES6的模块加载
可以用import,export，不需要把他们转成CommonJS模块的格式

### 代码分割

- ES6 Loader 规范定义了 System.import 方法，用于在运行时动态加载 ES6 模块。
- Webpack 把 System.import 作为拆分点，然后把被请求的模块放入一个单独的 “块”（chunk）中。
- System.import 接受模块名作为参数，然后返回一个 Promise。
- chunk 加载失败产生的错误现在可以被捕获了。
```
	System.import("./module").then(module => {
		module.default;
	}).catch(err => {
		console.log("Chunk loading failed");
	});
```
开发服务器会运行在 localhost:8080(打开你的浏览器访问该地址就能看到你的页面). 需要注意的是 script 标签里的 /assets 是和 output.publicPath 匹配的--你可以把它命名成任何你想要的名字(如果你使用CDN, 这会很有用).
Webpack 提供了热加载功能. 当你修改了 JavaScript 文件后, Webpack 会自动重新加载资源, 而不需要你手动去刷新浏览器. 但是, 任何对 webpack.config.js 文件的改变都需要重启服务器才会生效.

**动态表达式**
还可以把一个表达式作为参数传给 System.import。表达式的处理方式类似于 CommonJS（Webpack 为每个可能的文件创建一个独立的上下文）。
System.import 会令每个可能的模块都产生一个独立的 chunk。所以就可以用循环产生多个 chunk，感觉简写了有木有

```
function route(path, query) {
	return System.import("./routes/" + path + "/route")
		.then(route => new route.Route(query));
}
// 这会为每种可能的路径组合都创建一个对应的 chunk。
```

### Babel 与 Webpack

在默认情况下，Babel 的 es2015 预设方案（preset）会把 ES6 模块转换为 CommonJS 格式。如果你想让 Webpack 来处理 ES6 模块，那你应该换用 es2015-webpack 这个预设方案。

## 配置
在过去的环境中，变量通常用来在配置文件中处理不同的环境。Webpack 2 引入了一种新方法，可以将选项传给配置。
配置文件可以导出一个函数，这个函数返回配置。这个函数会被 CLI（命令行界面）调用，而通过 --env 参数传过来的值会被传递给这个配置函数。

```
on the terminal : --env dev //string

on the terminal : --env.minimize true --env.server localhost //object

//配置
// webpack.config.babel.js
exports default function(options) {
	//string  => dev
	//object  => {minimize: true, server: "localhost"}
	return {
		// ...
		devtool: options.dev ? "cheap-module-eval-source-map" : "hidden-source-map"
	};
}
```

## 解析选项

{
	modules: [path.resolve(__dirname, "app"), "node_modules"]
	// （以前这个选项分散在 `root`、`modulesDirectories` 和 `fallback` 三处。）
	// 模块查找路径：指定解析器查找模块的目录。
	// 相对路径会在每一层父级目录中查找（类似 node_modules）。
	// 绝对路径会直接查找。
	// 将按你指定的顺序查找。

	descriptionFiles: ["package.json", "bower.json"],
	// 描述文件：这些 JSON 文件将在目录中被读取。

	mainFields: ["main", "browser"],
	// 入口字段：在解析一个包目录时，描述文件中的这些字段所指定的文件将被视为包的入口文件。

	mainFiles: ["index"]
	// 入口文件：在解析一个目录时，这些文件将被视为目录的入口文件。

	aliasFields: ["browser"],
	// 别名字段：描述文件中的这些字段提供了该包的别名对照关系。
	// 这些字段的内容是一个对象，每当请求某个键名时，就会映射到对应的键值。

	extensions: [".js", ".json"],
	// 扩展名

	moduleExtensions: ["-loader"],
	//模块后缀名：在解析一个模块名时，将会尝试附加这些后缀名

	enforceModuleExtension: false,
	// If false it's also try to use no module extension from above

	alias: {
		jquery: path.resolve(__dirname, "vendor/jquery-2.0.0.js")
	}
	// 请求重定向，在解析一个模块名时，会使用这个别名映射表

## Minor breaking changes

### Promise polyfill

分块加载机制现在是依赖于 Promise 的。这表示你需要在旧版浏览器下提供一个 Promise 的 polyfill。

### other polyfills
- 你需要一个 Object.defineProperty 的 polyfill 来实现 ES6 的模块特性。或者在以（除了 module.exports、module.id、module.loaded 或 module.hot 之外的）其它方式使用 module 对象时，这个 polyfill 也是需要的。
- 为了实现 ES6 的模块特性，你还需要一个 Function.prototype.bind 的 polyfill
- 你需要一个 Object.keys 的 polyfill 来运行 require.context().keys()

## Loaders configuration
```
loaders: [
	{
		test: /\.css$/,
		loaders: [
			"style-loader",
			{ loader: "css-loader", query: { modules: true } },
			{
				loader: "sass-loader",
				query: {
					includePaths: [
						path.resolve(__dirname, "some-folder")
					]
				}
			}
		]
	}
]
```

### Loader 选项以及代码压缩
UglifyJsPlugin 将不再把所有 loader 都切到代码压缩模式。debug 选项已经被移除。Loader 不应该再从 Webpack 的配置那里读取自己选项了。取而代之的是，你需要通过 LoaderOptionsPlugin 来提供这些选项。
```
new webpack.LoaderOptionsPlugin({
	test: /\.css$/, // optionally pass test, include and exclude, default affects all loaders
	                // 可以传入 test、include 和 exclude，默认会影响所有的 loader
	minimize: true,
	debug: false,
	options: {
		// pass stuff to the loader
		// 这里的选项会传给 loader
	}
})
```

## Plugins

现在许多插件将可以接受一个选项对象，而不是以前多个参数的形式,

```
/*
配置webpack插件
plugin和loader的区别是, loader是在import时根据不同的文件名, 匹配不同的loader对这个文件做处理,
而plugin, 关注的不是文件的格式, 而是在编译的各个阶段, 会触发不同的事件, 让你可以干预每个编译阶段.
简而言之： loader关注文件名，plugin关注事件
*/
 module: {
    /*
    配置各种类型文件的加载器, 称之为loader
    webpack当遇到import ... 时, 会调用这里配置的loader对引用的文件进行编译
    */
    rules: [
      {
        /*
        使用babel编译ES6/ES7/ES8为ES5代码
        使用正则表达式匹配后缀名为.js的文件
        */
        test: /\.js$/,

        // 排除node_modules目录下的文件, npm安装的包不需要编译
        exclude: /node_modules/,

        /*
        use指定该文件的loader, 值可以是字符串或者数组.
        这里先使用eslint-loader处理, 返回的结果交给babel-loader处理. loader的处理顺序是从最后一个到第一个.
        eslint-loader用来检查代码, 如果有错误, 编译的时候会报错.
        babel-loader用来编译js文件.
        */
        use: ['babel-loader', 'eslint-loader']
      },

      {
        // 匹配.html文件
        test: /\.html$/,
        /*
        使用html-loader, 将html内容存为js字符串, 比如当遇到
        import htmlString from './template.html'
        template.html的文件内容会被转成一个js字符串, 合并到js文件里.
        */
        use: 'html-loader'
      },

      {
        // 匹配.css文件
        test: /\.css$/,

        /*
        先使用css-loader处理, 返回的结果交给style-loader处理.
        css-loader将css内容存为js字符串, 并且会把background, @font-face等引用的图片,
        字体文件交给指定的loader打包, 类似上面的html-loader, 用什么loader同样在loaders对象中定义, 等会下面就会看到.
        */
        use: ['style-loader', 'css-loader']
      },

      {
        /*
        匹配各种格式的图片和字体文件
        上面html-loader会把html中<img>标签的图片解析出来, 文件名匹配到这里的test的正则表达式,
        css-loader引用的图片和字体同样会匹配到这里的test条件
        */
        test: /\.(png|jpg|jpeg|gif|eot|ttf|woff|woff2|svg|svgz)(\?.+)?$/,

        /*
        使用url-loader, 它接受一个limit参数, 单位为字节(byte)

        当文件体积小于limit时, url-loader把文件转为Data URI的格式内联到引用的地方
        当文件大于limit时, url-loader会调用file-loader, 把文件储存到输出目录, 并把引用的文件路径改写成输出后的路径

        比如 views/foo/index.html中
        <img src="smallpic.png">
        会被编译成
        <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAA...">

        而
        <img src="largepic.png">
        会被编译成
        <img src="/f78661bef717cf2cc2c2e5158f196384.png">
        */
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10000
            }
          }
        ]
      }
    ]
  },

  /*
  配置webpack插件
  plugin和loader的区别是, loader是在import时根据不同的文件名, 匹配不同的loader对这个文件做处理,
  而plugin, 关注的不是文件的格式, 而是在编译的各个阶段, 会触发不同的事件, 让你可以干预每个编译阶段.
  */
  plugins: [
    /*
    html-webpack-plugin用来打包入口html文件
    entry配置的入口是js文件, webpack以js文件为入口, 遇到import, 用配置的loader加载引入文件
    但作为浏览器打开的入口html, 是引用入口js的文件, 它在整个编译过程的外面,
    所以, 我们需要html-webpack-plugin来打包作为入口的html文件
    */
    new HtmlWebpackPlugin({
      /*
      template参数指定入口html文件路径, 插件会把这个文件交给webpack去编译,
      webpack按照正常流程, 找到loaders中test条件匹配的loader来编译, 那么这里html-loader就是匹配的loader
      html-loader编译后产生的字符串, 会由html-webpack-plugin储存为html文件到输出目录, 默认文件名为index.html
      可以通过filename参数指定输出的文件名
      html-webpack-plugin也可以不指定template参数, 它会使用默认的html模板.
      */
      template: './src/index.html'
    }),
    new webpack.ProvidePlugin({$: 'jquery', jQuery: 'jquery', 'window.jQuery': 'jquery'}),
  ],

```

[参考](https://github.com/dwqs/blog/issues/46)
[参考](https://github.com/cssmagic/blog/issues/58)
