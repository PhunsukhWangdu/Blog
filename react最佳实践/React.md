## React

#### ReactElement和ReactClass

#### ReactElement
一个描述DOM节点或component实例的字面级对象。它包含一些信息，包括组件类型type和属性props。就像一个描述DOM节点的元素（虚拟节点）。它们可以被创建通过React.createElement方法或jsx写法

分为DOM Element和Component Elements两类：

* DOM Elements

	当节点的type属性为字符串时，它代表是普通的节点，如div,span
	
		{
		  type: 'button',
		  props: {
		    className: 'button button-blue',
		    children: {
		      type: 'b',
		      props: {
		        children: 'OK!'
		      }
		    }
		  }
		}
		
* Component Elements

	当节点的type属性为一个函数或一个类时，它代表自定义的节点

		class Button extends React.Component {
		  render() {
		    const { children, color } = this.props;
		    return {
		      type: 'button',
		      props: {
		        className: 'button button-' + color,
		        children: {
		          type: 'b',
		          props: {
		            children: children
		          }
		        }
		      }
		    };
		  }
		}
		
		// Component Elements
		{
		  type: Button,
		  props: {
		    color: 'blue',
		    children: 'OK!'
		  }
		}

####ReactClass

ReactClass是平时我们写的Component组件(类或函数)，例如上面的Button类。ReactClass实例化后调用render方法可返回DOM Element。

