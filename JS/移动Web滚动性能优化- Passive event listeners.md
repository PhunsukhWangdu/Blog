## addEventListener 第三个参数

	target.addEventListener(type, listener[, options]);
	target.addEventListener(type, listener[, useCapture]);

addEventListener，如果为同一个行为添加多个事件处理程序，如果传入的是匿名函数或者
不同的函数，则会将事件依次绑定在监听的行为上

如果传入的事件处理函数是同一个函数（指向一个引用），那么这个事件只会绑定一次

如果我们定义了一个函数b，并作为事件监听函数传入，之后再为这个行为添加监听函数b，也只会触发一次，但是如果我们修改了b的指向，指向了一个新的函数，再作为时间监听函数传入时，老的b和新的b都会触发

可以理解为，只要传入了一个新的函数（不是内容，而是地址上的新的，内容相同的匿名函数算不同的函数），那么都会绑定到行为上。

------------

#### 移动Web滚动性能优化: Passive event listeners

在事件处理函数中，会传递 Event 对象作为参数，而这个参数最常用的 2 个方法就是 event.preventDefault() 和 event.stopPropagation()。 

* stopPropagation() 阻止事件传播
* preventDefault() 阻止事件的默认行为


在移动网页中，我们经常使用的就是 touch 系列的事件，如：

* touchstart 
* touchmove
* touchend
* touchcancel

a标签默认行为是跳转链接，如果在a标签的onclick事件中执行event.preventDefalut()，那么就解除了这个默认行为

如果在touchstart上调用preventDefault，浏览器无法预先知道事件监听器上是否有阻止默认行为的操作，只能等时间处理程序执行完，而监听器执行是需要时间的，即使是监听一个空函数，空函数执行也是需要耗时的，而这个期间页面是不滚动的，所以就会有卡顿的现象

当你触摸滑动页面时，页面应该跟随手指一起滚动。而此时你绑定了一个 touchstart 事件，你的事件大概执行 200 毫秒。这时浏览器就犯迷糊了：如果你在事件绑定函数中调用了 preventDefault，那么页面就不应该滚动，如果你没有调用 preventDefault，页面就需要滚动。但是你到底调用了还是没有调用，浏览器不知道。只能先执行你的函数，等 200 毫秒后，绑定事件执行完了，浏览器才知道，“哦，原来你没有阻止默认行为，好的，我马上滚”。此时，页面开始滚。

如果 Web 开发者能够提前告诉浏览器：“我不调用 preventDefault 函数来阻止事件事件行为”，那么浏览器就能快速生成事件，从而提升页面性能。

在最新的 DOM 规范中，事件绑定函数的第三个参数变成了对象：

	target.addEventListener(type, listener[, options]);
	
我们可以通过传递 passive 为 true 来明确告诉浏览器，事件处理程序不会调用 preventDefault 来阻止默认滑动行为。

在 Chrome 浏览器中，如果发现耗时超过 100 毫秒的非 passive 的监听器，会在 DevTools 里面警告你加上 {passive: true}。

添加了passive:true后，即使在事件处理函数中添加了preventdefault，preventDefaut也会无效

#### 其余的参数

	addEventListener(type, listener, {
	    capture: false,
	    passive: false,
	    once: false
	})

* capture：Boolean，true为捕获，false冒泡，默认false
* once：Boolean，表示 listener 在添加之后最多只调用一次。如果是 true， listener 会在其被调用之后自动移除
* passive: Boolean，表示 listener 永远不会调用 preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告
 