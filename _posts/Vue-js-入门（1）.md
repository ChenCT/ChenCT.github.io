---
title: Vue.js--入门（1）
date: 2017-09-13 15:57:14
categories: Web前端 #分类
tags:
- Vue.js
description: #Vue.js入门笔记
---
## Vue简介
Vue.js是一套构建用户界面的渐进式框架，是当下很火的一个JavaScript MVVM库。
MVVM模式（Model-View-ViewModel）
ViewModel是Vue.js的核心，是一个Vue实例。
使用Vue的过程就是定义MVVM各个组成部分的过程。

将ViewModel中DOM Listeners和Data Bindings看作两个工具，它们是实现双向绑定的关键。
从View侧看，ViewModel中的DOM Listeners工具会帮我们监测页面上DOM元素的变化，如果有变化，则更改Model中的数据；
从Model侧看，当我们更新Model中的数据时，Data Bindings工具会帮我们更新页面中的DOM元素。

<!-- More -->
### 引入Vue
`<script src="https://unpkg.com/vue"></script>`

### Hello World示例
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>

    <body>
        <!--这是我们的View-->
        <div id="app">
            {{ message }}
        </div>
    </body>
    <script src="https://unpkg.com/vue"></script>
    <script>
        // 这是我们的Model
        var exampleData = {
            message: 'Hello World!'
        }

        // 创建一个 Vue 实例或 "ViewModel"
        // 它连接 View 与 Model
        new Vue({
            el: '#app',
            data: exampleData
        })
    </script>
</html>
```
主要分为3个过程
**1.定义View**
**2.定义Model**
**3.创建一个Vue实例或"ViewModel"，它用于连接View和Model**

在这个示例中，选项对象的el属性指向View，el: '#app'表示该Vue实例将挂载到&lt;div id="app"&gt;...&lt;/div&gt;这个元素；data属性指向Model，data: exampleData表示我们的Model是exampleData对象。
Vue.js有多种数据绑定的语法，最基础的形式是文本插值，使用一对大括号语法，在运行时{{ message }}会被数据对象的message属性替换，所以页面上会输出"Hello World!"。

### 双向绑定示例
MVVM模式本身是实现了双向绑定的，在Vue.js中可以使用v-model指令在表单元素上创建双向数据绑定。
```JavaScript
<!--这是我们的View-->
<div id="app">
    <p>{{ message }}</p>
    <input type="text" v-model="message"/>
</div>
```

---
## Vue.js常用指令
**Vue.js的指令是以v-开头的，它们作用于HTML元素，指令提供了一些特殊的特性，将指令绑定在元素上时，指令会为绑定的目标元素添加一些特殊的行为，我们可以将指令看作特殊的HTML特性（attribute）。**

### v-if指令
v-if是条件渲染指令，根据表达式的真假来删除和插入元素，语法：`v-if="expression"`
例：
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
        <div id="app">
            <h1>Hello, Vue.js!</h1>
            <h1 v-if="yes">Yes!</h1>
            <h1 v-if="no">No!</h1>
            <h1 v-if="age >= 25">Age: {{ age }}</h1>
            <h1 v-if="name.indexOf('jack') >= 0">Name: {{ name }}</h1>
        </div>
    </body>
    <script src="https://unpkg.com/vue"></script>
    <script>
        
        var vm = new Vue({
            el: '#app',
            data: {
                yes: true,
                no: false,
                age: 28,
                name: 'keepfool'
            }
        })
    </script>
</html>
```
页面上只渲染了3个&lt;h1&gt;元素，v-if值为false的&lt;h1;元素没有渲染到HTML

age是定义在选项对象的data属性中的，为什么Vue实例可以直接访问它呢？
这是因为**每个Vue实例都会代理其选项对象里的data属性**。

### v-show指令
v-show也是条件渲染指令，和v-if指令不同的是，**使用v-show指令的元素始终会被渲染到HTML，它只是简单地为元素设置CSS的style属性**。

### v-else指令
v-else元素必须立即跟在v-if或v-show元素的后面——否则它不能被识别。
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
        <div id="app">
            <h1 v-if="age >= 25">Age: {{ age }}</h1>
            <h1 v-else>Name: {{ name }}</h1>
            <h1>---------------------分割线---------------------</h1>
            <h1 v-show="name.indexOf('keep') >= 0">Name: {{ name }}</h1>
            <h1 v-else>Sex: {{ sex }}</h1>
        </div>
    </body>
    <script src="https://unpkg.com/vue"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                age: 28,
                name: 'keepfool',
                sex: 'Male'
            }
        })
    </script>
</html>
```
v-else元素是否渲染在HTML中，取决于前面使用的是v-if还是v-show指令。
这段代码中v-if为true，后面的v-else不会渲染到HTML；v-show为tue，但是后面的v-else仍然渲染到HTML了。

### v-for指令
v-for指令基于一个数组渲染一个列表，它和JavaScript的遍历语法相似：
`v-for="item in items"`

### v-bind指令
v-bind指令可以在其名称后面带一个参数，中间放一个冒号隔开，这个参数通常是HTML元素的特性（attribute），例如：v-bind:class
`v-bind:argument="expression"`
下面这段代码构建了一个简单的分页条，v-bind指令作用于元素的class特性上。
这个指令包含一个表达式，表达式的含义是：高亮当前页。
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <link rel="stylesheet" href="styles/demo.css" />
    </head>
    <body>
        <div id="app">
            <ul class="pagination">
                <li v-for="n in pageCount">
                    <a href="javascripit:void(0)" v-bind:class="activeNumber === n + 1 ? 'active' : ''">{{ n + 1 }}</a>
                </li>
            </ul>
        </div>
    </body>
    <script src="https://unpkg.com/vue></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                activeNumber: 1,
                pageCount: 10
            }
        })
    </script>
</html>
```

### v-on指令
v-on指令用于给监听DOM事件，它的用语法和v-bind是类似的，例如监听&lt;a&gt;元素的点击事件：
`<a v-on:click="doSomething">`
**有两种形式调用方法：绑定一个方法（让事件指向方法的引用），或者使用内联语句。**
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
        <div id="app">
            <p><input type="text" v-model="message"></p>
            <p>
                <!--click事件直接绑定一个方法-->
                <button v-on:click="greet">Greet</button>
            </p>
            <p>
                <!--click事件使用内联语句-->
                <button v-on:click="say('Hi')">Hi</button>
            </p>
        </div>
    </body>
    <script src="https://unpkg.com/vue"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                message: 'Hello, Vue.js!'
            },
            // 在 `methods` 对象中定义方法
            methods: {
                greet: function() {
                    // // 方法内 `this` 指向 vm
                    alert(this.message)
                },
                say: function(msg) {
                    alert(msg)
                }
            }
        })
    </script>
</html>
```

### v-bind和v-on的缩写
Vue.js为最常用的两个指令v-bind和v-on提供了缩写方式。**v-bind指令可以缩写为一个冒号，v-on指令可以缩写为@符号。**
```JavaScript
<!--完整语法-->
<a href="javascripit:void(0)" v-bind:class="activeNumber === n + 1 ? 'active' : ''">{{ n + 1 }}</a>
<!--缩写语法-->
<a href="javascripit:void(0)" :class="activeNumber=== n + 1 ? 'active' : ''">{{ n + 1 }}</a>

<!--完整语法-->
<button v-on:click="greet">Greet</button>
<!--缩写语法-->
<button @click="greet">Greet</button>
```
---
原文链接:[http://www.cnblogs.com/keepfool/p/5619070.html](http://www.cnblogs.com/keepfool/p/5619070.html)





