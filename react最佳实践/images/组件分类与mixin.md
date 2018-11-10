##5.容器组件与表现组件

容器组件:
1.更关心行为部分;
2.负责渲染对应的表现组件; 
3.发起 API 请求并操作数据; 
4.定义事件处理器;
5.写作类的形式。

表现组件:
1.更关心视觉表现;
2.负责渲染 HTML 标记(或其他组件); 
3.以 props 的形式从父组件接收数据;
4.通常写作无状态函数式组件。

合理拆分以提高复用性

##6.mixin

只能和creatClass方法结合使用，用class的方式无法使用

	const WindowResize = {
		getInitialState() {
	      return {
	        innerWidth: window.innerWidth,
	      }
		},
		componentDidMount() {
			window.addEventListener('resize', this.handleResize)
		},
		handleResize() {
	      this.setState({
	        innerWidth: window.innerWidth,
	      })
		},
		componentWillUnMount() {
			window.removeEventListener('resize', this.handleResize)
		},
		...
	}


	const MyComponent = React.createClass({
		mixins: [WindowResize],
		render() {
			console.log('window.innerWidth', this.state.innerWidth)
		},
	})
	
mixin允许合并生命周期方法和初始状态,例如如果mixin和引入的组件中定义了相同的生命周期方法，两者会顺序执行

问题：
1.有时候不可避免的利用内部函数进行组件通信，因此如果mixin在组件中增添了一个新的属性，会造成组件的重新渲染，造成state过大，而且这样各个mixin之间会有耦合，互相依赖
2.react会对相同声明周期合并，但是对于同名的属性和方法无能为力