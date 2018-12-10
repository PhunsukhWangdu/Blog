# HMR

webpack-dev-server主要是处理我们的构建静态资源任务，同时它还会作为一个静态文件服务器提供我们最新打包成的静态文件供本地开发使用。

webpack-dev-server与我们平时开发用的静态文件服务器的不同之处在于，它知道新的文件在何时被打包了出来，以及究竟是哪个文件发生了改变。这个基础之上，webpack-dev-server可以和浏览器之间建立一个web socket进行通信，一旦新文件被打包出来，webpack-dev-server就告诉浏览器这个消息，这时浏览器就可以自动刷新页面或者进行热替换操作，而不用等到开发者手动刷新。

---
Hot Module Replacement模块热替换，相比hot reloading，采用局部刷新，通知浏览器变更的代码，进行局部替换。

安装好webpack-dev-server后：

	const webpack = require('webpack')
	
	module.exports = {
	  // ...
	  devServer: {
	    hot: true // dev server 的配置要启动 hot，或者在命令行中带参数开启
	  },
	  plugins: [
	    // ...
	    new webpack.NamedModulesPlugin(), // 用于启动 HMR 时可以显示模块的文件名而不是id
	    new webpack.HotModuleReplacementPlugin(), // Hot Module Replacement 的插件
	  ],
	}
	
究其原因，主要是webpack的热替换原理是这样的：
当一个模块b发生改变，而模块内又没有HMR代码来处理这一消息时，那这一消息就会被传递到依赖模块b的其他模块上；如果消息在新模块上没有被捕获的话就会再次进行传递；如果所有的消息都被捕获了的话，那我们的应用就应该已经按照代码进行了更新；反之如果有消息冒泡到了入口(entry)文件还没有被捕获的话，那就说明我们的代码中没有处理这类变更方法，那webpack就会刷新浏览器页面，即从HMR回退到LiveReload。


