# 由 CSS 浮动清除谈及 Float、Position 属性和 BFC 块格式化上下文
---

## 引子

本文的起因在于我在重现一道学习网站上的布局时，自己编写的代码得到的导航栏布局结果如下：

<img src="pictures/CSS清除浮动/导航布局错误.jpg" alt="错误导航布局">

而实际正确的结果如图：

<img src="pictures/CSS清除浮动/导航布局正确.jpg" alt="正确导航布局">

查看后发现是少了一段代码：

	.clearfix::after {
	    content: "";
	    clear: both;
	    display: table;
	}

其中，正确代码中将除去 header 和 footer 的主体区域放在一个大类 clearfix 中。

