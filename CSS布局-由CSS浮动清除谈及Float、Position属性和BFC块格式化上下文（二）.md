# 由 CSS 浮动清除谈及 Float、Position 属性和 BFC 块格式化上下文（二）
---

## CSS 浮动清除的几种方法

### 1 父级设置合适的 height 直接撑开

这种方法严格来说只能算把父元素撑开，不推荐。

### 2 利用 clear 属性，清除浮动

clear 属性规定元素的哪一侧不允许有其他浮动元素，属性值有 left、right、both。

修改（一）最后的代码：

    <div class="container">
        <div class="boxbg">
            <div class="box"></div>
            <div class="box" style="clear:left;"></div>
            <div class="box"></div>
        </div>
    </div>

此时可得：

![clearleft]("pictures/CSS清除浮动/clearleft.PNG")

clear 禁止了第二个 box 左侧有浮动元素。

对于CSS的清除浮动，一定要牢记：这个规则只能影响使用清除的元素本身，不能影响其他元素。所以如果向第一个 box 添加 clear:right 的样式是无法得到上图效果的。

### 2.1 添加空div清理浮动

对上文代码稍作修改，在父元素的最后添加一个空 div,并设置清理两边的浮动。

    <div class="container">
        <div class="boxbg">
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
			<div style="clear:both;"></div> <!--新增代码-->
        </div>
    </div>

![空div]("pictures/CSS清除浮动/空div.PNG")

### 2.2 CSS 伪元素

	.clearfix:after{
	    content:"";
	    display:table;
	    clear:both;
	}

	<div class="container">
        <div class="boxbg clearfix">
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
			<div style="clear:both;"></div> <!--新增代码-->
        </div>
    </div>

通过 CSS 的 :after 伪元素向父元素的最后添加样式，为其追加一个不可见的块元素。2.1 和 2.2 本质是一样的。

该方法的起源详见 Nicolas Gallagher 在 [A new micro clearfix hack](http://nicolasgallagher.com/micro-clearfix-hack/)。

### 3 使父容器形成BFC

### 3.1 BFC 介绍

块格式化上下文（Block Formatting Context，BFC）是Web页面的可视化CSS渲染的一部分，是按照块级盒子布局的。

其中 FC 即 Formatting Context 指的就是通常而言的普通文本流（常说的文档流其实分为定位流、浮动流和普通流三种），是页面中的一块渲染区域，决定了其子元素如何布局，以及和其他元素之间的关系和作用。常见的 FC 除了 B(Block)FC、I(Inline)FC [CSS2.1]，还有 G(GridLayout)FC 和 F(Flex)FC [CSS3]。

简单来说，一个 BFC 也就是独立隔离的一个块级元素，容器里面的子元素不会影响到外面元素的布局，反之亦然。

一个HTML元素要创建BFC，满足下列的**任意一个或多个条件**即可：

- 根元素，即 HTML 元素

- position（定位元素）：absolute|fixed

- float（浮动元素）：left|right

- display: inline-block|table-cell|flex|inline-flex|grid|inline-grid

- overflow: hidden|auto|scroll（不为 visible）

全部触发方式见 [块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)。

上述触发方式可能会带来一些副作用，如：

+ display: table 可能引发响应性问题

+ overflow: scroll 可能产生多余的滚动条

+ float: left 将把元素移至左侧，并被其他元素环绕

+ overflow: hidden 将裁切溢出元素

+ position：改变元素的定位方式

因此创建块格式化上下文的方式必须基于实际需要考虑。

### 3.2 BFC 特性

1. BFC会阻止垂直外边距（margin-top、margin-bottom）折叠

> 按照BFC的定义，只有同属于一个 BFC 时，两个元素才有可能发生垂直Margin的重叠，这个包括相邻元素，**嵌套元素**，只要他们之间没有阻挡(例如边框，非空内容，padding 等)就会发生 margin 重叠。

阻止垂直外边距折叠的方法很简单，就是让两个相邻元素（或嵌套元素）处于不同的BFC中，这点对于嵌套元素来说更有意义，可以使父元素和子元素的 margin 不发生折叠。

2. BFC 不会重叠浮动元素，可以阻止元素被浮动元素覆盖

3. BFC 可以包含浮动元素，不会产生塌陷

**[深入理解BFC](http://www.cnblogs.com/xiaohuochai/p/5248536.html)** 中进行了 BFC 特性演示，可以看出不同触发条件会带来不同的影响。

### 3.3 父容器成为 BFC 以清理浮动

利用 3.2 中 BFC 特性的第三点，修改代码解决父容器塌陷问题，这里采用了 overflow:hidden 实现。

    <div class="container">
        <div class="boxbg" style="overflow:hidden">
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
        </div>
    </div>

![BFC清除浮动]("pictures/CSS清除浮动/BFC清除浮动.PNG")

### 3.4 BFC 其他注意点

1. BFC 中盒子的对齐方式

在一个BFC中，块盒与行盒（行盒由一行中所有的内联元素所组成）都会垂直的沿着其**父元素的边框**排列。W3C给出得规范是：

> 在BFC中，每一个盒子的左外边缘（margin-left）会触碰到容器的左边缘(border-left)（对于从右到左的格式来说，则触碰到右边缘）。浮动也是如此（尽管盒子里的行盒子 Line Box 可能由于浮动而变窄），除非盒子创建了一个新的 BFC（在这种情况下盒子本身可能由于浮动而变窄）。

![BFC中盒子的对齐方式](https://camo.githubusercontent.com/79bf7dc35ecbddea11eab4edc9eacf6bd88af365/687474703a2f2f7365676d656e746661756c742e636f6d2f696d672f62566d327150)

2. display: flow-root

一个新的 display 属性的值，它可以创建无副作用的BFC。在父级块中使用 display: flow-root 可以创建新的BFC。

> flow-root:
>
> The element generates a block container box, and lays out its contents using flow layout. It always establishes a new block formatting context for its contents. [CSS2]。

主流浏览器 Firefox 53+ 、 Chrome 58+ 和 Opera 45+ 支持 flow-root 属性，可以看出该属性兼容性并不高，实际使用中为了更好对 flow-root 做降级处理，可以通过 CSS 条件属性 @supports() 来做优雅降级处理，应用到本文示例代码：

	@supports(display:flow-root) {
		.boxbg {
			display: flow-root;
		}
	}

@supports 是 CSS3 新增的条件判断，充许根据浏览器对CSS特性的支持情况来定义不同的样式。

上面的代码意思就是，如果浏览器支持 display:flow-root 属性则为 class="boxbg" 的元素运用该样式。

但 @supports 本身也存在兼容问题。

# 参考文档

[CSS清浮动处理（Clear与BFC）](https://www.cnblogs.com/dolphinX/p/3508869.html)

[A new micro clearfix hack](http://nicolasgallagher.com/micro-clearfix-hack/)

[深入理解BFC](http://www.cnblogs.com/xiaohuochai/p/5248536.html)
