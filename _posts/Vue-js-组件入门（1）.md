---
title: Vue.js--组件入门（1）
date: 2017-09-14 16:52:59
categories: Web前端 #分类
tags:
- Vue.js
description: #Vue.js入门笔记
---
## Vue组件简介
组件系统是Vue.js其中一个重要的概念，它提供了一种抽象，让我们可以使用独立可复用的小组件来构建大型应用，任意类型的应用界面都可以抽象为一个组件树
**组件可以扩展HTML元素，封装可重用的HTML代码，我们可以将组件看作自定义的HTML元素。**

<!-- more -->
## 组件的创建和注册
1. 调用Vue.extend()方法创建组件构造器
2. 调用Vue.component()方法注册组件
3. 在Vue实例的作用范围内使用组件

### 理解
1. Vue.extend()是Vue构造器的扩展，调用Vue.extend()创建的是一个组件构造器。 
2. Vue.extend()构造器有一个选项对象，选项对象的template属性用于定义组件要渲染的HTML。 
3. 使用Vue.component()注册组件时，需要提供2个参数，第1个参数时组件的标签，第2个参数是组件构造器。 
4. 组件应该挂载到某个Vue实例下，否则它不会生效。

```JavaScript
<!DOCTYPE html>
<html>
    <body>
        <div id="app1">
            <my-component></my-component>
        </div>
        
        <div id="app2">
            <my-component></my-component>
        </div>
        
        <!--该组件不会被渲染-->
        <my-component></my-component>
    </body>
    <script src="js/vue.js"></script>
    <script>
        var myComponent = Vue.extend({
            template: '<div>This is a component!</div>'
        })
        
        Vue.component('my-component', myComponent)
        
        var app1 = new Vue({
            el: '#app1'
        });
        
        var app2 = new Vue({
            el: '#app2'
        })
    </script>
</html>
```

### 全局注册和局部注册
**调用Vue.component()注册组件时，组件的注册是全局的，这这意味着该组件可以在任意Vue示例下使用。**
如果不需要全局注册，或者是让组件使用在其它组件内，可以用**选项对象的components属性实现局部注册。**

```JavaScript
<!DOCTYPE html>
<html>
    <body>
        <div id="app">
            <!-- 3. my-component只能在#app下使用-->
            <my-component></my-component>
        </div>
    </body>
    <script src="js/vue.js"></script>
    <script>
        // 1.创建一个组件构造器
        var myComponent = Vue.extend({
            template: '<div>This is my first component!</div>'
        })
        
        new Vue({
            el: '#app',
            components: {
                // 2. 将myComponent组件注册到Vue实例下
                'my-component' : myComponent
            }
        });
    </script>
</html>
```

### 父组件和子组件
在组件中定义并使用其他组件，这就构成了父子组件的关系
```JavaScript
<!DOCTYPE html>
<html>
    <body>
        <div id="app">
            <parent-component>
            </parent-component>
        </div>
    </body>
    <script src="js/vue.js"></script>
    <script>
        
        var Child = Vue.extend({
            template: '<p>This is a child component!</p>'
        })
        
        var Parent = Vue.extend({
            // 在Parent组件内使用<child-component>标签
            template :'<p>This is a Parent component</p><child-component></child-component>',
            components: {
                // 局部注册Child组件，该组件只能在Parent组件内使用
                'child-component': Child
            }
        })
        
        // 全局注册Parent组件
        Vue.component('parent-component', Parent)
        
        new Vue({
            el: '#app'
        })
        
    </script>
</html>
```

理解：
1. var Child = Vue.extend(...)定义一了个Child组件构造器
2. var Parent = Vue.extend(...)定义一个Parent组件构造器
3. components: { 'child-component': Child }，将Child组件注册到Parent组件，并将Child组件的标签设置为child-component。
4. template :'&lt;p&gt;This is a Parent component&lt;/p&gt;&lt;child-component&gt;&lt;/child-component&gt;'，在Parent组件内以标签的形式使用Child组件。
5. Vue.component('parent-component', Parent) 全局注册Parent组件
6. 在页面中使用&lt;parent-component&gt;标签渲染Parent组件的内容，同时Child组件的内容也被渲染出来

Child组件是在Parent组件中注册的，它只能在Parent组件中使用，确切地说：**子组件只能在父组件的template中使用。**

请注意下面两种子组件的使用方式是错误的：
**1. 以子标签的形式在父组件中使用**
**2. 在父组件标签外使用子组件**

### 组件注册语法糖
以上组件注册的方式有些繁琐，Vue.js为了简化这个过程，提供了注册语法糖。
**使用Vue.component()直接创建和注册组件：**
```JavaScript
// 全局注册，my-component1是标签名称
Vue.component('my-component1',{
    template: '<div>This is the first component!</div>'
})

var vm1 = new Vue({
    el: '#app1'
})
```

`Vue.component()`的第1个参数是标签名称，第2个参数是一个选项对象，使用选项对象的template属性定义组件模板。
使用这种方式，Vue在背后会自动地调用`Vue.extend()`。


**在选项对象的components属性中实现局部注册：**
```JavaScript
var vm2 = new Vue({
	el: '#app2',
	components:{
		//局部注册，my-component2是标签名称
		'my-component2': {
			template:'<div>This is the second component!</div>'
		},
		'my-component3': {
			template:'<div>This is the third component!</div>'
		}
	}
})
```

### 使用script或template标签
尽管语法糖简化了组件注册，但在template选项中拼接HTML元素比较麻烦，这也导致了HTML和JavaScript的高耦合性。
庆幸的是，Vue.js提供了两种方式将定义在JavaScript中的HTML模板分离出来。

**使用&lt;script&gt;标签**
```JavaScript
<!DOCTYPE html>
<html>
    <body>
        <div id="app">
            <my-component></my-component>
        </div>
        
        <script type="text/x-template" id="myComponent">
            <div>This is a component!</div>
        </script>
    </body>
    <script src="js/vue.js"></script>
    <script>
        
        Vue.component('my-component',{
            template: '#myComponent'
        })
        
        new Vue({
            el: '#app'
        })
        
    </script>
</html>
```

template选项现在不再是HTML元素，而是一个id，Vue.js根据这个id查找对应的元素，然后将这个元素内的HTML作为模板进行编译。
**使用&lt;script&gt;标签时，type指定为text/x-template，意在告诉浏览器这不是一段js脚本，浏览器在解析HTML文档时会忽略&lt;script&gt;标签内定义的内容。**

**使用&lt;template&gt;标签**
如果使用`<template>`标签，则不需要指定type属性。
```JavaScript
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
        <div id="app">
            <my-component></my-component>
        </div>
        
        <template id="myComponent">
            <div>This is a component!</div>
        </template>
    </body>
    <script src="js/vue.js"></script>
    <script>
        
        Vue.component('my-component',{
            template: '#myComponent'
        })
        
        new Vue({
            el: '#app'
        })
        
    </script>
</html>
```

### 组件的el和data选项
传入Vue构造器的多数选项也可以用在 `Vue.extend()` 或`Vue.component()`中，不过有两个特例：`data`和`el`。
Vue.js规定：在定义组件的选项时，`data`和`el`选项必须使用函数。

另外，如果data选项指向某个对象，这意味着所有的组件实例共用一个data。
我们应当使用一个函数作为 data 选项，让这个函数返回一个新对象：
```JavaScript
Vue.component('my-component', {
    data: function(){
        return {a : 1}
    }
})
```

## 使用props
**组件实例的作用域是孤立的。**这意味着不能并且不应该在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。

### props基础示例
```JavaScript
var vm = new Vue({
    el: '#app',
    data: {
        name: 'keepfool',
        age: 28
    },
    components: {
        'my-component': {
            template: '#myComponent',
            props: ['myName', 'myAge']
        }
    }
})
```
为了便于理解，你可以将这个Vue实例看作my-component的父组件。
如果我们想使父组件的数据，则必须先在子组件中定义props属性，也就是`props: ['myName', 'myAge']`这行代码。

定子组件的HTML模板
```JavaScript
<template id="myComponent">
    <table>
        <tr>
            <th colspan="2">
                子组件数据
            </th>
        </tr>
        <tr>
            <td>my name</td>
            <td>{{ myName }}</td>
        </tr>
        <tr>
            <td>my age</td>
            <td>{{ myAge }}</td>
        </tr>
    </table>
</template>
```

将父组件数据通过已定义好的props属性传递给子组件：
```JavaScript
<div id="app">
    <my-component v-bind:my-name="name" v-bind:my-age="age"></my-component>
</div>
```

**注意：在子组件中定义prop时，使用了camelCase命名法。由于HTML特性不区分大小写，camelCase的prop用于特性时，需要转为 kebab-case（短横线隔开）。例如，在prop中定义的myName，在用作特性时需要转换为my-name。**

在父组件中使用子组件时，通过以下语法将数据传递给子组件：
`<child-component v-bind:子组件prop="父组件数据属性"></child-component>`

## prop的绑定类型
### 单向绑定
既然父组件将数据传递给了子组件，那么如果子组件修改了数据，对父组件是否会有所影响呢？
我们将子组件模板和页面HTML稍作更改：
```JavaScript
<div id="app">

    <table>
        <tr>
            <th colspan="3">父组件数据</td>
        </tr>
        <tr>
            <td>name</td>
            <td>{{ name }}</td>
            <td><input type="text" v-model="name" /></td>
        </tr>
        <tr>
            <td>age</td>
            <td>{{ age }}</td>
            <td><input type="text" v-model="age" /></td>
        </tr>
    </table>

    <my-component v-bind:my-name="name" v-bind:my-age="age"></my-component>
</div>

<template id="myComponent">
    <table>
        <tr>
            <th colspan="3">子组件数据</td>
        </tr>
        <tr>
            <td>my name</td>
            <td>{{ myName }}</td>
            <td><input type="text" v-model="myName" /></td>
        </tr>
        <tr>
            <td>my age</td>
            <td>{{ myAge }}</td>
            <td><input type="text" v-model="myAge" /></td>
        </tr>
    </table>
</template>
```

**prop默认是单向绑定：当父组件的属性变化时，将传导给子组件，但是反过来不会。这是为了防止子组件无意修改了父组件的状态**

---
### 双向绑定
可以使用`.sync`显式地指定双向绑定，这使得子组件的数据修改会回传给父组件。
`<my-component v-bind:my-name.sync="name" v-bind:my-age.sync="age"></my-component>`

**注：`sync`在2.0中被移除，在2.3.0起又重新引入，但这次只是作为一个编译时 
的语法糖存在，会被扩展为一个自动更新父组件属性的`v-on`侦听器**
如下代码
`<comp :foo.sync="bar"></comp>`
会被扩展为：
`<comp :foo="bar" @update:foo="val => bar = val"></comp>`
当子组件需要更新foo的值时，它需要显式地触发一个更新事件：
`this.$emit('update:foo',newValue)`

### 单次绑定(弃用)
`<my-component v-bind:my-name.once="name" v-bind:my-age.once="age"></my-component>`


---
### prop验证
`props:{
	data:Array,
	columns:Array,
	filterKey:String
}`
我们可以为组件的 props 指定验证规格。如果传入的数据不符合规格，Vue 会发出警告。


原文链接:[http://www.cnblogs.com/keepfool/p/5625583.html](http://www.cnblogs.com/keepfool/p/5625583.html)
