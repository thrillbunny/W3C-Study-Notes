## JS继承

构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。

### 1. 原型链继承

基本思想：通过原型链，让一个引用类型继承另一个引用类型的属性和方法。简单点说就是让新实例的原型等于父类的实例。

	function Test() {
		this.name = 'thrilly';
	}
	Test.prototype.demo = function() {
		return 'test';
	}
	
	function TestChild() {
	}
	// 将 Test 实例赋给 TestChild 的原型对象
	TestChild.prototype = new Test(); 
	
	let t = new TestChild();
	// TestChild 实例能访问到 Test 对象的实例方法，也能访问到其原型属性中的方法
	t.name = 'thrilly';
	t.demo(); // 'test'

通过这一继承方法，新实例可以继承的属性包括：新实例构造函数的属性、父类构造函数的属性和父类原型上的属性（但父类实例的属性无法继承）。

缺点：
1. 继承单一；
2. 新实例无法向父类型的构造函数传参；
3. 所有新实例都会共享父类实例原型链上的属性。

### 2. 构造函数继承

基本思想：在子类型构造函数的内部调用父类构造函数。

通过使用 apply() 和 call() 方法可以在新创建的子类对象上执行构造函数，将父类构造函数引入子类中。

	function Test(age) {
		this.names = ['thrilly'];
		this.age = age;
	}

	function TestChild() {
		// 继承了 Test
		Test.call(this, 18);
	}
	
	let t1 = new TestChild();
	t.names.push('chuya');
	console.log(t1.names); // ['thrilly', 'chuya']
	console.log(t1.age); // 18
	
	let t2 = new TestChild();
	console.log(t2.names); // ['thrilly']

	console.log(t1 instanceof Test); // false

这一继承方法:
1. 只继承了父类构造函数的属性，没有继承父类原型的属性，解决了原型链继承的缺点;
2. 可以继承多个构造函数（call/apply多次）;
3. 在子类实例中可以可向父实例传参。

缺点：
1. 只能继承父类构造函数的属性；
2. 无法实现构造函数的复用。（每次都要重新调用）；
3. 每个新实例都有父类构造函数的副本，臃肿。

### 3. 组合继承（组合原型链继承和借用构造函数继承）（常用）

	function Test(name){
	　　this.name = name;
	}
	Test.prototype.HiName = function(){
	　　console.log(this.name);
	}
	
	function TestChild(name, age){
		// 第二次调用父类构造函数
	　　Test.call(this, name); // 借用构造函数模式，属性继承
		this.age = age;
	}
	// 原型链继承，继承方法
	// 第一次调用父类构造函数
	TestChild.prototype = new Test();
	TestChild.prototype.constructor = TestChild;

	TestChild.prototype.HiAge = function() {
	　　console.log(this.age);
	}
	
	let t = new TestChild('thrilly', 24);
	t.HiName(); // thrilly
	t.HiAge(); // 24

结合了 1 和 2 两种模式的优点**传参和复用**：
1. 可以继承父类原型上的属性，可以传参，可复用；
2. 每个新实例引入的构造函数属性是**私有**的。

缺点：
调用了两次父类构造函数（耗内存），子类的构造函数会代替原型上的那个父类构造函数。

### 4. 原型式继承

	function object(o) {
		function F() {} // 创建临时性的构造函数
		F.prototype = o; // 将传入的对象作为这个构造函数的原型
		return new F(); // 返回临时构造函数的实例
	}
	
	let Test = {
	　　colors:['red',blue,'black'],
	}
	
	let t1 = object(Test);
	t1.colors.push('green');
	let t2 = object(Test);
	t2.colors.push('grey');
	console.log(Test.colors); // red,blue,black,green,grey

从本质上讲， object() 就是对传入其中的对象执行了一次**浅复制**。即浅复制一个对象，用函数进行包装，从而可以方便添加新属性。

这一方法相当于 ES5 新增 的 Object.create()方法。

缺点：
1. 所有实例都会继承原型上的属性；
2. 无法实现复用。（新实例的属性都是后面添加的）

### 5. 寄生式继承

相当于给原型式继承外面套了个壳子。

	function object(o) {
		function F() {} // 创建临时性的构造函数
		F.prototype = o; // 将传入的对象作为这个构造函数的原型
		return new F(); // 返回临时构造函数的实例
	}

	function subobject(o) {
		let o2 = object(o);
		o2.name = 'thrilly'; // 增强对象
		return o2;
	}

	let Test = {
	　　colors:['red',blue,'black'],
	} 
	let t1 = subobject(Test);
	console.log(typeof subobject); // function
	console.log(typeof t1); // object
	console.log(t1.name); // thrilly

缺点：没用到原型，无法复用

### 6. 寄生组合式继承（常用）

基本思想：通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。

基本模型：

	function inheritProperty(subType, superType) {
	　　var prototype = object(superType.prototype); // 创建对象
	　　prototype.constructor = subType; // 增强对象
	　　subType.prototype = prototype; // 指定对象
	}

	function object(o) {
		function F() {} // 创建临时性的构造函数
		F.prototype = o; // 将传入的对象作为这个构造函数的原型
		return new F(); // 返回临时构造函数的实例
	}
	
	function Test(name){
	    this.name = name;
	    this.colors = ['red','blue','black'];
	}
	Test.prototype.sayName = function (){
	    alert(this.name);
	};
	function TestChild(name, age){
	    Test.call(this,name);
	    this.age = age;
	}

	inheritProperty(Test, TestChild);

	TestChild.prototype.sayAge = function() {
	    console.log(this.age);
	}

大佬的案例：

寄生：在函数内返回对象然后调用；

组合：1、函数的原型等于另一个实例。2、在函数中用apply或者call引入另一个构造函数，可传参。

![1](https://images2017.cnblogs.com/blog/1171086/201801/1171086-20180107014609737-663376247.png)

![2](https://images2017.cnblogs.com/blog/1171086/201801/1171086-20180107013758206-57906529.png)

对比下组合继承：
![3](https://images2017.cnblogs.com/blog/1171086/201801/1171086-20180105155605534-390785677.png)

这一方法修复了组合继承两次调用父类的问题。

最后借用大佬的话：

> 继承这些知识点与其说是对象的继承，更像是函数的功能用法，如何用函数做到复用，组合，这些和使用继承的思考是一样的。上述几个继承的方法都可以手动修复他们的缺点，但就是多了这个手动修复就变成了另一种继承模式。

> 这些继承模式的学习重点是学它们的思想，不然你会在coding书本上的例子的时候，会觉得明明可以直接继承为什么还要搞这么麻烦。就像原型式继承它用函数复制了内部对象的一个副本，这样不仅可以继承内部对象的属性，还能把函数（对象，来源内部对象的返回）随意调用，给它们添加属性，改个参数就可以改变原型对象，而这些新增的属性也不会相互影响。

参考：

[理解js继承的6种方式](https://www.cnblogs.com/Grace-zyy/p/8206002.html)

## Object.create() 和 new 的区别

阅读了相关博客，总结一句话：

new 只能新建构造函数的实例，所以构造函数内的属性会共享给实例，构造函数原型链的方法被实例继承；

Object.create 的参数可以为对象（空对象{}、null）/构造函数，参数是对象的话，会继承原对象的属性，参数为构造函数，由于没有绑定 this，不会获取到构造函数的属性，需要重新定义。

另一位大佬的话：

> 其实  new 是一种语法糖，真正new干了两件事，第一是在构造函数中第一句加上 this= Object.create(Parent.prototype)， 第二件事是最后结束的时候 return this; 

代码解释 new 和 Object.create 的过程：

	var obj = {}; // 1. 创建一个空对象
	obj.__proto__ = Func.prototype; // 2. 设置原型链
	var result = Func.call(obj); // 3. 让Func的this指向obj，并执行Func函数体
	return typeof result === 'object'? result : obj; // 4. 判断Func（构造函数）的返回值类型(这一步见下)

	Object.create =  function (o) {
	    var F = function () {};
	    F.prototype = o;
	    var obj = new F();
	    return obj;
	};


构造函数默认 return this，不用写，如下

	function A(){
	  this.name = x;
	  // return this; 
	}

如果构造函数 return 是基本数据类型：

	return 1
	return "abc"

则 return 后的东西忽略，就是 
	
	return {}

如果是 return 的是引用类型，则以 return 的内容为准：

	function A(){
	  this.name = x;  // 无效  
	  return {a: 1}; 
	}

