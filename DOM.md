### 事件冒泡和捕获，事件委托

DOM 标准事件流（event flow）存在三个阶段：事件捕获阶段、处于目标阶段、事件冒泡阶段。

[解析Javascript事件冒泡机制](https://blog.csdn.net/luanlouis/article/details/23927347)

事件冒泡终止的方法：

1）相应的处理函数内加入event.stopPropagation()，事件停留在本节点；

2）event.target 引用了产生此event对象的dom 节点，而event.currrentTarget 则引用了当前处理节点，判定event.target===event.currentTarget, 两者相等，则执行相应的处理函数；

3) 父节点统一处理事件，通过判断事件的发生地（即事件产生的节点），然后做出相应的处理(事件委托）。

注意阻止事件冒泡的方法有多种：

[preventDefault()、stopPropagation()、return false 之间的区别](https://www.cnblogs.com/dannyxie/p/5642727.html)

当你每次调用”return false“的时候，它实际上做了3件事情：
　　•event.preventDefault();
　　•event.stopPropagation();
　　•停止回调函数执行并立即返回。

事件委托的优点：代码简介，给浏览器的压力小；

事件委托就是在祖先级 DOM 元素绑定一个事件，当触发子孙级 DOM 元素的事件时，利用事件流（事件冒泡）的原理来触发绑定在祖先级 DOM 的事件。

### 事件绑定

以为buttton元素绑定单击事件，来探讨事件绑定的几种方式：

1. on + 事件

		<button onclick=function(){}></button>

2. addEventLister, attachEvent

- addEventListener(event, listener, useCapture)　　

参数定义：

event---（事件名称，如click，不带on）
listener---事件监听函数
useCapture---是否采用事件捕获进行事件捕捉，默认为false，即采用事件冒泡方式

addEventListener 在 IE11、Chrome 、Firefox、Safari等浏览器都得到支持。

- attachEvent(event,listener)　　

参数定义：

event---（事件名称，如onclick，带on)
listener---事件监听函数。

attachEvent 主要兼容 IE10 及以下的浏览器。

3. jQuery 方法

[Jquery中.bind()、.live()、.delegate()和.on()之间的区别详解](https://www.jb51.net/article/120018.htm)

.bind() 方法将事件类型和一个事件处理函数直接注册到了被选中的DOM元素中，但会将所有的事件处理函数附加到所有匹配的元素。

.live() 方法使用了事件委托的概念，将与事件处理函数关联的选择器和事件信息一起附加到文档的根级元素（即 document）。通过将事件信息注册到 document 上，这个事件处理函数将允许所有冒泡到 document 的事件调用它（例如委托型、传播型事件）。一旦有一个事件冒泡到 document 元素上， Jquery 会根据选择器或者事件的元数据来决定哪一个事件处理函数应该被调用。（Jquery 1.7以后的版本被弃用）

.delegate() 方法与 live() 方式实现方式相类似，它不是将选择器或者事件信息附加到 document，而是让你指定附加的元素。就像是 live() 方法一样，这个方法使用事件委托来正确地工作。

	/* .delegate() 方法会将选择器和事件信息 ( "li a" & "click" ) 附加到你指定的元素上 ( "#members" )。
	*/
	 
	$( "#members" ).delegate( "li a", "click", function( e ) {} );

新的 .on() 方法其实就是模拟 .bind() ， .live() 和 .delegate() 实现的语法糖，在 Jquery 1.7 版本中 .bind() ， .live() 和 .delegate() 方法只需要使用 .on() 方法一种方式来调用它们。当然 .unbind() ， .die() 和 .undelegate() 方法也一样，以下代码片段是从Jquery 1.7版本的源码中截取出来的：

	bind: function( types, data, fn ) {
	 return this.on( types, null, data, fn );
	},
	unbind: function( types, fn ) {
	 return this.off( types, null, fn );
	},
	 
	live: function( types, data, fn ) {
	 jQuery( this.context ).on( types, this.selector, data, fn );
	 return this;
	},
	die: function( types, fn ) {
	 jQuery( this.context ).off( types, this.selector || "**", fn );
	 return this;
	},
	 
	delegate: function( selector, types, data, fn ) {
	 return this.on( types, selector, data, fn );
	},
	undelegate: function( selector, types, fn ) {
	 return arguments.length == 1 ? 
	  this.off( selector, "**" ) : 
	  this.off( types, selector, fn );
	}

不同的使用方式决定了如何调用 on 方法。

	/* Jquery的 .bind() , .live() 和 .delegate() 方法只需要使用`.on()`方法一种方式来调用它们 */
	 
	// Bind
	$( "#members li a" ).on( "click", function( e ) {} ); 
	$( "#members li a" ).bind( "click", function( e ) {} );
	 
	// Live
	$( document ).on( "click", "#members li a", function( e ) {} ); 
	$( "#members li a" ).live( "click", function( e ) {} );
	 
	// Delegate
	$( "#members" ).on( "click", "li a", function( e ) {} ); 
	$( "#members" ).delegate( "li a", "click", function( e ) {} );

### 原生 JS 获取 HTML DOM 元素的8种方法

[原生JS获取HTML DOM元素的8种方法](https://www.cnblogs.com/web-record/p/10131782.html)

