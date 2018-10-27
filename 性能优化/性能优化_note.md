从输入 URL 到页面加载完成，发生了什么？

my: 域名解析，先看缓存，没有的话服务器逐层向上查找，拿到ip后，返回本地服务器，向这个ip发送请求，ngnix转发，与服务器建立连接，三次握手（ack??)send,request,establish,然后开始发送请求，四次握手请求关闭

DNS 解析
TCP 连接
HTTP 请求抛出
服务端处理请求，HTTP 响应返回
浏览器拿到响应数据，解析响应内容，把解析的结果展示给用户

性能优化逃不出上面五个步骤

浏览器端的性能优化——这部分涉及资源加载优化、服务端渲染、浏览器缓存机制的利用、DOM 树的构建、网页排版和渲染过程、回流与重绘的考量、DOM 操作的合理规避等等——这正是前端工程师可以真正一展拳脚的地方

切入点： 网络层面和渲染层面

webpack 性能调优与 Gzip 原理

HTTP优化核心：
1.减少请求数——合并
2.提高响应速度——压缩 

工具：webpack

webpack 的构建过程太花时间
webpack 打包的结果体积太大

externals有时候还是会打包进来，commonsChunkPlugin每次都要重新构建vender，处于效率，引入DllPlugin

my：所有依赖库打入一个文件，另外维护一个指向这个依赖库内容的映射表，这样依赖不会跟随业务来打包，只有当依赖包发生变化的时候才需要重新打包

以一个基于 React 的简单项目为例，我们的 dll 的配置文件可以编写如下：

	const path = require('path')
	const webpack = require('webpack')
	
	module.exports = {
	    entry: {
	      // 依赖的库数组
	      vendor: [
	        'prop-types',
	        'babel-polyfill',
	        'react',
	        'react-dom',
	        'react-router-dom',
	      ]
	    },
	    output: {
	      path: path.join(__dirname, 'dist'),
	      filename: '[name].js',
	      library: '[name]_[hash]',
	    },
	    plugins: [
	      new webpack.DllPlugin({
	        // DllPlugin的name属性需要和libary保持一致
	        name: '[name]_[hash]',
	        path: path.join(__dirname, 'dist', '[name]-manifest.json'),
	        // context需要和webpack.config.js保持一致
	        context: __dirname,
	      }),
	    ],
	}...

DllPlugin把vendor打包的所有依赖做了一个映射表到vendor-mainfest.json中

vendor-manifest.json，则用于描述每个第三方库对应的具体路径

	{
	  "name": "vendor_397f9e25e49947b8675d",
	  "content": {
	    "./node_modules/core-js/modules/_export.js": {
	      "id": 0,
	        "buildMeta": {
	        "providedExports": true
	      }
	    },
	    "./node_modules/prop-types/index.js": {
	      "id": 1,
	        "buildMeta": {
	        "providedExports": true
	      }
	    },
	    ...
	  }
	}...

webpack.config.js

	const path = require('path');
	const webpack = require('webpack')
	module.exports = {
	  mode: 'production',
	  // 编译入口
	  entry: {
	    main: './src/index.js'
	  },
	  // 目标文件
	  output: {
	    path: path.join(__dirname, 'dist/'),
	    filename: '[name].js'
	  },
	  // dll相关配置
	  plugins: [
	    new webpack.DllReferencePlugin({
	      context: __dirname,
	      // manifest就是我们第一步中打包出来的json文件
	      manifest: require('./dist/vendor-manifest.json'),
	    })
	  ]
	}...

happypack?????webpack是单线程的，happypack可以利用cpu多核并发方面的优势将任务分为多个子进程去执行，提高打包的效率

	webpack-bundle-analyzer 包可视化组成分析工具
	
	const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
	 
	module.exports = {
	  plugins: [
	    new BundleAnalyzerPlugin()
	  ]
	}...

webpack2支持es6 通过import tree-shaking可以知道有效的引入和无用的代码，模块中没有真正引用的代码在打包的时候会被删除，tree-shaking针对模块级别的冗余代码，是webpack有的功能

力度更细的冗余代码的剔除，往往会整合进js和css的压缩和分离过程中，UglifyJsPlugin

webpack3对其需要引入
	const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
	module.exports = {
	 plugins: [
	   new UglifyJsPlugin({
	     // 允许并发
	     parallel: true,
	     // 开启缓存
	     cache: true,
	     compress: {
	       // 删除所有的console语句    
	       drop_console: true,
	       // 把使用多次的静态值自动定义为变量
	       reduce_vars: true,
	     },
	     output: {
	       // 不保留注释
	       comment: false,
	       // 使输出的代码尽可能紧凑
	       beautify: false
	     }
	   })
	 ]
	}...
webpack4中直接整合进去了，通过uglifyjs-webpack-plugin对代码做压缩了，在 webpack4 中，我们是通过配置 optimization.minimize 与optimization.minimizer 来自定义压缩相关的操作的

按需加载

	import BugComponent from '../pages/BugComponent'
	...
	<Route path="/bug" component={BugComponent}>
改成按需加载

	webpack:
	output: {
	    path: path.join(__dirname, '/../dist'),
	    filename: 'app.js',
	    publicPath: defaultSettings.publicPath,
	    // 指定 chunkFilename
	    chunkFilename: '[name].[chunkhash:5].chunk.js',
	},...
路由部分：

	const getComponent => (location, cb) {
	  require.ensure([], (require) => {
	    cb(null, require('../pages/BugComponent').default)
	  }, 'bug')
	},
	...
	<Route path="/bug" getComponent={getComponent}>...

核心：

	require.ensure(dependencies, callback, chunkName)
	
???getComponent和component的入参、ensure的核心实现

???react-router4的code-splitting,bundle-loader,直接实现按需加载

Gzip

在request headers 中加上这么一句：

	accept-encoding:gzip

http的压缩，服务器发送请求之前对文件进行压缩，重新编码以减小文件大小，例如对使用过多的重复字符串进行代替，

HTTP 压缩是一种内置到网页服务器和网页客户端中以改进传输速度和带宽利用率的方式。在使用 HTTP 压缩的情况下，HTTP 数据在从服务器发送前就已压缩：兼容的浏览器将在下载所需的格式前宣告支持何种方法给服务器；不支持压缩方法的浏览器将下载未经压缩的数据。最常见的压缩方案包括 Gzip 和 Deflate。

以请求签服务器压缩的开销，和接收请求后CPU解析的开销（浏览器解析压缩文件的开销），替换了传输过程中的开销

Gzip核心Deflate，http压缩最常用Gzip

webpack的Gzip和服务器端的Gzip要合理搭配，webpack的压缩就相当于是在构建的时候分担了一些服务器压缩的工作