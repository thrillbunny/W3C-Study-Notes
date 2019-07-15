# WebSocket

---

### 1. WebSocket 是什么

WebScoket 是一种让客户端和服务器之间能进行双向实时通信的技术。
（大红书的原文：在一个单独的持久连接上提供全双工双向通信）

[HTTP和WebSocket区别](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051502.png)

它是 HTML 最新标准 HTML5 的一个协议规范，有以下特点：

1）本质上是个**基于 TCP 的协议**，服务器实现简单，它通过 HTTP/HTTPS 协议发送一条特殊的请求进行握手后创建了一个 TCP 连接，此后浏览器/客户端和服务器之间便可以通过此连接来进行双向实时通信。(相当于建立了一个特殊通道）；
2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
3）自定义协议，数据格式比较轻量，性能开销小，通信高效，不像 HTTP 那样是字节级的开销；
4）可以发送文本或发送二进制数据，复杂数据结构可以先序列化为 JSON 字符串；
5）**没有同源限制**，客户端可以与任意服务器通信（是否能够通信取决于服务器）；
6）协议标识符是ws（如果加密，则为wss），如：

	ws://example.com:80/some/path

### 2. WebSocket 与 HTTP、TCP、Socket 的关系

WebSocket 和 HTTP 都属于**应用层协议**，且都是**基于 TCP** 的，它们的 send 函数最终也是通过 TCP 系统接口来做数据传输。

那么 WebSocket 和 HTTP 的关系呢？

WebSocket 和 HTTP 都是基于 TCP 的可靠传输协议，都位于应用层；
WebSocket 在**建立握手连接**时，数据是通过 HTTP 协议传输的，但是在连接建立后，真正的数据传输阶段则不需要 HTTP 协议的参与。
（大红书原文：在 JavaScript 中创建了 Web Socket 之后，会有一个 HTTP 请求发送到浏览器以发起连接。在取得服务器响应后，建立的连接会使用 HTTP 升级从 HTTP 协议交换为 WebSocket 协议。）

它们之间的关系如下两图：

![HTTP和TCP关系1](https://images2017.cnblogs.com/blog/1247371/201711/1247371-20171120120707258-353166864.gif)

![HTTP和TCP关系2](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051503.jpg)

WebSocket 和 Socket 虽然名字很像，其实是完全不同的概念。

Socket 其实并不是一个协议，而是为了方便使用 TCP 或 UDP 而抽象出来的一层，是位于应用层和传输层之间的一组接口。

> Socket 是应用层与 TCP/IP 协议族通信的中间软件抽象层，它是一组接口。
在设计模式中，Socket 其实就是一个门面模式，它把复杂的 TCP/IP 协议族隐藏在 Socket 接口后面，对用户来说，一组简单的接口就是全部，让 Socket 去组织数据，以符合指定的协议。

当两台主机通信时，必须通过 Socket 连接，Socket 则利用 TCP/IP 协议建立 TCP 连接。TCP 连接则更依靠于底层的 IP 协议，IP 协议的连接则依赖于链路层等更低层次。

而 WebSocket 则是 H5 提出的典型的应用层协议。

### 3. WebSocket 优势

1）HTTP 协议被设计为无状态、半双工、单向通信的，通信只能由客户端发起（一个巨大的缺陷），即客户端请求一次，服务器回复一次。

如果服务器有连续的状态变化，服务器消息想及时下发到客户端，就需要采用类似于**轮询**的机制，即客户端定时频繁的向服务器发出请求，了解服务器有没有新的消息，通信效率很低（HTTP不停重连或者始终连接），而且 HTTP 数据报头本身的字节量较大，浪费了大量带宽和服务器资源；

2）为提高效率，出现了 **AJAX/Comet** 技术，它实现了客户端和服务器双向通信，且节省了一定带宽，但仍然需要发出请求，本质上仍然是**轮询**；

3）新一代 HTML 标准 HTML5 推出了 WebSocket 技术，它使客户端和服务器之间能通过 HTTP 协议建立 TCP 连接，之后便可以随时随地进行双向通信，且交换的数据报头信息量很小；

4）在需要即时通讯的前提下，如网页的 QQ 等实时聊天系统、游戏需要同时支持手机端和 Web 端实现双屏互动，就需要使用 WebSocket；但如果游戏不需要支持 Web 端，且对实时性要求比较高，如多人射击、MMORPG 之类，那么使用 TCP/UDP 结合的原生 Socket 会更好；

5）自定义协议，传递的数据包量很小，适合移动应用，因为移动应用带宽和网络延迟都是非常关键的解决点。

### 4. 如何使用 WebSocket？

客户端：

在支持 WebSocket 的浏览器中，创建 Socket 之后，通过 onopen、onmessage、onclose、onerror 四个事件的实现来处理 Socket 的响应，可以通过 send 传输数据给服务器。

注意 WebSocket 对象不支持 DOM 2级事件侦听器，因此必须使用 DOM 0 级语法分别定义每个事件处理程序。

message 事件把返回的数据保存在 event.data 中。

	var socket = new WebSocket("ws://www.example.com/server.php");

	var message = {
		time: new Date(),
		text: "Hello world!",
		clientId: "asdfp8734rew"
	};
	socket.send(JSON.stringify(message));

	socket.onopen = function(){
		alert("Connection established.");
	};

	socket.onmessage = function(event){
		var data = event.data;
		//处理数据
	};

	socket.onerror = function(){
		alert("Connection error.");
	};

	socket.onclose = function(){
		alert("Connection closed.");
	};

其中， close 事件的 event 对象有三个额外的属性： wasClean、 code 和 reason，其中 wasClean 是一个布尔值，表示连接是否已经明确地关闭，code 是服务器返回的数值状态码，而 reason 包含服务器发回的消息。

	socket.onclose = function(event){
		console.log("Was clean? " + event.wasClean + " Code=" + event.code + " Reason="
		+ event.reason);
	};

readyState 属性返回实例对象的当前状态，共有四种:

+ WebSocket.OPENING (0)：正在建立连接
+ WebSocket.OPENING (1)：已经建立连接
+ WebSocket.CLOSING (2)：正在关闭连接
+ WebSocket.CLOSE (3)：已经关闭连接

注意 WebSocket 没有 readystatechange 事件。 

服务器端：

node 可以使用 [nodejs-websocket](https://www.npmjs.com/package/nodejs-websocket) 或者 [socket.io](https://github.com/socketio/socket.io)等。

一个简单但具体的实现可以看我自己写的那个简易聊天室（没有做心跳检测），服务器端使用的是 nodejs-websocket。

WebSocket 是 HTML5最新提出的规范，虽然主流浏览器都已经支持，但仍然可能有不兼容的情况，为了兼容所有浏览器，给程序员提供一致的编程体验，socket.io 将 WebSocket、AJAX 和其它的通信方式全部封装成了统一的通信接口，在使用 socket.io 时，不用担心兼容问题，底层会自动选用最佳的通信方式。因此说，WebSocket 是 socket.io 的一个子集。（除了基于 node.js 的 socket.io 外还有很多许多语言、框架和服务器都提供了 WebSocket 支持）

[socket.io 官网（结合 express）](https://socket.io/get-started/chat/)

socket.io 最核心的两个api是 emit 和 on ，服务端和客户端都有这两个api。
通过 emit 和 on 可以实现服务器与客户端之间的双向通信。

+ emit ：发射一个事件，第一个参数为事件名，第二个参数为要发送的数据，第三个参数为回调函数（如需对方接受到信息后立即得到确认时，则需要用到回调函数）。 
+ on ：监听一个 emit 发射的事件，第一个参数为要监听的事件名，第二个参数为回调函数，用来接收对方发来的数据，该函数的第一个参数为接收的数据。

服务器端：

	var app = require('express')();
	var http = require('http').createServer(app);
	var io = require('socket.io')(http); // initialize a new instance of socket.io by passing the http object
	
	app.get('/', function(req, res){
	  res.sendFile(__dirname + '/index.html');
	});
	
	io.on('connection', function(socket){
	  socket.on('chat message', function(msg){ // on
	    console.log('message: ' + msg);
	  });
	});
	
	http.listen(3000, function(){
	  console.log('listening on *:3000');
	  socket.on('disconnect', function(){
	    console.log('user disconnected');
	  });
	});


客户端：

	<script src="/socket.io/socket.io.js"></script>
	<script src="https://code.jquery.com/jquery-1.11.1.js"></script>
	<script>
	  $(function () {
	    var socket = io();
	    $('form').submit(function(e){
	      e.preventDefault(); // prevents page reloading
	      socket.emit('chat message', $('#m').val()); // emit
	      $('#m').val('');
	      return false;
	    });
	  });
	</script>

### 5. WebSocket 连接过程

连接过程 —— 握手过程：

	1. 浏览器、服务器建立 TCP 连接，三次握手。这是通信的基础，传输控制层，若失败后续都不执行。
	2. TCP 连接成功后，浏览器通过HTTP协议向服务器传送 WebSocket 支持的版本号等信息。（开始前的HTTP握手）
	3. 服务器收到客户端的握手请求后，同样采用 HTTP 协议回馈数据。
	4. 当收到了连接成功的消息后，通过 TCP 通道进行传输通信。

### 6. WebSocket 心跳检测

Websocket 实现的是客户端和服务器端的长连接，在连接过程中可能连接失效等，为保证连接的可靠性和稳定性需要建立 WebSocket 心跳重连机制。

> 在使用原生 WebSocket 的时候，如果设备网络断开，不会立刻触发 WebSocket t的任何事件，前端也就无法得知当前连接是否已经断开。这个时候如果调用 websocket.send 方法，浏览器才会发现连接断开了，便会立刻或者一定短时间后（不同浏览器或者浏览器版本可能表现不同）触发 onclose 函数。

> 后端 Websocket 服务也可能出现异常，造成连接断开，这时前端也并没有收到断开通知，因此需要前端定时发送心跳消息 ping，后端收到 ping 类型的消息，立马返回 pong 消息，告知前端连接正常。如果一定时间没收到 pong 消息，就说明连接不正常，前端便会执行重连。

> 为了解决以上两个问题，以前端作为主动方，定时发送 ping 消息，用于检测网络和前后端连接问题。一旦发现异常，前端持续执行重连逻辑，直到重连成功。

改写客户端代码：

	var socket = new WebSocket(url);

	var message = {
		time: new Date(),
		text: "Hello world!",
		clientId: "asdfp8734rew"
	};
	socket.send(JSON.stringify(message));

	socket.onopen = function(){
		alert("Connection established.");
	};

	socket.onmessage = function(event){
		var data = event.data;
		// 处理数据
	};

	socket.onerror = function(){
		reconnect(url);
	};

	socket.onclose = function(){
		reconnect(url);
	};

	function reconnect(url) {
		// 设置延迟避免请求过多
		var timer = setTimeout(function() {
			if(socket.readyState === 2 || socket.readyState === 3) {
				socket.onopen();
			} else if(socket.readyState === 1) {
				clearTimeout(timer);
			}
		}, 500);
	}

此时正常情况下失去连接会触发 onclose 方法，执行重连。

针对断网情况的心跳重连需要定时发送消息，触发 websocket.send 方法，如果网络断开了，浏览器便会触发 onclose 事件。

	var heartCheck = {
	    timeout: 10000, // 10ms
	    timeoutObj: null,
		serverTimeoutObj: null,
	    reset: function(){
	        clearTimeout(this.timeoutObj);
	　　　　 this.start();
	    },
	    start: function(){
	        this.timeoutObj = setTimeout(function(){
	            //这里发送一个心跳，后端收到后，返回一个心跳消息
	            //onmessage拿到返回的心跳就说明连接正常
	            ws.send("HeartBeat");
	        }, this.timeout)
	    }
	}
	
	ws.onopen = function () {
	   heartCheck.start();
	};
	
	ws.onmessage = function (event) {
	    heartCheck.reset();
	}

查看网上的一段图片传输重连的完整代码：

	// 图像预览 websocket
	var ws;
	
	// 是否有ajax的返回消息时调用, 如果有返回值 -> 值为true, 执行重连， 否则 -> 为 false，就不会执行重连
	var flag;
	
	// 图片
	var img = "";
	
	function createWebSocket(url) {
	    flag = true;
	    try {
	　　　　 // 如果websocket不存在的时候 实例化websocket，并且调用websocket的函数
	        if (ws == null || typeof ws !== WebSocket) {
	            ws = new WebSocket(url);
	            initEventHandle();
	        } else {
	            reconnect(url);
	        }
	    } catch (e) {
	        $('#image').attr('src', './images/480x270.png');
	    }
	}
	
	// 判断是否有图像传回来
	var hasImage = false;
	
	function initEventHandle() {
	    ws.onclose = function (event) {
	        if (flag) {
	            reconnect(wsUrl);
	        } else { }
	    };
	    ws.onerror = function (event) {
	        if (flag) {
	            reconnect(wsUrl);
	        } else { }
	    };
	    ws.onopen = function () {
	        //心跳检测重置
	        heartCheck.reset().start();
	    };
	    ws.onmessage = function (event) {
	        //如果获取到消息，心跳检测重置
	        //拿到任何消息都说明当前连接是正常的
	        var data = JSON.parse(event.data);
			var serverTimer;

	　　　　 // 如果没有image字段，没有图像传回来，开始定时器，这时 hasImage是false，不会进到websocket关闭的函数，
	　　　　　　 如果没有接到字段，hasImage值为true，一秒钟之后，计时器会进入到onclose事件，
	　　　　　　 其间如果接收到字段，hasImage值为false，计时器也将不会执行onclose事件
	        if (!data.image) {
	            serverTimer = setTimeout(function () { //如果超过一定时间还没重置，说明后端主动断开了
	                if (hasImage) {
	                    if ( !(ws.readyState == 2 || ws.readyState == 3) ) {
	                        ws.onclose();
	                        ws.close();
	                    }
	                    $('#image').attr('src', './images/480x270.png');
	                }
	            }, 1000)
	            hasImage = true;
	        } else {
	　　　　　// 有image字段传回来，清空定时器，hasImage变量为false
	            hasImage = false;
	            clearTimeout(serverTimer);
	        }       
	　　　　 // 有image图像，保存到img变量，如果没有图片传回来，可以显示上一张图片，避免页面出现空白
	        if (data.image) img = data.image;
	if (img == "") $('#image').attr('src', './images/480x270.png');
	        　　$('#image').attr('src', 'data:image/png;base64,' + img);
	           
	　　　　　// 将数据置空
	        data = null;
	    }
	}
	
	function reconnect(url) {
	    //没连接上会一直重连，设置延迟避免请求过多
	    var timer = setTimeout(function () {
	        if (ws.readyState == 2 || ws.readyState == 3) {
	            ws.onopen();
	        } else if (ws.readyState == 1) {
	            clearTimeout(timer);
	        }
	    }, 500);
	
	}
	
	
	//心跳检测
	var heartCheck = {
	    timeout: 1000,//1秒
	    timeoutObj: null,
	    serverTimeoutObj: null,
	    reset: function () {
	        clearTimeout(this.timeoutObj);
	        return this;
	    },
	    start: function () {
	        var self = this;
	        this.timeoutObj = setTimeout(function () {
	            //这里发送一个心跳，后端收到后，返回一个心跳消息，
	            //onmessage拿到返回的心跳就说明连接正常
	            ws.send('{\"token\":\"\", \"code\": \"1\"}');
	        }, this.timeout)
	    }
	}

### 6. Websocket 协议报文

Websocket 协议有两部分：握手和数据传输。

来自客户端的握手看起来像如下形式：

	GET /chat HTTP/1.1
	Host: server.example.com
	Upgrade: websocket
	Connection: Upgrade
	Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
	Origin: http://example.com
	Sec-WebSocket-Protocol: chat, superchat
	Sec-WebSocket-Version: 13

来自服务器的握手看起来像如下形式：

	HTTP/1.1 101 Switching Protocols
	Upgrade: websocket
	Connection: Upgrade
	Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
	Sec-WebSocket-Protocol: chat

一旦客户端和服务器都发送了它们的握手，且握手成功，接着开始数据传输部分。 这时便建立了双向通信信道，两端彼此独立，随意发生数据。

主要的请求首部意义如下：

Connection: Upgrade：表示要升级协议
Upgrade: websocket：表示要升级到 websocket 协议。
Sec-WebSocket-Version: 13：表示 websocket 的版本。如果服务端不支持该版本，需要返回一个 Sec-WebSocket-Versionheader ，里面包含服务端支持的版本号。
Sec-WebSocket-Key：与后面服务端响应首部的 Sec-WebSocket-Accept 是配套的，提供基本的防护，比如恶意的连接，或者无意的连接。

主要的响应首部如下：

状态代码101表示协议切换，协议升级
Sec-WebSocket-Accept：根据客户端请求首部的 Sec-WebSocket-Key 计算出来，计算伪代码如下：
> toBase64(sha1(Sec-WebSocket-Key + 258EAFA5-E914-47DA-95CA-C5AB0DC85B11))

### 7. WebSocket 和 SSE

SSE （ Server-sent Events ）是 WebSocket 的一种轻量代替方案，使用 HTTP 协议。
严格地说，HTTP 协议是没有办法做服务器推送的，但是当服务器向客户端声明接下来要发送**流信息**时，客户端就会保持连接打开，SSE 使用的就是这种原理。

SSE 是单向通道，只能服务器向客户端发送消息，如果客户端需要向服务器发送消息，则需要一个新的 HTTP 请求。 
如果需要实时通信，平均每秒会向服务器发送一次消息的话，那应该选择 WebSocket。如果频率较低的话，差异就不大。
需要用新数据局部更新网络应用时，SSE 可以做到不需要用户执行任何操作，便可以完成。

举例我们要做一个统计系统的管理后台，我们想知道统计数据的实时情况（单向推送）。类似这种更新频繁、低延迟的场景，SSE 可以完全满足。
其他一些应用场景：例如邮箱服务的新邮件提醒，微博的新消息推送、管理后台的一些操作实时同步等，SSE 都是不错的选择。

SSE还有下面几个优点：

- 实现一个完整的服务仅需要少量的代码；
- 可以在现有的服务中使用，不需要启动一个新的服务；
- 可以用任何一种服务端语言中使用；
- 基于 HTTP/HTTPS 协议，可以直接运行于现有的代理服务器和认证技术。

Server-sent Events 规范也是 HTML 5 规范的一个组成部分，规范主要由两个部分组成：

　　第一部分是服务器端与浏览器端之间的通讯协议，

　　第二部分则是在浏览器端可供 JavaScript 使用的 EventSource 对象。

通讯协议是基于纯文本的简单协议。服务器端的响应的内容类型是“text/event-stream”。响应文本的内容可以看成是一个事件流，由不同的事件所组成。

每个事件由类型和数据两部分组成，同时每个事件可以有一个可选的标识符。不同事件的内容之间通过仅包含回车符和换行符的空行（“\r\n”）来分隔。每个事件的数据可能由多行组成。服务器响应的一个实例如下：

	data: first event
	 
	data: second event
	id: 100
	 
	event: myevent
	data: third event
	id: 101
	 
	: this is a comment
	data: fourth event
	data: fourth event continue

每个事件之间通过空行来分隔。对于每一行来说，冒号（“:”）前面表示的是该行的类型，冒号后面则是对应的值。可能的类型包括：

- 类型为空白，表示该行是注释，会在处理时被忽略。
- 类型为 data，表示该行包含的是数据。以 data 开头的行可以出现多次。所有这些行都是该事件的数据。
- 类型为 event，表示该行用来声明事件的类型。浏览器在收到数据时，会产生对应类型的事件。
- 类型为 id，表示该行用来声明**事件的标识符**。
- 类型为 retry，表示该行用来声明浏览器在连接断开之后进行再次连接之前的等待时间。

如果服务器端返回的数据中包含了事件的标识符 id，浏览器会记录最近一次接收到的事件的标识符。如果与服务器端的连接中断，当浏览器端再次进行连接时，会通过 HTTP 首部字段 **Last-Event-ID** 来声明最后一次接收到的事件的标识符。服务器端可以通过浏览器端发送的事件标识符来确定从哪个事件开始来继续连接。
SSE默认支持断线重连机制，在连接断开时会 触发 EventSource 的 error 事件，同时自动重连。再次连接成功时 EventSource 会把 Last-Event-ID 属性作为请求头发送给服务器，这样服务器就可以根据这个 Last-Event-ID 作出相应的处理。虽然 id 并不是必须项，为保证数据可靠，需要给每个信息加上 id 属性。

对于服务器端返回的响应，浏览器端需要在 JavaScript 中使用 EventSource 对象来进行处理。EventSource 使用的是标准的事件监听器方式，只需要在对象上添加相应的事件处理方法即可。EventSource 提供了三个标准事件：open、message 和 error。

	var es = new EventSource('events');
	es.onmessage = function(e) {
	    console.log(e.data);
	};
	 
	es.addEventListener('myevent', function(e) {
	    console.log(e.data);
	});

下面是一个简单的实例实现 SSE：

服务器端：
	
	const http = require('http');
	
	http.createServer((req, res) => {
	
	  // 服务器声明接下来发送的是事件流
	  res.writeHead(200, {
	    'Content-Type': 'text/event-stream', // 服务器端响应的内容类型
	    'Cache-Control': 'no-cache',
	    'Connection': 'keep-alive',
	    'Access-Control-Allow-Origin': '*',
	  });
	
	  // 发送消息
	  setInterval(() => {
	    res.write('event: slide\n'); // 事件类型
	    res.write(`id: ${+new Date()}\n`); // 消息 ID
	    res.write('data: 7\n'); // 消息数据
	    res.write('retry: 10000\n'); // 重连时间
	    res.write('\n\n'); // 消息结束
	  }, 3000);
	
	  // 发送注释保持长连接
	  setInterval(() => {
	    res.write(': \n\n');
	  }, 12000);
	}).listen(2000);

服务器首先向客户端声明接下来发送的是事件流（ text/event-stream ）类型的数据，然后就可以向客户端多次发送消息。

客户端：

	if (window.EventSource) {
	  // 创建 EventSource 对象连接服务器
	  const source = new EventSource('http://localhost:2000');
	
	  // 连接成功后会触发 open 事件
	  source.addEventListener('open', () => {
	    console.log('Connected');
	  }, false);
	
	  // 服务器发送信息到客户端时，如果没有 event 字段，默认会触发 message 事件
	  source.addEventListener('message', e => {
	    console.log(`data: ${e.data}`);
	  }, false);
	
	  // 自定义 EventHandler，在收到 event 字段为 slide 的消息时触发
	  source.addEventListener('slide', e => {
	    console.log(`data: ${e.data}`); // => data: 7
	  }, false);
	
	  // 连接异常时会触发 error 事件并自动重连
	  source.addEventListener('error', e => {
	    if (e.target.readyState === EventSource.CLOSED) {
	      console.log('Disconnected');
	    } else if (e.target.readyState === EventSource.CONNECTING) {
	      console.log('Connecting...');
	    }
	  }, false);
	} else {
	  console.error('Your browser doesn\'t support SSE');
	}

创建了一个 EventSource 对象，传入参数：url，并且根据服务器的状态和发送的信息作出响应。

EventSource从父接口 EventTarget 中继承了属性和方法，其内置了 3 个 EventHandler 属性、2 个只读属性和 1 个方法：

EventHandler 属性：

- EventSource.onopen 在连接打开时被调用。
- EventSource.onmessage 在收到一个没有 event 属性的消息时被调用。
- EventSource.onerror 在连接异常时被调用。

只读属性：

- EventSource.readyState 一个 unsigned short 值，代表连接状态。可能值是 CONNECTING (0), OPEN (1), 或者 CLOSED (2)。
- EventSource.url 连接的 URL。

方法：

-EventSource.close() 关闭连接。

结合 XHR, SSE 完全可以替代 WebSocket 的功能，但这里我们只稍作了解，之后有时间再进一步学习。

推荐这篇文章

[SSE技术详解：使用 HTTP 做服务端数据推送应用的技术](https://www.cnblogs.com/goloving/p/9196066.html)

---

### 参考文档

[WebSocket和SocketIO总结](https://www.cnblogs.com/foupwang/p/7865694.html)

[WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html?utm_source=tuicool&utm_medium=referral)

[初探和实现websocket心跳重连(npm: websocket-heartbeat-js)](https://www.cnblogs.com/1wen/p/5808276.html)

[websocket的重连](https://www.cnblogs.com/una1804/p/9116890.html)

[WebSocket 详解](https://segmentfault.com/a/1190000012948613)