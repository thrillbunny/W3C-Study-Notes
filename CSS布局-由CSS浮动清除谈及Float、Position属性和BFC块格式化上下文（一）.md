# 由 CSS 浮动清除谈及 Float、Position 属性和 BFC 块格式化上下文（一）
---

## 引子

本文的起因在于我在重现一道学习网站上的布局时，自己编写的代码得到的导航栏布局结果如下：

![错误导航布局](pictures/CSS清除浮动/导航布局错误.PNG)

而实际正确的结果如图：

![正确导航布局](pictures/CSS清除浮动/导航布局正确.PNG)

可以很明显的看出问题所在：底部的 footer 背景蔓延到了主区域。

这是因为主内容区域设置了 float:left; 一方面可以使主内容区的 sidemenu 边栏和 content 文章内容形成单行布局，但也使它们在普通流中脱离了父容器，父容器没有把浮动的子元素包围起来，造成了父元素的塌陷，这就需要清除父容器两边的浮动，使父容器能够撑开。

回到本文的导航栏布局，可以采用一个非常经典的方法来清除浮动：

	.clearfix::after {
	    content: "";
	    clear: both;
	    display: table;
	}

其中，将包括 sidemenu 边栏和 content 文章内容的主体区域放在一个大类 clearfix 中。

## Float、Position 属性

### 1. HTML 布局

首先要对 HTML 布局的两个基本概念有清楚的认识。

### 1.1 盒子模型

HTML中元素的盒子模型分为两种：**块级元素（Block）**和**内联元素（Inline）**。两种盒子模型区别如下：

1. 块级（Block）类型的元素会独占一行，内联（Inline）类型的元素则都会在一行内显示直到该行排满，前后不会自动换行（但可以通过设置 display：block/inline/inline-block 改变元素的布局方式）；

2. 默认情况下，Block 元素宽度默认为其父元素宽度的 100%，内联（Inline）元素根据自身的内容及子元素来决定宽度；

3. 块级（Block）类型的元素可以设置 width、height 属性，而内联（Inline）类型设置无效；

4. 块级（Block）元素可以设置水平和垂直方向的 margin 和 padding 属性；内联（Inline）元素水平方向的padding-left, padding-right, margin-left, margin-right都会产生边距效果，但垂直方向的padding-top, padding-bottom, margin-top, margin-bottom不会产生边距效果。

列举一些常见的元素分类：

- 块级元素有 DIV, FORM, TABLE, P, PRE, H1~H6, DL, OL, UL 等。

- 常见的内联元素有 SPAN, A, STRONG, EM, LABEL, INPUT, SELECT, TEXTAREA, IMG, BR 等。

### 1.2 HTML 普通文本流

浏览器在读取HTML源代码的时候是根据元素在代码出现的顺序读取，最终元素的呈现方式是依据元素的盒子模型来决定的。行内元素从左到右，块状元素从上到下按顺序排列。

元素在HTML的普通流中会“占用”一个位置，而“占用”位置的大小、位置则是由元素的盒子模型来决定。但有一些方法可以使元素脱离 HTML 的普通文本流。

### 2. Position 定位

position 的属性值共有四个 static、relative、absolute、fixed、sticky。

#### static

HTML 元素的默认值，即没有定位，遵循正常的文档流对象。静态定位的元素不会受到 top, bottom, left, right 影响。

#### relative

俗称相对定位，相对定位元素的定位是**相对元素的默认位置**，即将元素偏离元素的默认位置，但普通文本流中依然保持着原有的占用空间，并没有脱离普通文本流，只是元素发生视觉上发生的偏移。

相对定位元素后的正常文本流中元素会根据相对定位元素原本的位置向下排列。相对定位并不会改变内联（Inline）元素的display属性。

	<style>
	div {
	width: 100px;
	height: 50px;
	text-align: center;
	line-height: 50px; /*垂直居中*/
	margin: 10px;
	color: white;
	}	
	.move {
	position: relative;
	top: 30px;
	left:40px;
	}
	</style>
	
	<div style="background: orange;">orange</div>
	<div style="background: lightblue;" class="move">lightblue</div>
	<div style="background: lightgreen;">lightgreen</div>

![relative](pictures/Position/relative.PNG)

相对定位元素经常被用来作为绝对定位元素的容器块。

#### absolute

俗称绝对定位，绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>根节点。

应用了 position: absolute 的元素会脱离页面中的普通文本流，不占用空间，宽高塌陷，在普通文本流之上（z-index），并改变 display 属性，所以会与其他元素发生重叠。

	<style>
		div{
		  margin: 0 auto;
		  border: 1px solid;
		}
		.box{
		  width: 400px;
		  height: 200px;
		}
		.box1 {
		  width: 300px;
		  height: 180px;
		}
		.box2 {
		  width: 250px;
		  height: 140px;
		}
		.box3 {
		  width: 200px;
		  height: 100px;
		  position: absolute; top: 0; left: 0;
		}
	<style>
	<body>
		<div style="background: yellow;" class="box">
		    <div style="background: #fff" class="box1">
		        box1
		        <div style="background: #81b6ff" class="box2">
		            box2
		            <div style="background: #b6ff00;" class="box3">
		                box3
		            </div>
		        </div>
		    </div>
		</div>
	</body>

没有设置绝对定位元素的实际情况：

![初始情况](pictures/Position/absolute初始.PNG)

设置 box2 元素为绝对定位，对比设置不同父元素的情况：

1.不设置父元素

![不设置父元素](pictures/Position/没有设置父.PNG)

2.设置 box1 为父元素

![设置 box1 为父元素](pictures/Position/设置box1为父.PNG)

3.设置 box2 为父元素

![设置 box2 为父元素](pictures/Position/设置box2为父.PNG)

本文的用例设置了每个盒子的宽高，而参考文档 [对CSS中的Position、Float属性的一些深入探讨](http://www.cnblogs.com/coffeedeveloper/p/3145790.html#html) 中提出了更多情况，得到了下面的结论：

> 1）块状元素在position(relative/static)的情况下width默认为100%，但是设置了position: absolute之后，会将width变成auto。

> 2）元素设置了position: absolute之后，如果没有设置top、bottom、left、right属性的话，浏览器会默认设置成auto，而auto的值则是该元素的“默认位置”。即设置position: absolute前后的offsetTop和offsetLeft属性值不变。

> 特殊情况：

> Firefox的话会直接将top、left设置成offsetTop和offsetLeft的值而非auto。

> IE7下的表现更类似于float，会附加到父元素的末尾。

针对第 2 点，本文进行了尝试，去除了 top 和 left 设置，得到结果：

![不设置top和left](pictures/Position/不设置top和left.PNG)

也就是相对定位元素不会根据父元素进行定位，而是位于其默认位置。

【补充】

1. 应用了position: relative/absolute的元素，margin属性仍然有效，建议不要设置margin数据，因为很难精确元素的定位，尽量减少干扰因素；

2. position: absolute 忽略根元素的 padding；

3. inline 元素在应用了 position：absolute 之后会**改变 display 为 block**，应用了 relative 则不会；

4. 应用了 position: absolute / relative 之后，会覆盖其他非定位元素（即 position 为 static 的元素），如果不想覆盖到其他元素，也可以将 z-index 设置成 -1。

#### fixed
元素的位置相对于浏览器窗口是固定位置，即使窗口是滚动的它也不会移动。

存在兼容性问题， fixed 定位在 IE7 和 IE8 下需要声明 !DOCTYPE 才能支持。

fixed 和 absolute 有很多共同点：

1.会改变行内元素的呈现模式，使元素的display变更为block；

2.会让元素脱离文档流普通流，不占据空间；

3.fixed定位的元素默认会覆盖到非定位元素上。

fixed 与 absolute 最大的区别：

absolute 的父元素是可以被设置的，而 fixed 父元素固定为浏览器窗口。即当你滚动网页，其元素相对浏览器窗口的定位是恒定不变的。

#### sticky

俗称粘性定位，基于用户的滚动位置来定位。

粘性定位的元素是依赖于用户的滚动，在 position:relative 与 position:fixed 定位之间切换。在目标区域范围内，它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed，会固定在目标位置。

元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。
这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

存在兼容性问题，Internet Explorer、Edge 15 及更早 IE 版本不支持 sticky 定位。 Safari 需要使用 -webkit-sticky。 

### 3. Float 属性

float 的属性值有：none、left、right，该属性能定义元素向左/右方向浮动（只能横向浮动）。

当元素应用了 float 属性后，将会脱离普通文本流，其容器（父元素）将得不到脱离普通流的子元素高度，可能会造成塌陷，如本文的引子中所示。

float 会将元素的 display 属性变更为 block。

浮动元素的后一个元素会围绕着浮动元素（典型运用是为图片设置 float 属性，形成文本围绕的效果），与应用了position的元素相比，浮动元素并不会遮盖后一个元素；浮动元素的前一个元素不会受到任何影响。设置多个元素的 float 属性可使它们并排显示。

### 3.1 与 position 的兼容性问题

1. 与 position: relative 兼容问题

元素同时应用了position: **relative**、float、（top / left / bottom / right）属性后，则元素**先浮动**到相应的位置，然后再根据（top / left / bottom / right）所设置的距离来发生偏移。

2. 与 position: absolute 兼容问题

元素同时应用了 position: absolute 及 float 属性，则 **float 失效**（绝对定位优先级更高）。

	<div style="position: absolute; right:10px; top: 10px; float: left;">
		我是一个应用了position：absolute和float：left的DIV，不过我还是在浏览器的右边，没有浮动到左边。
	</div>

3. 同时应用 position: absolute 和 float: left 会导致清除浮动无效。

### 3.2 实现块级元素的横向排布

方法1：将需要横向排布的块级元素均设置 display:inlin-block；

方法2：将需要横向排布的块级元素均 float：left。元素添加 float之后，此浮动元素会在其碰到父级元素边框或者前一个浮动元素边框后紧邻其后显示。

在浮动元素总宽度大于父级元素时自动换行，换行的时候遇到前一个float并在其后显示。如下图例子：

	<style>
	    .boxBg{
	    margin: 0 auto;
	    width:500px;
	    height:120px;
	    border:2px solid #ccc;
	    }
	    .box1{
	    width:100px;
	    height:100px;
	    background-color:orange;
	    float:left;
	    }
	    .box2{
	    width:140px;
	    height:50px;
	    background-color:lightblue;
	    float:left;
	    }
	    .box3{
	    width:400px;
	    height:50px;
	    background-color:lightgreen;
	    float:left;
	    }
	</style>
	<div class="boxBg">
		<div class="box1">orange</div>
		<div class="box2">lightblue</div>
		<div class="box3">lightgreen</div>
	</div>

![块元素横向布局](pictures/Position/块元素横向.PNG)

如果设置的是 inline-block，lightgreen 框就不会在 orange 后，而是另起一行。

## Float 带来的困扰

浮动布局虽然能带来一定便利，但也产生了新的问题。

### 问题1 float 元素占用的空间

还是拿 3.2 中代码来说，如果 lightblue 和 lightgreen 没有设置 float 属性，就会产生如下效果：
 
![浮动元素遮盖](pictures/Position/浮动元素遮盖.PNG)

orange 框遮住了后面两个框的一部分，这是因为 float 元素虽然脱离了普通文本流，不占据**块级空间**，但会占据另外的空间，引用 [关于css float 属性以及position:absolute 的区别](https://www.cnblogs.com/enemy/p/3750588.html) 文中的话：

> 浮动元素会占据另外的空间，也就是行框空间，通俗的讲就是文本所占的空间

即浮动元素会影响**块级元素之内的文字以及内联元素**，所以才能实现文本环绕效果。

### 问题2 float 元素导致的父元素塌陷

	<!doctype html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Clear float</title>
	    <style type="text/css">
	        .container{
	            margin: 30px auto;
	            width:600px;
	            height: 300px;
	        }
	        .boxbg{
	            border:solid 3px orange;
	        }
	        .box{
	            width: 100px;
	            height: 100px;
	            background-color: lightgreen;
	            margin: 10px;
	            float: left;
	        }
	    </style>
	</head>
	<body>
	    <div class="container">
	        <div class="boxbg">
	            <div class="box"></div>
	            <div class="box"></div>
	            <div class="box"></div>
	        </div>
	    </div>
	</body>
	</html>

实际的结果是这样的：

![父元素塌陷](pictures/CSS清除浮动/父元素塌陷.PNG)

但我们希望看到的是有橙色边框包围三个盒子，这就是因为父容器并没有把浮动的子元素包围起来，父子间的 margin 和 padding 设置值不能正确被显示，俗称塌陷。

下一章具体讨论如何清除父元素塌陷和什么是 BFC 的问题。

# 参考文档

[对CSS中的Position、Float属性的一些深入探讨](http://www.cnblogs.com/coffeedeveloper/p/3145790.html#html)

[菜鸟教程-CSS Position](http://www.runoob.com/css/css-positioning.html)

[关于css float 属性以及position:absolute 的区别](https://www.cnblogs.com/enemy/p/3750588.html)
