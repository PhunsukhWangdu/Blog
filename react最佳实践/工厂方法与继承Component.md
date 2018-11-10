React最佳实践

react是一种声明式的编程方式

无需关心与dom的交互操作，只要告诉react你希望在屏幕上看到什么

核心是元素，一个数据结构，type告诉react这个组件是什么，string的话那就是dom节点，函数的话就是组件，react回去调用它，传入props得到返回的元素，递归操作直到得到完整的dom树

##1.createClass与继承React.Component
	const Button = React.createClass({
	      propTypes: {
	        text: React.PropTypes.string,
	      },
	      getDefaultProps() {
	        return {
	          text: 'Click me!',
	        }
	},
	      render() {
	        return <button>{this.props.text}</button>
	}, })


	class Button extends React.Component {
	  render() {
	    return <button>{this.props.text}</button>
	  }
	}
	Button.propTypes = {
	  text: React.PropTypes.string,
	}
	Button.defaultProps = {
	  text: 'Click me!',
	}

使用类的好处在于，只需要在 JavaScript 对象上定义属性，无须使用 getDefaultProps 这种 React 特有的函数，同理getInitialState

在 ES2015 中，若想在子类中使用 this，必须先调用 super 方法。React 还会将 props 对象 传给父组件。

在createClass中，直接允许我们创造事件处理器，并且this会指向本身

    const Button = React.createClass({
      handleClick() {
        console.log(this)
      },
      render() {
        return <button onClick={this.handleClick} />
	}, })


而class中，需要手动绑定，否则会丢失实例对象

	class Button extends React.Component {
	      handleClick() {
	      console.log(this)
	  }
	  render() {
	    return <button onClick={this.handleClick} />
	} }
函数传给时间处理器后，丢失了对组件实例的引用

ES6的箭头函数，可以将当前的this绑定到函数体

	() => this.setState()
Babel 会将以上代码转译为以下代码:

	var _this = this;
	(function () {
	  return _this.setState();
	});

所以借用箭头函数，绑定this

	class Button extends React.Component {
	handleClick() { 8
	    console.log(this)
	  }
	  render() {
	    return <button onClick={() => this.handleClick()} />
	} }
可是这样，每次组件渲染时就会触发函数，导致子组件在父组件每次更新的时候都接收到新的props，导致低效的渲染，尤其是在纯函数组件的方面。解决办法就是在构造函数中绑定，这样多次渲染也不会改变