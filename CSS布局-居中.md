#CSS布局问题详解一 居中
---
## 1 CSS居中

### 1.1 水平居中 Horizontally
#### 1.1.1 inline or inline-* elements(text,link,etc)
	.center-child {
		text-align:center;
	}
对于块级元素内的内联元素(inline,line-block,inline-flex,inline-table)有效。

#### 1.1.2	block level element
	.center-me {
		margin: 0 auto;
		width: 200px;
	}
块级元素需要两步设置：

1）设置块元素 width

2）设置 margin-left 和 margin-right 为 auto 。

#### 1.1.3 two or more block-level elements
示例共有代码 - html

	<main class="block-center">
	  <div>
	    I'm an element that is block-like with my siblings and we're centered in a row.
	  </div>
	  <div>
	    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
	  </div>
	  <div>
	    I'm an element that is block-like with my siblings and we're centered in a row.
	  </div>
	</main>

示例共有代码 - css

	body {
	  background: #f06d06;
	  font-size: 80%;
	}
	
	main {
	  background: white;
	  margin: 20px 0;
	  padding: 10px;
	}
	
	main div {
	  background: black;
	  color: white;
	  padding: 15px;
	  max-width: 125px;
	  margin: 5px;
	}

##### 方案1 display:inline-block
	.block-center {
		text-align: center;
	}
	.block-enter {
		display: inline-block;
		text-align: center /*文字左对齐美观*/
	}
##### 方案2 display:flex
	.block-center {
		display: flex;
		justify-content: center;
	}

### 1.2 垂直居中 Vertically
#### 1.2.1 inline or inline-* elements(text,link,etc)
##### 1） 单行文本
设置 padding-top 和 padding-bottom 值相等。

	.line {
	  padding-top: 30px;
	  padding-bottom: 30px;
	}

对于单行文本，设置 height 与 line-height 相等，也能使其居中。

	.center-text-trick {
	  height: 100px;
	  line-height: 100px;
	  white-space: nowrap;
	}

##### 2） multiple lines
1）设置 padding-top 和 padding-bottom 值相等。

2）table布局 或 display: table-cell

3) display: flex

	.flex-center-vertically {
		display: flex;
		flex-direction: column;
		justify-content: center;
	}

### 1.3 水平垂直居中

### 参考文档
[Centering in CSS: A Complete Guide](https://css-tricks.com/centering-css-complete-guide/)
---
-