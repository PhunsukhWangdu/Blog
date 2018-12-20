# js 中 toString( ) 和valueOf( )

-------
## toString()方法

主要用于Array、Boolean、Date、Error、Function、Number等对象转化为字符串形式。

日期类的toString()方法返回一个可读的日期和字符串。

#### 数组形式：

	  var array = ["CodePlayer", true, 12, -5];  
	  console. log( array.toString() );  
  
输出的形式为：
  CodePlayer,true,12,-5
 

#### 日期形式:

	var date = new Date(2016, 9, 3, 23, 11, 59, 230);  
	console. log( date.toString() );   

输出的形式是：

Mon Oct 03 2016 23:11:59 GMT+0800 (CST)// (中国标准时间)  

#### 数字形式：

	var num =  15.26540;  
	consoel.log( num.toString() );  

输出的形式是：15.2654  

#### Object:

	var obj = {name: "张三", age: 18};  
	console. log( obj.toString() );   

输出的形式是：[object Object]  
 

#### 布尔形式：

	var bool = true;  
	console. log( bool.toString() );  

输出的形式是：true  

null和undefined不能调用toString方法

	null.toString()
	//VM182:1 Uncaught TypeError: Cannot read property 'toString' of null

	undefined.toString()
	//VM203:1 Uncaught TypeError: Cannot read property 'toString' of undefined

但是string方法可以将其转化为字符串

	String(null)
	//"null"
	String(undefined)
	"undefined"
##valueOf()方法

如果存在任意原始值，它就默认将对象转换为表示它的原始值。

对象是复合值，而且大多数对象无法真正表示为一个原始值，因此默认的valueOf()方法简单地返回对象本身，而不是返回一个原始值。

数组、函数和正则表达式简单地继承了这个默认方法，调用这些类型的实例的valueOf()方法只是简单返回这个对象本身。

日期类定义的valueOf()方法会返回它的一个内部表示：1970年1月1日以来的毫秒数.

	var d = new Date(2016,9,2);   
	d.valueOf();
   
输出的是：1475337600000  