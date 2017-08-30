# 初步认识vue
vue是一套构建用户界面的渐进式框架，是mvvm框架的一种。vue采用了自底向上增量开发的设计，其核心库只关注视图层，它不仅易于上手，还便于与第三方库或既有项目整合。
> vue有三个部分组成 : 视图-数据-视图模型。视图即HTML部分。
> vue的引入：
```html
<script src="https://unpkg.com/vue"></script>
//或者直接引入文件
<script src="js/vue.js"></script>
```
## 声明式渲染
vue的核心是一个允许采用简洁的模板语法来声明式的将数据渲染进 DOM：
```html
<div id="app">
    {{message}}
</div>

<script>
    var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue!'
    }
    })
</script>
```
就这样，数据和DOM进行了绑定，视图将数据引入，并进行渲染，显示出来，而且元素是响应式的，打开控制台，修改 app.message 的值，就会发现视图的文本也会相应的更新。

除了文本插值，我们还可以使用指令的方式绑定DOM元素属性。
```html
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
<script>
    var app2 = new Vue({
    el: '#app-2',
    data: {
        message: '页面加载于 ' + new Date().toLocaleString()
    }
    })
</script>
```
如上，v-bind 属性被称为指令，其前缀 v- 表示其为vue提供的特殊属性。这里该指令的作用是：“将这个元素节点的 title 属性和 Vue 实例的 message 属性保持一致”。

## 条件与循环

控制一个元素的显示与隐藏
```html
<div id="app-3">
    <p v-if="seen">显示</p>
</div>
<script>
    var app3=new Vue({
        el:"#app-3",
        data:{
            seen:true
        }
    })
</script>
```
如果在控制台输入 app3.seen=false ，你就发现上例的文本隐藏了。

利用 v-for 指令绑定数组的数据来渲染一个项目列表
```html
<div id="app-4">
    <ol>
        <li v-for="todo in todos">
            {{todo.text}}
        </li>
    </ol>
</div>
<script>
    var app4=new Vue({
        el:"#app-4",
        data:{
            todos:[
                {text:"HTML"},
                {text:"JAVASCRIPT"},
                {text:"VUE.JS"},
            ]
        }
    })
</script>
```
```
1.HTML
2.JAVASCRIPT
3.VUE.JS
```
在控制台输入 app3.todos.push({text:"CSS"}) ，列表中会增加一个新项

# 处理用户输入
利用 v-on 绑定一个时间监听器，使用户和应用之间进行互动
```html
<div id="app-5">
    <p>{{message}}</p>
    <input type="button" value="逆转消息" v-on:click="reverse">
</div>
<script>
    var app5=new Vue({
        el:"#app-5",
        data({
            message:"Hello world!"
        }),
        methods:{
            reverse:function(){
                this.message=this.message.split("").reverse().join("")
            }
        }
    })
</script>
```
通过 v-model 指令，可以轻松实现表单输入和应用状态之间的双向绑定
```html
<div id="app-6">
    <p>{{message}}</p>
    <input type="text" v-model="message">
</div>
<script>
    var app6=new Vue({
        el:"app-6",
        data:{
            message:"hello"
        }
    })
</script>
```
## 组件化应用构建
使用 v-bind 指令将todo传到每一个重复的组件中
```html
<div id="app-7">
    <ol>
        <todo-item v-for="item in groceryLIst" v-bind:todo="item" v-bind:key="item.id"></todo-item>
    </ol>
</div>
<script>
    Vue.component("todo-item",{
        props:["todo"],
        temlpate:"<li>{{todo.text}}</li>"
    })
    var app7=new Vue({
        el:"#app-7",
        data:{
            groceryLIst:[
                {id:0,text:"JAVASCRIPT"},
                {id:1,text:"HTML"},
                {id:2,text:"VUE"}
            ]
        }
    })
</script>
```
```
1.JAVASCRIPT
2.HTML
3.VUE
```
在上面的例子中，我们已经设法将应用分割成了两个更小的单元，子单元通过 props 接口实现了与父单元很好的解耦。
# To be continued......