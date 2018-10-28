# 服务器端渲染 SSR

客户端渲染

往往从服务端获取到的html文件都很简洁，就一个根节点，具体需要运行js才得到需要渲染到页面的dom

	<!doctype html>
	<html>
	  <head>
	    <title>我是客户端渲染的页面</title>
	  </head>
	  <body>
	    <div id='root'></div>
	    <script src='index.js'></script>
	  </body>
	</html>
特点：原文件中找不到最后展示的页面内容

服务器端渲染

请求到的就是要在页面展现的内容，服务器把组要的组件活页面以HTML字符串形式返回，客户端收到后直接渲染，不需要去执行js获取展现内容

服务器端渲染解决的问题：

客户端渲染的页面，无法被seo搜索引擎抓取内容
首屏加载速度过慢——下载html，下载js，运行js渲染页面——服务器帮忙生成页面，客户端请求到的可以直接在页面进行渲染展现

什么是服务器端渲染(SSR)？
Vue.js 是构建客户端应用程序的框架。默认情况下，可以在浏览器中输出 Vue 组件，进行生成 DOM 和操作 DOM。然而，也可以将同一个组件渲染为服务器端的 HTML 字符串，将它们直接发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序。

服务器渲染的 Vue.js 应用程序也可以被认为是"同构"或"通用"，因为应用程序的大部分代码都可以在服务器和客户端上运行。


# 为什么使用服务器端渲染(SSR)？
https://ssr.vuejs.org/zh/#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%B8%B2%E6%9F%93-ssr-%EF%BC%9F

与传统 SPA（Single-Page Application - 单页应用程序）相比，服务器端渲染(SSR)的优势主要在于：

更好的 SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。

请注意，截至目前，Google 和 Bing 可以很好对同步 JavaScript 应用程序进行索引。在这里，同步是关键。如果你的应用程序初始展示 loading 菊花图，然后通过 Ajax 获取内容，抓取工具并不会等待异步完成后再行抓取页面内容。也就是说，如果 SEO 对你的站点至关重要，而你的页面又是异步获取内容，则你可能需要服务器端渲染(SSR)解决此问题。

更快的内容到达时间(time-to-content)，特别是对于缓慢的网络情况或运行缓慢的设备。无需等待所有的 JavaScript 都完成下载并执行，才显示服务器渲染的标记，所以你的用户将会更快速地看到完整渲染的页面。通常可以产生更好的用户体验，并且对于那些「内容到达时间(time-to-content)与转化率直接相关」的应用程序而言，服务器端渲染(SSR)至关重要。

使用服务器端渲染(SSR)时还需要有一些权衡之处：

开发条件所限。浏览器特定的代码，只能在某些生命周期钩子函数(lifecycle hook)中使用；一些外部扩展库(external library)可能需要特殊处理，才能在服务器渲染应用程序中运行。

涉及构建设置和部署的更多要求。与可以部署在任何静态文件服务器上的完全静态单页面应用程序(SPA)不同，服务器渲染应用程序，需要处于 Node.js server 运行环境。

更多的服务器端负载。在 Node.js 中渲染完整的应用程序，显然会比仅仅提供静态文件的 server 更加大量占用 CPU 资源(CPU-intensive - CPU 密集)，因此如果你预料在高流量环境(high traffic)下使用，请准备相应的服务器负载，并明智地采用缓存策略。

在对你的应用程序使用服务器端渲染(SSR)之前，你应该问的第一个问题是，是否真的需要它。这主要取决于内容到达时间(time-to-content)对应用程序的重要程度。例如，如果你正在构建一个内部仪表盘，初始加载时的额外几百毫秒并不重要，这种情况下去使用服务器端渲染(SSR)将是一个小题大作之举。然而，内容到达时间(time-to-content)要求是绝对关键的指标，在这种情况下，服务器端渲染(SSR)可以帮助你实现最佳的初始加载性能

react中实践：

VDom.js:

	import React from 'react'
	
	const VDom = () => {
	  return <div>我是一个被渲染为真实DOM的虚拟DOM</div>
	}
	
	export default VDom
在服务端的入口文件中，我引入这个组件，对它进行渲染：

	import express from 'express'
	import React from 'react'
	import { renderToString } from 'react-dom/server'
	import VDom from './VDom'
	
	// 创建一个express应用
	const app = express()
	// renderToString 是把虚拟DOM转化为真实DOM的关键方法
	const RDom = renderToString(<VDom />)
	// 编写HTML模板，插入转化后的真实DOM内容
	const Page = `
	            <html>
	              <head>
	                <title>test</title>
	              </head>
	              <body>
	                <span>服务端渲染出了真实DOM:  </span>
	                ${RDom}
	              </body>
	            </html>
	            `
	            
	// 配置HTML内容对应的路由
	app.get('/index', function(req, res) {
	  res.send(Page)
	})
	
	// 配置端口号
	const server = app.listen(8000)

VDom 组件会被 renderToString 转化为了一个内容为<div data-reactroot="">我是一个被渲染为真实DOM的虚拟DOM</div>的字符串，这个字符串被插入 HTML 代码，成为了真实 DOM 树的一部分

vue中的实践
	
	const Vue = require('vue')
	// 创建一个express应用
	const server = require('express')()
	// 提取出renderer实例
	const renderer = require('vue-server-renderer').createRenderer()
	
	server.get('*', (req, res) => {
	  // 编写Vue实例（虚拟DOM节点）
	  const app = new Vue({
	    data: {
	      url: req.url
	    },
	    // 编写模板HTML的内容
	    template: `<div>访问的 URL 是： {{ url }}</div>`
	  })
	    
	  // renderToString 是把Vue实例转化为真实DOM的关键方法
	  renderer.renderToString(app, (err, html) => {
	    if (err) {
	      res.status(500).end('Internal Server Error')
	      return
	    }
	    // 把渲染出来的真实DOM字符串插入HTML模板中
	    res.end(`
	      <!DOCTYPE html>
	      <html lang="en">
	        <head><title>Hello</title></head>
	        <body>${html}</body>
	      </html>
	    `)
	  })
	})
	
	server.listen(8080)
	
但是把这部分放到服务器会使服务器压力过大，毕竟浏览器是用户人手一台，首屏渲染体验和 SEO 的优化方案却很多，不到万不得已可以先不要考虑服务器端渲染