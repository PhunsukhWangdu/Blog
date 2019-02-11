## es6 import 按需加载

import命令会被 JavaScript 引擎静态分析，先于模块内的其他模块执行（叫做”连接“更合适）。所以，下面的代码会报错。

	// 报错
	if (x === 2) {
	import MyModual from './myModual';
	}

上面代码中，引擎处理import语句是在编译时，这时不会去分析或执行if语句，所以import语句放在if代码块之中毫无意义，因此会报句法错误SyntaxError，而不是执行时错误。

也就是说，import和export命令只能在模块的顶层，不能在代码块之中（比如，在if代码块之中，或在函数之中）。

这样的设计，固然有利于编译器提高效率，但也导致无法在运行时加载模块。在语法上，条件加载就不可能实现。如果import命令要取代 Node 的require方法，这就形成了一个障碍。因为require是运行时加载模块，import命令无法取代require的动态加载功能。

	const path = './' + fileName;
	const myModual = require(path);

上面的语句就是动态加载，require到底加载哪一个模块，只有运行时才知道。import语句做不到这一点。

因此，有一个提案，建议引入import()函数，完成动态加载。

import()函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，也会加载指定的模块。另外，import()函数与所加载的模块没有静态连接关系，这点也是与import语句不相同。

import()类似于 Node 的require方法，区别主要是前者是异步加载，后者是同步加载。

适用场合
下面是import()的一些适用场合。

（1）按需加载。

import()可以在需要的时候，再加载某个模块。

	button.addEventListener('click', event => {
		import('./dialogBox.js')
	.then(dialogBox => {
		dialogBox.open();
	})
	.catch(error => {
		/* Error handling */
	})
	});
上面代码中，import()方法放在click事件的监听函数之中，只有用户点击了按钮，才会加载这个模块。

（2）条件加载

import()可以放在if代码块，根据不同的情况，加载不同的模块。

	if (condition) {
	import('moduleA').then(...);
	} else {
	import('moduleB').then(...);
	}
上面代码中，如果满足条件，就加载模块 A，否则加载模块 B。

（3）动态的模块路径

import()允许模块路径动态生成。

	import(f())
	.then(...);

上面代码中，根据函数f的返回结果，加载不同的模块。