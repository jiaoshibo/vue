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
利用 v-on 绑定一个事件监听器，使用户和应用之间进行互动
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

# 模板语法
> Vue 入门，Vue属性和指令 : https://segmentfault.com/a/1190000010917625
## 插值
### 文本
最常用的文本插值方法是使用"Mustache"语法
```html
<p>Message:{{msg}}</p>
```
Mustache 标签将会被替代为对应数据对象上 msg 属性的值。无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会更新。

通过使用 v-once 指令，可以执行一次性的插值，当数据改变时，插值处的内容便不会再更新

```html
<p v-once>这个数据将不会改变{{msg}}</p>
```
### 纯HTMl
{{}}会将数据解释为纯文本，为了能够输出HTML，可以使用 v-html 指令
```html
<div class="exp" v-html="rewHtml">{{msg}}</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            msg:"big world",
            rewHtml:"<p>hello world</p>"
        }
    })
</script>
```
属性 rewHtml 会将这个 div 内的内容替换，也就是说原来绑定的 msg 属性被忽略，rewHtml 将作为 HTML 被直接添加
```
hello world
```
### 特性
Mustache 语法不能用在设置 HTML 特性上，这时候就可以使用 v-bind 指令：
```html
<div class="exp" v-bind:title="tit">哈哈哈哈</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            tit:"2333"
        }
    })
</script>
```
布尔类特性可以这样设置，值为 false ，则该特性就会被删除
```html
<button class="exp" v-bind:disabled="tit">按钮</button>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            tit:true
        }
    })
</script>
```
如上，当我们把 tit的值改为 false 时，则 button 中的 disabled 会被删除，按钮也就可以点击了

### javascript 表达式

Vue 也提供了对js表达式的支持
```html
<div class="exp">{{ message.split('').reverse().join('')}}</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            message:"abcdefg"
        }
    })
</script>
```
```
gfedcba
```
这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含单个表达式，所以下面的例子都不会生效。
```html
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}
<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```
## 指令

指令（Directives）是带有 v- 前缀的特殊属性。指令属性的值预期是单个 JavaScript 表达式（v-for 是例外情况，稍后我们再讨论）。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。我上一篇文章中也提到过了
```html
<div id="app-3">
    <p v-if="seen">显示</p>
</div>
```
这里， v-if 指令将根据表达式 seen 的值的真假来插入/移除 <p> 元素。

### 参数

一些指令能够接收一个“参数”，在指令名称之后以冒号表示。例如，v-bind 指令可以用于响应式地更新 HTML 属性：
```html
<a v-bind:href="url"></a>
```
在这里 href 是参数，告知 v-bind 指令将该元素的 href 属性与表达式 url 的值绑定。

另一个例子是 v-on 指令，它用于监听 DOM 事件，也在我的上一篇文章中也提到了
```html
<input type="button" value="逆转消息" v-on:click="reverse">
```
### 修饰符
修饰符（Modifiers）是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：
```html
<form v-on:submit.prevent="onSubmit"></form>
```
## 过滤器
过滤器可以被用作一些常见的文本格式化，规定好过滤器的方法，便可以在模板里调用了。过滤器可以用在两个地方：mustache 插值和 v-bind 表达式。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符指示：
```html
<!-- in mustaches -->
{{ message | capitalize }}
<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>
```
```html
<!-- 依旧是反转这个字符串 -->
<div class="exp">{{ message|reverse}}</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            message:"abcdefg"
        },
        filters:{
            reverse:function(value){
                return value.split("").reverse().join("")
            }
        }
    })
</script>
```
过滤器也可以串联：
```html
<!-- 反转这个字符串，然后再把翻转后的字符串转换成数组 -->
<div class="exp">{{message|reverse|split}}</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            message:"abcdefg"
        },
        filters:{
            reverse:function(value){
                return value.split("").reverse().join("")
            },
            split:function(value1){
                return value1.split("")
            }
        }
    })
</script>
```
```
[ "g", "f", "e", "d", "c", "b", "a" ]
```
在这个例子中，reverse 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中，然后继续调用同样被定义为接收单个参数的过滤器函数 split，将 reverse 的结果传递到 split 中。

过滤器是 JavaScript 函数，因此可以接收参数：
```
{{ message | filterA('arg1', arg2) }}
```
这里，filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 取值后的值作为第三个参数。
## 缩写
Vue.js 为 v-bind 和 v-on 这两个最常用的指令，提供了特定简写：
### v-bind 缩写
```html
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```
### v-on 缩写
```html
<!-- 完整语法 -->
<a v-on:click="doSomething"></a>
<!-- 缩写 -->
<a @click="doSomething"></a>
```

# 计算属性

### gatter函数
```html
<!-- 使用了N次的字符串反转 -->
<div class="exp">
    <p>正向{{message}}</p>
    <p>反向{{reMessage}}</p>
</div>
<script>
    var exp = new Vue({
        el:".exp",
        data:{
            message:"abcdefg"
        },
        computed:{
            reMessage:function(){
                return this.message.split("").reverse().join("")
            }
        }
    })
</script>
```
结果如下
```
正向abcdefg

反向gfedcba
```
这里我们声明了一个计算属性 reMessage。我们提供的函数将用作属性 vm.reMessage 的 getter 函数：
```js
console.log(exp.reMessage) // --> "gfedcba"
exp.message="Hello"
console.log(exp.reMessage) // --> eybdooG
```
当我们在控制台修改exp.message的值时，exp.reMessage 的值也会更新
### 计算属性 vs method 方法
其实针对上面的例子，使用method方法也可以达到
```html
<p>反向{{reMessage}}</p>
<script>
    methods: {
        reMessage: function () {
            return this.message.split('').reverse().join('')
        }
    }
</script>
```
事实上，在计算结果上，method方法和compute方法是一致的，然而，不同的是计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。

而使用method方法时，每次重新渲染时，都要再次执行函数

### 计算属性 vs Watched 属性
```html
<div class="exp">
    {{fullName}}
    <input value="text" v-model="firstName">
    <input value="text" v-model="lastName">
</div>
<script>
var exp=new Vue({
    el:".exp",
    data:{
        firstName:"Pure",
        lastName:"View",
        fullName:"PureView"
    },
    watch:{
        firstName:function(val){
            this.fullName=val+this.lastName
        },
        lastName:function(val){
            this.fullName=this.firstName+val1
        }
    }
})
```
如上，我们要改变fullName的值，使用watch方法会有比较多的重复代码，如果要改变的数据很多，则需要写很多的watch方法。其实可以使用计算属性：
```js
var exp=new Vue({
    el:".exp",
    data:{
        firstName:"Pure",
        lastName:"View"
    },
    computed:{
        fullName:function(){
            return this.firstName+this.lastName
        }
    }
})
```
这样就舒服多了。
### 计算 setter
```html
<div class="exp">
    {{fullName}}
    <input value="text" v-model="firstName">
    <input value="text" v-model="lastName">
</div>
<script>
    var exp=new Vue({
        el:".exp",
        data:{
            firstName:"Pure",
            lastName:"View"
        },
        computed:{
            fullName:{
                get:function(){
                    return this.firstName+this.lastName
                },
                set:function(newValue){
                    var name=newValue.split("")
                    this.firstName=name[0]
                    this.lastName = name[name.length - 1]
                }
            }
        }
    })
</script>
```
在控制台修改 exp.fullName的值，那么 firstName 和 lastName 的值也会相应的更新
## Vue 方法
> 事件
```js
methods:{

}
```

> 过滤器
```js
filters:{

}
```
> 计算
```js
conputed:{

}
```
> 观察
```js
watch:{

}
```
> 钩子函数
```js
created function(){

}
mountde function(){

}
updated function(){

}
destoryed function(){
    
}
```
