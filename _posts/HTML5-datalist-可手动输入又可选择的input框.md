---
title: HTML5 datalist--可手动输入又可选择的input框
date: 2017-08-19 17:19:27
categories: Web前端 #分类
tags:
- html5
- datalist
- 自动补全
description: #可手动输入又可选择并可以自动补全的输入框
---

恩 领导让做一个带有模糊搜索的输入框 准确来说 UI让做的 
尬聊一句
开始开始

如何用&lt;datalist&gt;标签实现可手动输入又可选择并可以自动补全的输入框（厉害了我的输入框！）

<!--more-->

---
话不多说 直接上代码
**datalist简单实例**
```JavaScript
<input type="text" list="fruit" placeholder="一个厉害的输入框"/>
<datalist id="fruit">
    <option value="Apple">
    <option value="Peach">
    <option value="Pineapple">
    <option value="Mango吃了会过敏">
</datalist>
```
看这里看这里>> **input的list要与datalist的id一致哈~ **
严肃一点说就是，**请使用 input 元素的 list 属性来绑定 datalist**

---
再来一个邮箱自动补全
**datalist实现邮箱自动补全**
```JavaScript
<!-- html -->
<label>邮箱 : </label>
<input id="emailInput" type="text" list="e-mail" placeholder="请输入邮箱" oninput="completeEmail()"/>
<datalist id="e-mail"></datalist>

<!-- javascript -->
function completeEmail(){
	var info=$("#emailInput").val();
	$("#e-mail").empty();
	if(info.indexOf("@")>-1){
		return false;  
	}
	else{
		$("#e-mail").append("<option value='"+info+"@qq.com'>"
			               +"<option value='"+info+"@163.com'>"
			               +"<option value='"+info+"@126.com'>"
			               +"<option value='"+info+"@gmail.com'>"
			               +"<option value='"+info+"@yahoo.com'>")
	}
}
```
[邮箱自动补全](/img/8.19.3.png'邮箱自动补全')

好了 菜鸡的讲解到此结束


