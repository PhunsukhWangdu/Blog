#React16
---
#### 新的生命周期

![](https://i.imgur.com/TThYlQy.png)

**React16废弃的三个生命周期函数**

- componentWillMount
- componentWillReceiveProps
- componentWillUpdate

目前在16版本中componentWillMount，componentWillReceiveProps，componentWillUpdate并未完全删除这三个生命周期函数，而且新增了UNSAFE_componentWillMount，UNSAFE_componentWillReceiveProps，UNSAFE_componentWillUpdate三个函数，官方计划在17版本完全删除这三个函数，只保留UNSAVE_前缀的三个函数，目的是为了向下兼容，但是对于开发者而言应该尽量避免使用他们，而是使用新增的生命周期函数替代它们


#### Fiber

####ReactDOM.createPortal

一般的react结构中，子组件都是渲染在父组件内部，但某些情况下这样的嵌套关系会导致问题，例如模态框，通常都应该被渲染在body下

	render() {
	  // React 会在你提供的 domNode 下渲染，而不是在当前组件所在的 DOM
	  return ReactDOM.createPortal(
	    this.props.children,
	    domNode,
	  );
	}


####Render返回数组 字符串

render中不再要求只返回一个元素

	render() {
	  return 'Look ma, no spans!';
	}

	render() {
	  // 不需要再包裹一层了！
	  return [
	    // 但是要使用 key
	    <li key="A">First item</li>,
	    <li key="B">Second item</li>,
	    <li key="C">Third item</li>,
	  ];
	}

#### 错误处理componentDidCatch(error, info)

以前react运行错误会导致应用崩溃，现在组件增加componentDidCatch(error, info)生命周期函数，可以通过定义该错误处理函数，组件在运行时的错误会被catch住，作出相应处理保证页面不崩


####体积更小

16之前161kb，16缩小30% 109kb

#### 部分非核心模块被移出

React.createClass、React.PropTypes 等模块被移出了 react 包，现在你必须从单独的包里引入。

参考：https://juejin.im/post/5b6f1800f265da282d45a79a