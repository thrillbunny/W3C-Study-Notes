# JS 表单 - submit 和 onsubmit

---

### Question（来自 StackOverFlow）

	<form action="/action_page.php" onsubmit="alert('The form was submitted');" >
		Enter name: <input type="text" name="fname">
		<input type="button" onclick="document.getElementsByTagName('form')[0].submit()" value="Submit">
	</form>

上一段表单代码中，点击了按钮后触发了 submit() 方法提交了表单，但不会触发 onsubmit() 事件，这是为什么？ submit() 和 onsubmit() 有什么区别？

### 表单提交

	<input type='submit'>
	<input type='button'>
	<button>

上述三种表单元素都会触发 submit() 方法提交表单。

**PS：button 和 input type="button" 的具体区别**

1. button 起始标签和闭合标签都是必须的， input 是空标签，禁用闭合标签

2. button 的值并不是写在 value 属性里，而是在起始、闭合标签之间；在 button 元素内部，可以放置文字、图像、移动、水平线、框架、分组框、音频视频等，唯一禁止使用的元素是图像映射（热点地图），因为它对鼠标和键盘敏感的动作会干扰表单按钮的行为

3. button 元素可以添加 CSS 样式，如设置宽、高、背景色等

4. （按键在表单中）如果在 HTML 表单中使用 button 元素，不同的浏览器会提交不同的值，Internet Explorer 将提交开始标签与闭合标签之间的文本，而其他浏览器将提交 value 属性的内容，而 input 将始终提交 value 值；

5. （按键在表单中）button 按钮在 form 表单的时候会当成 submit 提交，类似 input type="submit"。

综合 4 和 5 两点，**在 HTML 表单中不要使用 button！！！**

### HTMLFormElement.submit()

以下摘自 W3CSchool

> HTMLFormElement.submit()用来提交表单，这个方法和提交表单按钮很类似。
> 
> 在基于Gecko的浏览器中，调用该方法后，表单的 onsubmit 事件句柄(比如,onsubmit="return false;")不会执行。
>
> 如果一个表单控件(比如一个submit类型的按钮)的name或id值为"submit",则它将覆盖表单的submit方法。

实例：

	document.forms["myform"].submit()

这也就解答了 Question 中为什么无法触发 onsubmit 事件，因为点击 button 按键会触发

	onclick="document.getElementsByTagName('form')[0].submit()"

### onsubmit()与submit()区别

1. submit() 是一个方法，该方法用于触发 form 表单提交；onsubmit()是一个 js 事件句柄，监听表单提交。发生顺序 onsubmit -> submit()。

2. HTMLFormElement.submit() 对 onsubmit()和submit() 的影响

		<script>
			function fun() {
				alert("form_submit");
			}
		</script>
		<form onsubmit="fun()">
			<!--能弹出form_submit-->
			<input type="submit" value="SUBMIT" id="Button1">

			<!--表单会提交，但是不会执行fun(),直接用脚本document.formName.submit()提交表单是不会触发表单的onsubmit()事件的-->
			<input type="button" value="onClick_submit" id="Button2" onClick="document.forms[0].submit()">

			<!--能弹出form_submit-->
			<input type="button" value="onClick_onSubmit" id="Button3" onClick="document.forms[0].onsubmit()">
		</form>

### 表单 onclick、onsubmit、submit 执行顺序

	<form action="#" method="POST" name="A" onsubmit="return X();">
		<input type="text" value="" />
		<input onclick="Y()" type="submit" value="提交" />
	</form>

执行顺序 onclick: Y() -> onsubmit: X() -> submit()

也就是说，只要 onclick 未 return false，那么就继续执行 onsubmit；只要 onsubmit 未 return false，那么表单就被会被提交。

注意一定要 **return X();** 才能取得函数的返回值，否则只是单纯地调用函数，返回值未被传递。

阻止表单提交的正确方式：

	<script>  
		function submitFun(){   
		    return true; //允许表单提交    
		    return false;//不允许表单提交  
		}  
	</script>  
		<form onsubmit="return submitFun();"> //注意此处不能写成 onsubmit="submitFun();"否则将表单总是提交  
	</form>  










