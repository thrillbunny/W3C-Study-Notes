# AJAX

---

## 面试经典问题

### 1. AJAX 应用和传统 Web 应用有什么不同？

1）同步和异步带来不同的用户体验

传统的 web 前端发送的是同步请求。在 web 页面和服务器端的交互过程中，浏览器的同步请求过程是：提交请求 -> 等待服务器处理 -> 服务器处理完毕后返回客户端请求的信息。
在这一过程中，客户端的 web 页面一直处于等待状态，不能干任何事情。

AJAX 应用的请求也分同步请求和异步请求，默认是进行异步请求，但也可以进行设置实现同步请求。在前端发送请求给服务器端后，用户仍然可以做其他事情，而不用等待服务器的响应，当请求的数据量比较大时能给用户带来非常好的使用体验。

2）数据交互方式不同

传统的 web 前端与后端的交互中，浏览器直接访问服务器端的Servlet来获取数据。**Servlet通过转发把页面发送给浏览器**。每次请求服务器都会返回处理好的整个网页，浪费了网络传输带宽，客户端的响应时间依赖于服务器的响应的时间，降低了客户端页面响应速度。

前端使用 AJAX 之后，浏览器是先把请求发送到 **XMLHttpRequest 异步对象**之中，异步对象对请求进行封装，再发送给服务器。
（相当于在服务器和用户之间加了一个作为中间层的 AJAX 引擎使用户操作与服务器响应异步化；这里并不是所有用户请求都会提交给服务器，一些数据验证和处理都是由 AJAX 引擎自己处理的，只有确定需要从服务器端读取数据才会将请求转发给服务器）

服务器并不是以转发的方式响应，而是以流的方式把数据返回给浏览器。
AJAX 应用可以仅向服务器发送并取回必需的数据，使得传输数据量大大减少，页面响应速度加快。同时很多处理请求可以由客户端自己完成，减轻了服务器压力。

新建了 XMLHttpRequest 异步对象后，异步对象会不停监视服务器端状态的变化，一旦获取了服务器端返回的数据就更新前端页面。

**（AJAX 最大特点：局部页面刷新/动态不刷新，在不更新整个页面的前提下维护数据）**

3）实现复杂度

传统 Web 页面实现代码简单，浏览器在背后做了很多数据组织和提交的工作。

AJAX 页面实现代码复杂，需要自己编写代码实现数据组织、提交和服务器返回数据的接收、页面的更新等操作。

![传统页面和AJAX请求的具体差异](https://img-blog.csdn.net/20160605101901089?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 2. AJAX 的实现步骤

1）建立 XmlHttpRequest 对象

	var xhr;
	
	if(window.XMLHttpRequest) {
		xhr = new XMLHttpRequest(); // 在 IE6 以上的版本以及其他内核的浏览器(Mozilla)等
	} else if(window.ActiveXObject) { 
		// IE6 及以下
	    var activeName = ["MSXML2.XMLHTTP", "Microsoft.XMLHTTP"];
	    for(var i = 0; i < activeName.length; i++) {
			try {
				xhr = new ActiveXobject(activeName[i]);
				break;
			} catch(e) {}
	    }
	} else {
		alert("Your browser does not support XMLHTTP.");
	}

2）使用 open 方法创建新的 HTTP 请求，指定 HTTP 请求方式（get/post)、URL 和验证信息
	
	// open(method, url, async, username, password)
	xhr.open("GET", "xxx.php", true)

如果采用的是 POST 方式，还需要设置请求头部信息：

	xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded")

3）设置回调函数，响应 HTTP 请求状态的变化
	
	xhr.onreadystatechange= callback;
	function callback(){}

4）向服务器端发送 HTTP 请求 send()

	xhr.send(null)

如果是 POST 方式则不为空

5）通过回调函数获取异步调用返回的数据，使用 JavaScript 和 DOM 实现局部页面刷新

	if(xhr.readyState === 4 && xhr.status === 200) {
		// ajax 请求成功，获取服务器返回数据
		var responseText = xhr.responseText; // 获取文本数据
		document.getElementById("indo").innerHTML = responseText;
	}

### 3. AJAX 请求的时候 GET 和 POST 方式的区别




### 3.1 HTTP 中的请求方法及其具体区别？

1）根据 HTTP 标准，HTTP 请求存在多种请求方法，其中 HTTP/1.0 规定了 GET、POST 和 HEAD 方法，HTTP/1.1 增加了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。在HTTP/1.1标准制定之后,又陆续扩展了一些方法,其中使用中较多的是 PATCH 方（至少说出其中的六种）

2）具体说明：

A. GET
`GET` 方法用于请求指定的页面信息，并返回响应主体。`GET` 方法请求长度存在限制。
根据 HTTP 规范，`GET` 方法应该只用于数据的读取，而不应当用于会产生副作用的**非幂等**的操作中。
`GET` 方法也可以用于数据的提交，提交的数据需要放在 URL 中，这样可能产生安全问题。

B. POST
`POST` 请求会向指定资源提交数据，请求服务器进行处理，如表单数据提交、文件上传等，请求数据会被包含在请求体中。POST方法是**非幂等**的方法，因为这个请求可能会**创建**新的资源或修改现有资源。

C. HEAD
`HEAD` 方法与 `GET` 方法一样，都是向服务器发出指定资源的请求。但是，服务器在响应 `HEAD` 请求时只会返回服务器的响应头部信息。`HEAD` 方法常被用于客户端查看服务器的性能。

D. PUT
`PUT` 请求会向指定资源位置上传其最新内容，`PUT` 方法是幂等的。通过该方法，客户端可以将指定资源的最新数据传送给服务器取代指定的资源的内容，实现资源的**更新**，但不会创建新资源。

E. DELETE
`DELETE` 方法用于请求服务器删除所请求 URI（统一资源标识符，Uniform Resource Identifier）所标识的资源。`DELETE` 方法后指定资源会被删除，该方法也是幂等的。
`DELETE` 请求一般返回三种状态码：
- 200(OK) 删除成功，同时返回已经删除的资源
- 202(Accepted) 删除请求已经接受，但没有被立即执行（资源也许已经被转移到了待删除区域）
- 204（NO Content) 删除请求已经被执行，但是没有返回资源（也许是请求删除不存在的资源造成的）

F. CONNECT
`CONNECT` 方法是 HTTP/1.1 协议预留的，能够将连接改为管道方式的代理服务器。通常用于 SSL 加密服务器的链接与非加密的 HTTP 代理服务器的通信。

G. TRACE
`TRACE` 请求服务器回显其收到的请求信息，该方法主要用于 HTTP 请求的测试或诊断。

H. OPTIONS
一般是用于客户端查看服务器的性能。该方法会请求服务器返回该资源所支持的所有 HTTP 请求方法该方法会用 * 来代替资源名称，向服务器发送 `OPTIONS` 请求。JavaScript 的 XMLHttpRequest 对象进行 CORS 跨域资源共享时，就是使用 OPTIONS 方法发送嗅探请求，以判断是否有对指定资源的访问权限。

I. PATCH
`PATCH` 方法出现的较晚，它在 2010 年的 RFC 5789 标准中被定义。`PATCH` 请求与 `PUT` 请求类似，同样用于资源的更新，但 `PATCH` 一般用于资源的部分更新，而 `PUT` 一般用于资源的整体更新；当资源不存在时，`PATCH` 会创建一个新的资源，而 `PUT` 只会对已存在的资源进行更新。（PUT 和 PATCH 的区别）

3）POST 和 PUT 的区别
谈及这两种请求的区别首先需要解释一个概念：幂等和非幂等。
对于单目运算，如果一个运算对于在范围内的所有的一个数多次进行该运算所得的结果和进行一次该运算所得的结果是一样的，那么我们就称该运算是幂等的。对于双目运算，则要求当参与运算的两个值是等值的情况下，如果满足运算结果与参与运算的两个值相等，则称该运算幂等。

POST 用于向指定资源提交数据，可以**创建或更新**数据，既不是安全的，也**不是幂等**的，这里安全和幂等指的是当操作没有达到预期的目标时，可以不停的重试，而不会对资源产生副作用。实际上，当用户多次发出同样的 POST 请求后，会创建出若干资源。如果每次更新提交相同的内容，最终的结果应当不一致的时候，就使用 POST。

PUT 用于向指定的 URI 传送更新资源，是**幂等**的，只会对同一资源进行更新，如果进行同样的操作，多次请求获得的结果是一致的。比如更新一篇 blog，因为文章已经有了单一的 URL，更新文章就使用 PUT。

另外，POST 是作用于一个集合资源之上的（PUT /uri），而 PUT 是作用于一个具体资源之上的（PUT /uri/xxx），简单说就是如果 URL 可以在客户端确定，则使用 PUT 更新，否则就使用 POST。就比如说很多资源使用数据库自增主键（key）作为标识信息，而创建的资源的标识信息到底是什么只能由服务端提供，这个时候就必须使用 POST。

4）GET 和 POST 的区别
A. 根据 HTTP 规范，二者**语义**是完全不同的。GET 应当用于服务器数据的获取（URI+query），是幂等操作，对于静态页面（URI）请求，服务器直接返回对应的资源给请求方，也可以用 query 查询字符串实现对动态页面的请求，获取所需的内容；POST 应当用于提交数据给服务器处理，实现服务器资源的更新或创建，是非幂等操作。

B. GET 请求的查询字符串是放在 URL 之中的，以 ？作为分隔，用 & 连接，如
	
	/test/demo_form.asp?name1=value1&name2=value2

POST请求的查询字符串则会被放在请求的 HTTP 消息主体中，如

	POST /test/demo_form.asp HTTP/1.1
	Host: learn.com
	name1=value1&name2=value2

GET 请求可被缓存，可以被保留在浏览器历史记录中，也可以收藏作为书签；POST 请求则不会被缓存，不会被保留在历史记录中。

C. GET 请求存在长度限制，这是因为 GET 是通过 URL 提交数据的，URL 长度受限，最长是 2048 个字符，提交的数据只允许是 ASCII 字符；POST 提交的数据依照 HTTP 规范理论是不存在限制的，但会受限于服务器处理的能力，提交的数据不限格式。

D. GET 的安全性较 POST 差，其实两种方式在传输层面的安全性区别不大，但 GET 采用 URL 提交数据，数据是所有人都可见的，还会被缓存或保留在历史记录里，因此安全性低，不要用 GET 方法发送密码或其他敏感信息。

E.（补充）GET 方式的请求产生一个 TCP 数据包，POST 会产生两个。

对于 GET 方式的请求，浏览器会把 HTTP HEADER 和 DATA 一起发送（URI+query），服务器响应200（返回响应数据）；
而对于 POST，浏览器先发送 HEADER，服务器响应 100 continue，浏览器再发送 DATA，服务器响应200 ok（返回响应数据）。

但并不是所有浏览器都会在 POST 中发送两次包，Firefox就只发送一次，而且由于网络传输速度一般很快，发一次包和两次包时间其实差不多，在网络环境差的情况下，两次包在验证数据包完整性上，更具优势。(??之后自己试验论证下)

参考资料：

[HTTP请求方法：GET、HEAD、POST、PUT、DELETE、CONNECT、OPTIONS、TRACE 说明](https://www.html.cn/archives/9341)

[get,put,post,delete含义与区别](https://286.iteye.com/blog/1420713)

[HTTP 方法：GET 对比 POST](http://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

[GET和POST两种基本请求方法的区别](https://www.cnblogs.com/logsharing/p/8448446.html)

### 4. AJAX 的优点和缺点？

优点：

1. 页面无刷新更新数据（局部页面更新）
2. 异步方式与服务器通信，响应迅速
3. 前后端负载平衡
把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本；ajax的原则是“按需取数据”，可以最大程度的减少冗余请求和响应对服务器造成的负担
4. 基于标准化、广泛支持的技术，无需额外的插件
5. 界面与应用分离

缺点：

1. 不支持 Back 和 History 功能，即对浏览器机制的破坏
2. 存在安全问题，AJAX 暴露了与服务器交互的细节
3. 对搜索引擎的支持比较弱
4. 破坏了浏览器的异常处理机制
5. 调试困难
6. 违背了URL和资源定位的初衷

### 5. AJAX 的适用和不适用场景

A. 适用场景

1) 用 AJAX 进行数据验证和表单交互

在填写表单内容时，有时需要保证数据的唯一性，如新用户注册填写的用户名，因此必须对用户输入的内容进行数据验证。
使用Ajax技术，可以由XMLHttpRequest对象发出验证请求，根据返回的HTTP响应判断验证是否成功，整个过程不需要弹出新窗口，也不需要将整个页面提交到服务器，快速而又不加重服务器负担。

2) 用 AJAX 自动更新页面

对于数据实时变化的 web 应用中，如热点新闻，天气预报等，在 AJAX 出现之前，用户为了及时了解最新内容，必须不断刷新页面，或页面本身实现定时刷新的功能。这可能会发生两种情况：

+ 某段时间网页的内容没有发生任何变化，但用户并不知道，仍然不断刷新页面；
+ 某段时间网页的内容已经发生任何变化，但用户失去了耐心，放弃了刷新页面，得不到最新的数据。

使用 AJAX 技术，可以通过 AJAX 引擎在后台进行定时轮询，向服务器发送请求，查看是否有最新数据，若有则将最新数据则获取并返回给客户端，通过 JavaScript 等方式通知用户。这样既避免了用户不断手工刷新页面，也不会因为重复刷新页面造成资源浪费。

3）多人参与的场景

如用户多人在线投票、讨论等场景可以使用 AJAX 提升页面刷新速度（控制在用户可接受范围内，如一秒内），带来更好的用户体验，使更多人参与进来。

B. 不适用场景

1) 需要使用后退按钮来查看历史搜索

AJAX 会破坏浏览器后退按钮的正常行为，在动态更新页面的情况下，用户无法回到前一个页面状态，因为浏览器仅能记下历史记录中的静态页面。

2) 部分简单的表单

一个内容简单的表单（如评论表单）或提交订单等都不应该使用以 AJAX 驱动的表单提交机制，因为这类简单的表单本身服务器的响应就很快速，AJAX 并不能使用户体验得到明显的改善。

3) 需要更新大量信息

如果页面上的大部分内容都需要更新，完全可以直接从服务器那里获得一个新页面。

4）控制页面呈现

AJAX 实际上是一个数据同步、操纵和传输的技术，不应该用它来呈现页面。

### 参考文档

[浅谈Ajax的适用场景和不适用场景](https://blog.csdn.net/zhouziyu2011/article/details/70183214)

### 6. 介绍 XMLHttpRequest 对象

![XMLHttpRequest简介](https://segmentfault.com/img/remote/1460000013286993?w=1304&h=720)

XMLHttpRequest 是 AJAX 的核心 API。XMLHttpRequest 使浏览器能够以异步方式从服务器取得信息，用户不必刷新页面就可以使用 XMLHttpRequest 对象取得新数据，然后再通过 DOM 将新数据插入到页面中，实现页面的局部更新。

IE 中可能会遇到三种不同版本的 XHR 对象，即 MSXML2.XMLHttp、
MSXML2.XMLHttp.3.0 和 MXSML2.XMLHttp.6.0。

IE7+、Firefox、Opera、Chrome 和 Safari 都支持原生的 XMLHttpRequest 对象。

1）XMLHttpRequest 方法

+ **open()** 创建 HTTP 请求

open(String method,String url,boolean asynch,String username,String password)

第一个参数指定请求方式（get，post，etc）
第二个方式指定请求地址
第三个参数指定请求异步还是同步（默认 true 异步）
第四和第五参数（可选）用于 HTTP 认证

+ **setRequestHeader()** 设置消息头

setRequestHeader(String header,String value)

主要用于 post 请求设置等：

	xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded")

+ **send()** 发送请求给服务器

send(content)

get 方式参数为空或 null，post 方式需要将提交参数写入

+ getAllResponseHeaders()
 
+ getResponseHeader(String header) 

+ abort() 取消异步请求

2) XMLHttpRequest 事件

**onreadystatechange**

请求状态改变的事件触发器，当 readyState 改变时触发，一般用于指定状态改变时的回调函数

3）XMLHttpRequest 属性

+ **readyState** 表示请求/响应过程的当前活动阶段，共有 5 种状态：
	- 0 未初始化
	- 1 启动，已经成功调用 open() 方法，但尚未调用 send() 方法
	- 2 发送，已经调用 send() 方法，但尚未接收到响应
	- 3 接收，已经接收到部分响应数据（HTTP Header）
	- 4 完成，已经接收到全部响应数据

+ **status** 响应的 HTTP 状态码（1XX，2XX，3XX，4XX，5XX）

+ statusText HTTP 状态码的说明

+ **responseText** 作为响应主体被返回的文本

+ responseXML 如果响应的内容类型是 text/xml 或 application/xml ，这个属性中将保存包含着响应数据的 XML DOM 文档

### 7. AJAX 解决浏览器缓存问题

+ AJAX 发送请求前添加 AjaxObj.setRequestHeader("If-Modified-Since", "0")
+ AJAX 发送请求前添加 AjaxObj.setRequestHeader("Cache-Control", "no-cache")
+ URL 添加随机数 "fresh="+Math.random()
+ URL 添加时间戳 "nowtime="+new Date().getTime()
+ jQuey 中设置 $.ajaxSetup({cache: false})

### 8. JavaScript 的同源策略，如何实现跨域请求

#### 同源策略：
同源策略是客户端脚本（尤其是Javascript）的重要的安全度量标准。它最早出自Netscape Navigator2.0，其目的是防止某个文档或脚本从多个不同源装载。
所谓同源指的是：**协议、域名、端口号**均相同。同源策略是一种安全协议，禁止页面加载或执行与自身来源不同的域的任何脚本。

情景：
用户在登录了银行页面后又浏览了恶意网站，如果没有同源限制，恶意网页上脚本就可以通过用户浏览器的 cookie 获取用户名和密码，再利用用户的账号进行违法行为。这就是[**CSRF攻击**](http://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html)即跨站请求伪造攻击。为避免 cookie 全网共享，需要同源策略。

跨域是针对同源的一个概念，协议、域名和端口号只要有一个不一致，请求就是跨域的。
同源策略针对下面三种跨域请求作出了限制：
1）通过 XMLHttpRequest 实现的 AJAX 请求不能跨域发送；
2）DOM 无法通过跨域请求获取；
3）Cookie、LocalStorage 和 IndexDB 无法读取。

#### AJAX 实现跨域请求：

除了架设服务器代理（浏览器请求同源服务器，再由后者请求外部服务），还有三种方法规避这个限制。

**1) JSONP 跨域**

JSONP 是服务器与客户端跨源通信的常用方法。

JSONP 基本思想：网页动态添加 script 标签，向服务器发送请求，请求 JSON数据，script 标签不受同源策略限制；服务器收到请求后，将 JSON 数据放在一个指定名字的回调函数里传回来。

浏览器网页：

	function addScriptTag(src) {
		var script = document.createElement('script');
		script.setAttribute("type","text/javascript");
		script.src = src;
		document.body.appendChild(script);
	}
	
	window.onload = function () {
		addScriptTag('http://example.com/ip?callback=foo');
	}
	
	function foo(data) {
		console.log('Your public IP address is: ' + data.ip);
	};

请求查询字符串必须有一个 callback 参数用来指定回调函数名称。

服务器收到这个请求以后，会将 JSON 数据放在回调函数的参数位置返回。

	foo({
	  "ip": "8.8.8.8"
	});

也可以通过 jQuery 封装的 $.ajax() 函数实现：

	$.ajax({
		url: "http://example.com/ip",	 
		type: "get",
		cache: false,
		dataType: "jsonp", // 设置返回的数据格式
		jsonp: "callback", // 定义了 callback 参数名
		jsonpCallback: "foo", // 定义了 jsonp 回调函数的函数名
		success: function(data){
			alert("success:"+data);
		},
		error:function(){
			alert("发生异常");
		}
	});
	
	function foo(data) {
		console.log('Your public IP address is: ' + data.ip);
	};

与直接在链接中添加查询字符串是一样的功能。JSON 和 JSONP 之后会再次进行详细学习。

JSONP 优点：简单适用，支持所有浏览器，服务器改造非常小。
JSONP 缺点：只能发送 GET 请求。

CORS与JSONP的使用目的相同，但是比JSONP更强大。JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

**2）CORS 跨域**

CORS，是 HTML5 的一项特性，它定义了一种浏览器和服务器交互的方式来确定是否允许跨域请求。
相对于 JSONP 这种解决方案来说，使用CORS，不需要要求服务器以指定格式返回数据（包装成JS脚本的格式：callback_func({ data });）；CORS，只需要在服务器端做一些通用设置。

下面详解下 CORS 跨域：

#### 简介

CORS需要浏览器和服务器同时支持。目前，IE10以上的所有现代浏览器都支持该功能。
整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。只要服务器实现了CORS接口，就可以跨源通信。

#### CORS请求

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

满足以下条件的即为简单请求：

a. 请求方法是以下三种之一（HTTP 1.0规定的三种请求）
+ HEAD
+ GET
+ POST
b. HTTP请求头部不超以下字段
+ Accept
+ Accept-Language
+ Content-Language
+ Last-Event-ID
+ Content-Type（仅限application/x-www-form-urlencoded、multipart/form-data、text/plain）

否则就属于非简单请求。浏览器对这两种CORS请求的处理是不一样的。

##### A 简单请求

对于简单请求，浏览器在头信息之中，增加一个 `Origin` 字段，直接发出CORS请求。

下面是一个例子，浏览器发现这次跨源AJAX请求是简单请求，就自动在头信息之中，添加一个 `Origin` 字段。

	GET /cors HTTP/1.1
	Origin: http://api.bbb.com
	HOST: api.aaa.com
	Accept-Language: en-US
	Connection: keep-alive
	User-Agent: Mozilla/5.0...

这段头信息中的 `Origin` 字段就是用来说明本次本次请求来自哪个源（协议 + 域名 + 端口），服务器根据这个值，决定是否同意这次请求。

如果 `Origin` 指定的源不在许可范围内，服务器会返回一个**正常**的HTTP响应。浏览器发现，这个回应的头信息没有包含 Access-Control-Allow-Origin 字段（详见下文），就知道出错了，从而抛出一个错误，该错误无法通过状态码识别（HTTP响应的状态码有可能是200），只能被 **XMLHttpRequest** 的 onerror 回调函数捕获。

如果 `Origin` 指定的源在许可范围内，服务器返回响应会添加几个与CORS有关的头部字段，这些字段都以 **Access-Control-** 开头：

	Access-Control-Allow-Origin: http://api.bbb.com
	Access-Control-Allow-Credentials: true
	Access-Control-Expose-Headers: FooBar
	Content-Type: text/html; charset=utf-8

其中：

Access-Control-Allow-Origin 是必须的字段，它的值可能是请求时 `Origin` 的值，也可能是 `* ` ，表示可接受任意域名的请求。

Access-Control-Allow-Credentials 是可选字段，其值是一个布尔值，表示是否允许浏览器发送Cookie。默认情况下，Cookie不包括在CORS请求之中。该字段设为true时即表示服务器允许浏览器将Cookie包含在请求中，一起发给服务器。这个值也只能设为true，如果服务器不要浏览器发送Cookie，删除该字段即可。

(服务器需要指定 Access-Control-Allow-Credentials 字段为 true，开发者则必须在 AJAX 请求中打开 withCredentials 属性，代码示例如下：

	var xhr = new XMLHttpRequest();
	xhr.withCredentials = true;

否则，即使服务器同意或要求发送 Cookie，浏览器也不会发送。

但是，如果省略 withCredentials 设置，有的浏览器还是会一起发送 Cookie。这时，可以显式关闭 withCredentials。

	xhr.withCredentials = false;

另外需要注意的是，如果要发送 Cookie， Access-Control-Allow-Origin 就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie 依然遵循**同源政策**，只有用服务器域名设置的 Cookie 才会上传，其他域名的 Cookie 并不会上传，且（跨源）原网页代码中的 document.cookie 也无法读取服务器域名下的 Cookie。）

Access-Control-Expose-Headers 是可选字段。CORS请求时， `XMLHttpRequest` 对象的  getResponseHeader() 方法只能拿到6个基本字段：**Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma**。如果想拿到其他字段，就必须在 Access-Control-Expose-Headers 里面指定。上面的例子指定，getResponseHeader('FooBar') 可以返回FooBar字段的值。

##### B 非简单请求

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为**"预检"请求**（preflight）。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的 XMLHttpRequest 请求，否则就报错。下面是浏览器的脚本示例：

	var url = 'http://api.aaa.com/cors';
	var xhr = new XMLHttpRequest();
	xhr.open('PUT', url, true);
	xhr.setRequestHeader('X-Custom-Header', 'value');
	xhr.send();

上面的脚本中，HTTP 采用 PUT 请求，并添加了一个名为 X-Custom-Header 的自定义首部字段。浏览器发现这次请求是非简单请求，就会**自动**发出一个"预检"请求，要求服务器确认可以这样请求。下面是这个"预检"请求的HTTP头信息：

	OPTIONS /cors HTTP/1.1
	Origin: http://api.bbb.com
	Access-Control-Request-Method: PUT
	Access-Control-Request-Header: X-Custom-Header
	Host: api.aaa.com
	Accept-Language: en-US
	Connection: keep-alive
	User-Agent: Mozilla/5.0...

注意，"预检"请求用的请求方法是 **OPTIONS**，表示这个请求是用来询问的。头信息里面，关键字段是 `Origin` ，表示请求来自哪个源。

除了 `Origin` 字段，"预检"请求的头信息还包括两个特殊字段:

Access-Control-Request-Method 是必须包含的字段，列出浏览器的 CORS 请求会用到哪些 HTTP 方法，上面用到的就是 PUT 请求。

Access-Control-Request-Header 是一个用逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段。

对于浏览器发送的"预检"请求，浏览器在检查了 Origin、Access-Control-Request-Method 和 Access-Control-Request-Header 字段以后确认允许跨源请求，就可以发送响应给浏览器：

	HTTP/1.1 200 OK
	Date: Mon, 01 Dec 2008 01:15:39 GMT
	Server: Apache/2.0.61 (Unix)
	Access-Control-Allow-Origin: http://api.bbb.com
	Access-Control-Allow-Methods: GET, POST, PUT
	Access-Control-Allow-Headers: X-Custom-Header
	Access-Control-Allow-Credentials: true
	Access-Control-Max-Age: 1728000
	Content-Type: text/html; charset=utf-8
	Content-Encoding: gzip
	Content-Length: 0
	Keep-Alive: timeout=2, max=100
	Connection: Keep-Alive
	Content-Type: text/plain

该 HTTP 响应中，关键的是 Access-Control-Allow-Origin 字段，该字段表示允许跨域请求的客户端，同样也可以设置为 * ，表示同意任意跨域请求。

Access-Control-Allow-Methods 字段是必须字段，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返回的是服务器所有支持的方法，而不单是浏览器请求的那个方法，这是为了避免多次"预检"请求。

如果浏览器请求包括 Access-Control-Request-Headers 字段，则表示 Access-Control-Allow-Headers 字段是必需的。它也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在"预检"中请求的字段。

Access-Control-Allow-Credentials 是可选字段，该字段与简单请求时的含义相同。

Access-Control-Max-Age 是可选字段，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应1728000秒（即20天），在此期间，所有同类型的请求都将不再发送预检请求，而是直接使用此次返回的头作为判断依据。
这个首部字段可以用作大大优化预检请求的请求次数。

如果浏览器否定了"预检"请求，会返回一个正常的HTTP响应，但是该响应中没有任何CORS相关的头信息字段（即 Access-Control-Allow- 字段）。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，该错误会被XMLHttpRequest 对象的 onerror 回调函数捕获。控制台会打印出如下的报错信息。

	XMLHttpRequest cannot load http://api.alice.com.
	Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.

服务器通过了"预检"请求后，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个Origin头信息字段，服务器的回应，也都会有一个Access-Control-Allow-Origin头信息字段。

下面是"预检"请求之后，浏览器的正常CORS请求：

	PUT /cors HTTP/1.1
	Origin: http://api.bob.com
	Host: api.alice.com
	X-Custom-Header: value
	Accept-Language: en-US
	Connection: keep-alive
	User-Agent: Mozilla/5.0...
	上面头信息的Origin字段是浏览器自动添加的。

下面是服务器正常的回应：

	Access-Control-Allow-Origin: http://api.bob.com
	Content-Type: text/html; charset=utf-8

上面头信息中，Access-Control-Allow-Origin字段是每次回应都必定包含的。

CORS跨域请求失败的案例可以见[ajax跨域，这应该是最全的解决方案了](https://segmentfault.com/a/1190000012469713)，解决这些问题均需要对服务器端进行相应配置。

#### 后端配置

因为我只学过 PHP 和 node.js，这里就只针对这两种语言的后台配置作介绍。

##### PHP 后端配置

配置非常简单，遵循以下步骤：

第一步：配置 PHP 后台，允许跨域

	<?php
		header('Access-Control-Allow-Origin: *');
		header('Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept');
	?>

第二步：在 httpd.conf 中配置 Apache web 服务器跨域

原始代码

	<Directory />
	    AllowOverride none
	    Require all denied
	</Directory>

改为以下代码

	<Directory />
	    Options FollowSymLinks
	    AllowOverride none
	    Order deny,allow
	    Allow from all
	</Directory

##### Node.js 后端配置（express 框架）

Node.js 的后台配置也很简单，只需用express如下配置:

app.all('*', function(req, res, next) {
	res.header('Access-Control-Allow-Origin','*');
	res.header('Access-Control-Allow-Headers','X-Requested-With');
	res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
	res.header('X-Powered-By', '3.2.1');
	res.header('Content-Type', 'application/json;charset=utf-8'); // 方便返回 json 数据

	if(req.method == 'OPTIONS') {
		res.sendStatus(200); // 让 OPTIONS 请求快速返回
	} else {
		next();
	}
});

**3）WebSocket**

WebSocket 是一种通信协议，使用 ws://（非加密）和 wss://（加密）作为协议前缀。该协议不实行同源策略，只要服务器支持，就可以通过它进行跨源通信。

以下面一个例子作示范，浏览器发出的 WebSocket 请求的头信息为：

	GET /chat HTTP/1.1
	HOST: server.example.com
	Upgrade: websocket
	Connection: Upgrade
	Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
	Sec-WebSocket-Protocol: chat, superchat
	Sec-WebSocket-Version: 13
	Origin: http://example.com

请求头部信息中有一个 Origin 字段，表示该请求的请求源（origin），即发自哪个域名，包含了这个字段使得 WebSocket 没有实行同源政策。

服务器可以根据这个字段的域名是否在通信白名单中，判断是否许可本次通信。许可通信后，服务器就会做出如下响应：

	HTTP/1.1 101 Switching Protocols
	Upgrade: websocket
	Connection: Upgrade
	Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
	Sec-WebSocket-Protocol: chat

WebSocket 之后还会进行专题学习。

4) 代理请求方式

注意，由于接口代理是有代价的，所以这个仅是**开发过程**中进行的。

与前面的方法不同，前面 CORS 是后端解决，而这个主要是前端对接口进行代理，也就是:

+ 前端 ajax 请求的是**本地**接口
+ 本地接口接收到请求后向实际的接口请求数据，然后再将信息返回给前端
+ 一般用 node.js 即可代理

具体方法搜索 node.js 代理请求。

参考资料：
[阮一峰-浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)
[阮一峰-跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
[ajax跨域，这应该是最全的解决方案了](https://segmentfault.com/a/1190000012469713)(这篇将解决方案作了详细的流程图，用图片列举了跨域请求失败的案例，值得再看)

#### 其他跨域技术：
+ < script src="..." >< /script > 标签嵌入跨域脚本，语法错误信息只能在同源脚本中捕捉到。

+ < link rel="stylesheet" href="..." > 标签嵌入CSS。由于CSS松散的语法规则，CSS的跨域需要一个设置正确的Content-Type 消息头。

+ < img src="..." >嵌入图片。支持的图片格式包括 PNG、GIF、JPEG、BMP、SVG 等。

+ < video > 和 < audio >嵌入多媒体资源。

+ < object >, < embed > 和 < applet > 嵌入插件。

+ form 表单提交。

+ @font-face 引入的字体。一些浏览器允许跨域字体（ cross-origin fonts），一些需要同源字体（same-origin fonts）。

+ iframe 与父窗口相互通信。

iframe 窗口和 window.open 方法打开的窗口与父窗口不同源，无法进行通信交互，例如父窗口想要获取 iframe 子窗口的 DOM，如果二者不是同源就会报错：

	document.getElementById('myIframe').contentWindow.document

同样子窗口获取主窗口的 DOM 也会报错：

	window.parent.document.body

如果两个窗口一级域名相同，只是二级域名不同，那么设置document.domain 属性（下文中介绍），就可以规避同源策略，拿到DOM。

对于完全不同源的网站，目前有三种方法解决跨域通信问题：

A. 片段识别符（fragment identifier）

片段标识符（fragment identifier）指的是，URL 的 # 号后面的部分，比如 http://example.com/x.html#fragment 的 #fragment。如果只是改变片段标识符，页面不会重新刷新。

父窗口可以把信息，写入子窗口的片段标识符:

	var src = originURL + '#' + data;
	document.getElementById('myIFrame').src = src;

子窗口通过监听 **hashchange** 事件获取数据信息:

	window.onhashchange = checkMessage;
	
	function checkMessage() {
	  var message = window.location.hash;
	  // ...
	}

子窗口也可以改变父窗口的片段标识符：

	window.parent.location.href = target + '#' + hash;

B. window.name

浏览器窗口有 window.name 属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了该属性，后一个网页便可以读取它。

父窗口先打开一个子窗口，载入一个不同源的网页，该网页将信息写入 window.name 属性。

	window.name = data;

接着，子窗口跳回一个与主窗口同域的网址。

	location = 'http://parent.url.com/xxx.html';

然后，主窗口就可以读取子窗口的 window.name 了。

	var data = document.getElementById('myFrame').contentWindow.name;

这种方法的优点是，window.name 容量很大，可以放置非常长的字符串；缺点是必须监听子窗口 window.name 属性的变化，影响网页性能（还很不常见）。

C. **跨文档通信API**（Cross-document messaging）

H5 提出了一个非常有用的 API：跨文档通信 API（Cross-document messaging）。

这个 API 为 window 对象新增了一个 **window.postMessage** 方法，允许跨窗口通信，不论这两个窗口是否同源。

父窗口 http://aaa.com 向子窗口 http://bbb.com 发消息只需要调用 postMessage 方法。

	var popup = window.open('http://bbb.com', 'title');
	popup.postMessage('Hello World!', 'http://bbb.com');

postMessage 方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即"协议 + 域名 + 端口"。也可以设为 *，表示不限制域名，向所有窗口发送。

子窗口向父窗口发送消息的写法类似。

	window.opener.postMessage('Nice to see you', 'http://aaa.com');

父窗口和子窗口都可以通过 message 事件，监听对方的消息。

	window.addEventListener('message', function(e) {
	  console.log(e.data);
	},false);

message 事件的事件对象 event，提供以下三个属性。

	event.source // 发送消息的窗口
	event.origin // 消息发向的网址
	event.data // 消息内容

下面的例子是，子窗口通过 event.source 属性引用父窗口，然后发送消息。

	window.addEventListener('message', receiveMessage);
	function receiveMessage(event) {
	  event.source.postMessage('Nice to see you!', '*');
	}

event.origin 属性可以过滤不是发给本窗口的消息。

	window.addEventListener('message', receiveMessage);
	function receiveMessage(event) {
	  if (event.origin !== 'http://aaa.com') return;
	  if (event.data === 'Hello World') {
	      event.source.postMessage('Hello', event.origin);
	  } else {
	    console.log(event.data);
	  }
	}

+ 跨域数据存储访问：Cookie 跨域和 LocalStorage 跨域。

cookie 允许同源网站共享，设置了 document.domain 后一级域名相同但二级域名不同的两个网站（同一父域）也可以共享 cookie。
如 A 网页是 http://w1.example.com/a.html，B 网页是 http://w2.example.com/b.html，那么只要设置相同的document.domain，两个网页就可以共享Cookie：

	document.domain="example.com"

另外，服务器也可以在设置Cookie的时候，指定Cookie的所属域名为一级域名，比如.example.com:

	Set-Cookie: key=value; domain=.example.com; path=/

这样的话，二级域名和三级域名不用做任何设置，都可以读取这个Cookie。

存储在浏览器中的数据，如 localStorage 和 IndexedDB，以源进行分割。每个源都拥有自己单独的存储空间，一个源中的 JS 脚本不能对属于其它源的数据进行读写操作。

但通过 **window.postMessage**，也可以实现读写其他窗口的 LocalStorage。

用一个例子实现主窗口写入 iframe 子窗口的 localStorage。

父窗口发送消息的代码如下：

	var win = document.getElementsByTagName('iframe')[0].contentWindow;
	var obj = { name: 'Jack' };
	win.postMessage(JSON.stringify({key: 'storage', data: obj}), 'http://bbb.com'); // postMessage 方法


子窗口将父窗口发来的消息，写入自己的LocalStorage：

	window.onmessage = function(e) {
		if (e.origin !== 'http://bbb.com') {
			return;
		}
		var payload = JSON.parse(e.data);
		localStorage.setItem(payload.key, JSON.stringify(payload.data));
	};

加强版（相互通信）：

父窗口：

	var win = document.getElementsByTagName('iframe')[0].contentWindow; // 获取 iframe 子窗口
	var obj = { name: 'Jack' };
	// 存入对象
	win.postMessage(JSON.stringify({key: 'storage', method: 'set', data: obj}), 'http://bbb.com');
	// 读取对象
	win.postMessage(JSON.stringify({key: 'storage', method: "get"}), "*");
	window.onmessage = function(e) {
		if (e.origin != 'http://aaa.com') return;
		// "Jack"
		console.log(JSON.parse(e.data).name);
	};

子窗口：

	window.onmessage = function(e) {
		if (e.origin !== 'http://bbb.com') return;
		var payload = JSON.parse(e.data);
		switch (payload.method) {
			case 'set':
				localStorage.setItem(payload.key, JSON.stringify(payload.data));
				break;
			case 'get':
				var parent = window.parent; // 获取父窗口
				var data = localStorage.getItem(payload.key);
				parent.postMessage(data, 'http://aaa.com');
				break;
			case 'remove':
				localStorage.removeItem(payload.key);
				break;
		}
	};

参考资料：[浏览器的同源策略
](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

### 9. 异步加载 JavaScript

	<script src="http://sample.com/script.js"></script>

这种方式加载 JS 脚本是同步加载，又称阻塞模式，会阻止浏览器的后续处理，只有当资源加载结束后才能进行下一步操作。如果脚本有中有输出 document 内容、修改 DOM、页面重定向等行为，就会造成页面堵塞，为避免阻塞，一般同步加载会放在 body 的末尾。

与同步加载对应的就是异步加载模式，浏览器在下载执行脚本的同时，还会继续进行后续页面的处理。异步加载主要有以下几种方式：

1）async 和 defer

defer：只有 IE 支持。defer 属性规定是否对脚本执行进行延迟，直到页面加载为止。

defer 声明脚本中将不会有 document.write 文档内容创建和 DOM 修改。浏览器会并行下载其他有 defer 属性的 script，而不会阻塞页面后续处理。注：所有的 defer 脚本必须保证按顺序执行的。

    <script src="" defer></script>
 
async：HTML5 新属性。async 属性规定一旦脚本可用，则会异步执行，该属性仅适用通过 src 加载的外部脚本。

脚本将在下载后尽快执行，作用同 defer，但是不能保证脚本按顺序执行。执行都将在 onload 事件之前完成。

    <script src="" ansyc></script>
 
Firefox 3.6、Opera 10.5、IE 9和最新的 Chrome 和 Safari 都支持 async 属性。可以同时使用 async 和 defer，实现浏览器的兼容。

2）使用脚本加载其他脚本，实现所谓的“懒加载”，包括两种方式：

+ 通过 ajax 请求去获取 js 代码，然后通过 eval 函数处理响应
+ onload 处理回调函数，实现 DOM 中动态插入 script 标签

由于 ajax 请求本身比较复杂，加上 eval 会造成作用域泄露、调试复杂等问题，后一种方法更好。

	(function(){
		if(window.attachEvent){
	    	window.attachEvent("load", asyncLoad);
	    }else{
	    	window.addEventListener("load", asyncLoad);
	    }
	    var asyncLoad = function(){
	    	var script = document.createElement('script'); 
	        // script.type = 'text/javascript'; 
	        // script.async = true; 
	        script.src = url; 
	        var head = document.getElementsByTagName('head')[0]; 
	        head.appendChild(script);
	    }
	)();

这种方法只是把插入 script 的方法封装进函数，然后放在 window 的 onload 方法里面执行，从而解决了阻塞 onload 事件触发的问题。

3) 父窗口插入 iframe，在 iframe 中异步执行 js

    var insertJS = function(){alert(2)};
    var iframe = document.createElement("iframe");
    document.body.appendChild(iframe);
    var doc = iframe.contentWindow.document;//获取iframe中的window要用contentWindow属性。
    doc.open();
    doc.write("<script>var insertJS = function(){};<\/script><body onload='insertJS()'></body>");
    doc.close();

一般前两种就可以实现脚本在各浏览器的异步加载了，第三种真的很少见。

### 参考文档

[JS异步加载的三种方式](https://www.cnblogs.com/xkloveme/articles/7569426.html) (写得不错)

[谈谈异步加载JavaScript](https://www.cnblogs.com/jinguangguo/p/4187641.html)