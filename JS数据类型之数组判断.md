# js数据类型判断之数组判断
---
## 引言 typeof操作符
typeof 操作符可以实现检测给定变量的数据类型，可能的返回值见下表。

| 类型 | 结果 |
| :-: | :-: |
| Undefined | "undefined" |
| Null | "object" |
| Boolean | "boolean" |
| Number | "number" |
| String | "string" |
| Function | "function" |
| Symbol （ECMAScript 6 新增） | "symbol" |
| 宿主对象（由JS环境提供）	| Implementation-dependent |
| 任何其他对象 | "object" |

其中 **null** 代表的是空指针对象(红皮书解释），所以 typeof(null) === "object"。
>摘自 MDN
>
>由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null的类型标签也成为了 0，typeof null就错误的返回了"object"

由上可知，typeof 对 null 、对象、数组返回的都是 object 类型，那么如何判断对象是数组类型？

## 1 instanceof

>摘自 MDN
>
>instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

语法为：

	result = variable instanceof constructor

举例说明：

	//定义构造函数
	var A = function() {}
	
	var o = new A();
	
	var result = o instanceof A; // true, Object.getPrototypeOf(o) === A.prototype

即 instanceof 可以用于判断一个变量是否是某个引用类型的实例，所以有

	var a = [];
	
	console.log(a instanceof Array); // true 

但注意，所有引用类型的值都是 Object 的实例，所以在检测一个引用类型值和 Object 构造函数时， instanceof 操作符永远会返回 true。

##2 constructor

constructor 返回对象相对应的构造函数,效果与 instanceof 类似。

即:

	(a.constructor == Array)  // a实例所对应的构造函数是否为Array? true or false

	(a instanceof Array)   //a是否Array的实例？true or false
	

综合1和2，一个较为严谨的通用方法是(摘自红皮书)：

	function isArray(object) {
		return object && typeof object == 'object' && Array == object.constructor;
	}

但是，instaceof 和 construcor ,被判断的 array 必须是在当前页面声明，原因如下：

>1、array属于引用型数据，传递过程中，仅仅是引用地址的传递。
>
>2、每个页面的Array原生对象所引用的地址是不一样的，在子页面声明的array，所对应的构造函数，是子页面的Array对象；父页面来进行判断，使用的Array并不等于子页面的Array。

## 3 数组特性判断

	function isArray(object){
	    return  object && typeof object==='object' &&    
	            typeof object.length==='number' &&  
	            typeof object.splice==='function' &&    
	             //判断length属性是否是可枚举的 对于数组 将得到false  
	            !(object.propertyIsEnumerable('length'));
	}

object.propertyIsEnumerable(proName) 判定指定属性（不包括原型链中的属性）是否可以枚举。

## 4 Object.prototype.toString.call()

ECMA 中对 Object.prototype.toString() 的解释如下：

>When the toString method is called, the following steps are taken:
>
>1.If the this value is undefined, return "[object Undefined]".
>
>2.If the this value is null, return "[object Null]".
>
>3.Let O be the result of calling ToObject passing the this value as the argument.
>
>4.Let class be the value of the [[Class]] internal property of O.
>
>5.Return the String value that is the result of concatenating the three Strings "[object ", class, and "]".

即调用 Object.prototype.toString.call() 会返回 [object class] 的样式，这也是 Jquery 判断数据类型的主要方式。

检测代码如下：

	function isArray(o) {
		 return Object.prototype.toString.call(o) === '[Object Array]';
	}

这种方式既解决了 instanceof 和 constructor 存在的跨页面问题，也解决了数组属性检测方式所存在的问题；Object.prototype.toString() 也可以判断其他类型的对象，如 Function 等。