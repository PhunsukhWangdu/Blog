##Flux Standard Action

#### Flux Standard Action

action在Flux架构中是及其重要的概念，它是应用状态变化的必要条件，所有的状态都必须通过action触发。action的角色是状态变更信息的载体，是一个object，包含一个表示action type的字段，这是Flux对action的全部要求。不同于Flux作为架构思想的宽泛要求，在实际的开发中，我们往往希望打交道的同类实物有着类似的接口/结构。

Flux Standard Action的定位是“一个用户友好的Flux action对象标准”，它希望通过规范action的格式，为通用的action工具或抽象的实现奠定基础。换句话说，如果所有的action都有着类似的结构，那么通过统一的方法创建或修改action，或者通过统一的逻辑对action进行分析与响应等，便有了存在的可能性。

#### FSA结构

典型的Flux Standard Action结构如下：

	{
	    type: 'ADD_TODO',
	    payload: {
	        text: 'Do something.'
	    }
	}
	
不难发现，FSA首先是一个普通的action，鼓励我们将负载信息放到payload字段中。基于这样的初步认识，了解一下它的规范。

* 一个action必须是一个普通的JavaScript对象，有一个type字段。
* 一个action可能有error字段、payload字段、meta字段。
* 一个action必须不能包含除type、payload、error及meta以外的其他字段。

#### type
必需字段。action的type字段标识了当前发生行为的本质特征。相同类型的行为所对应的action的type值必须是严格相等的。它往往取值为字符串常量。

#### payload
可选字段。可以是任意类型的数据，顾名思义，它存放当前action的“负载”内容。当error字段值为true时候，payload的值应当是一个Error对象。

#### error
可选字段。当取值为true时，当前action代表了某处发生了错误。

#### meta
可选字段。可以是任意类型的数据。用来存放非负载内容的额外信息。在Redux项目中，典型的使用meta的例子就是存放那些用来给middleware使用的信息，理论上meta的内容不会影响reducer的行为。

正如前面提到的，基于相同的action结构，提取action操作的公共逻辑会更加方便，redux-actions、redux-promise等都是在FSA基础上衍生出来的action处理工具。