## ES6 模块与 CommonJS 模块的差异

CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。

ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的import有点像 Unix 系统的“符号连接”，原始值变了，import加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块

ES6 输入的模块变量，只是一个“符号连接”，所以这个变量是只读的，对它进行重新赋值会报错

首先，就是this关键字。ES6 模块之中，顶层的this指向undefined；CommonJS 模块的顶层this指向当前模块，这是两者的一个重大差异。

## CommonJS 模块的循环加载
CommonJS 模块的重要特性是加载时执行，即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。
让我们来看，Node 官方文档里面的例子。

脚本文件a.js代码如下。

	exports.done = false;var b = require('./b.js');
	console.log('在 a.js 之中，b.done = %j', b.done);
	exports.done = true;
	console.log('a.js 执行完毕');

上面代码之中，a.js脚本先输出一个done变量，然后加载另一个脚本文件b.js。注意，此时a.js代码就停在这里，等待b.js执行完毕，再往下执行。

再看b.js的代码。

	exports.done = false;var a = require('./a.js');
	console.log('在 b.js 之中，a.done = %j', a.done);
	exports.done = true;
	console.log('b.js 执行完毕');

上面代码之中，b.js执行到第二行，就会去加载a.js，这时，就发生了“循环加载”。系统会去a.js模块对应对象的exports属性取值，可是因为a.js还没有执行完，从exports属性只能取回已经执行的部分，而不是最后的值。

a.js已经执行的部分，只有一行。

	exports.done = false;

因此，对于b.js来说，它从a.js只输入一个变量done，值为false。
然后，b.js接着往下执行，等到全部执行完毕，再把执行权交还给a.js。于是，a.js接着往下执行，直到执行完毕。我们写一个脚本main.js，验证这个过程。

	var a = require('./a.js');var b = require('./b.js');
	console.log('在 main.js 之中, a.done=%j, b.done=%j', a.done, b.done);
	执行main.js，运行结果如下。
	
	$ node main.js
	
	在 b.js 之中，a.done = false
	b.js 执行完毕
	在 a.js 之中，b.done = true
	a.js 执行完毕
	在 main.js 之中, a.done=true, b.done=true

上面的代码证明了两件事。一是，在b.js之中，a.js没有执行完毕，只执行了第一行。二是，main.js执行到第二行时，不会再次执行b.js，而是输出缓存的b.js的执行结果，即它的第四行。
exports.done = true;
总之，CommonJS 输入的是被输出值的拷贝，不是引用。
另外，由于 CommonJS 模块遇到循环加载时，返回的是当前已经执行的部分的值，而不是代码全部执行后的值，两者可能会有差异。所以，输入变量的时候，必须非常小心。

	var a = require('a'); // 安全的写法
	var foo = require('a').foo; // 危险的写法
	exports.good = function (arg) {
	return a.foo('good', arg); // 使用的是 a.foo 的最新值
	};
	
	exports.bad = function (arg) {
	return foo('bad', arg); // 使用的是一个部分加载时的值
	};
上面代码中，如果发生循环加载，require('a').foo的值很可能后面会被改写，改用require('a')会更保险一点。

##ES6 模块的循环加载

ES6 处理“循环加载”与 CommonJS 有本质的不同。ES6 模块是动态引用，如果使用import从一个模块加载变量（即import foo from 'foo'），那些变量不会被缓存，而是成为一个指向被加载模块的引用，需要开发者自己保证，真正取值的时候能够取到值。

请看下面这个例子。

	// a.mjs
	import {bar} from './b';
	console.log('a.mjs');
	console.log(bar);
	export let foo = 'foo';
	// b.mjs
	import {foo} from './a';
	console.log('b.mjs');
	console.log(foo);
	export let bar = 'bar';

上面代码中，a.mjs加载b.mjs，b.mjs又加载a.mjs，构成循环加载。

执行a.mjs，结果如下。
	
	$ node --experimental-modules a.mjs
	b.mjs
	ReferenceError: foo is not defined
上面代码中，执行a.mjs以后会报错，foo变量未定义，这是为什么？
让我们一行行来看，ES6 循环加载是怎么处理的。

首先，执行a.mjs以后，引擎发现它加载了b.mjs，因此会优先执行b.mjs，然后再执行a.mjs。接着，执行b.mjs的时候，已知它从a.mjs输入了foo接口，这时不会去执行a.mjs，而是认为这个接口已经存在了（就是一个指向a的foo的引用），继续往下执行。执行到第三行console.log(foo)的时候，才发现这个接口根本没定义，因此报错。

解决这个问题的方法，就是让b.mjs运行的时候，foo已经有定义了。这可以通过将foo写成函数来解决。

	// a.mjs
	import {bar} from './b';
	console.log('a.mjs');
	console.log(bar());function foo() { return 'foo' }
	export {foo};
	// b.mjs
	import {foo} from './a';
	console.log('b.mjs');
	console.log(foo());function bar() { return 'bar' }
	export {bar};
	这时再执行a.mjs就可以得到预期结果。

	$ node --experimental-modules a.mjs
	b.mjs
	foo
	a.mjs
	bar

这是因为函数具有提升作用，在执行import {bar} from './b'时，函数foo就已经有定义了，所以b.mjs加载的时候不会报错。

这也意味着，如果把函数foo改写成函数表达式，也会报错。