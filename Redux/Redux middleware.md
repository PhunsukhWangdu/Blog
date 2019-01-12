##Redux

####三大原则

* 单一数据源

	Redux里只有一个单一数据源，所有对象的状态都保存在一个对象中，这样我们可以随时提取整个应用的状态进行持久化
* 状态是只读的

	reducer可以根据当前触发的action对当前的状态进行迭代，这里并不是修改应用的状态，而是返回一个全新的状态。
	redux提供的createStore会根据reducer生成store，最后可以利用store.dispatch方法来达到修改state的目的。
	
* 状态修改均由纯函数完成

	redux中通过reducer来确定状态的修改，每个reducer都是一个纯函数，没有副作用，即接受一定的输入，必会得到一定的输出
	
Redux是一个可预测的状态容器

#### Redux middleware

* 一个在dispatch action和在reducer接收之前的一层中间处理，可以检阅每一个传进来的action并进行相应的操作处理，决定其被reducer接受的最终形态

* 一个可以组合，自由插拔的插件机制，为了增强dispatch

* 每个middleware处理一个相对独立的业务，通过串联不同的middleware实现多样的功能