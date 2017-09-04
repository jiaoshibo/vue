# 组件
## 使用组件
### 注册组件
首先我们要创建一个实例：
```js
new Vue({
    el:'.exp',
    // ......
})
```
要注册一个全局组件，你可以使用 Vue.component(tagName, options)。例如：
```js
Vue.component('my-component', {
  // 选项
})
```
组件在注册之后，便可以在父实例的模块中以自定义元素 < my-component >< /my-component > 的形式使用。要确保在初始化根实例之前注册了组件：
```html
<div class="exp">
    <my-component></my-componetn>
</div>
<script>
    Vue.component('my-component',{
        template:'<div>这是一个组件</div>'
    })
    new Vue({
        el:'.exp',
    })
</script>
```
渲染结果：
```html
<div class="exp">
    <div>这是一个组件</div>
</div>
```
> 对于自定义标签名，Vue.js 不强制要求遵循 W3C 规则 (小写，并且包含一个短杠)，尽管遵循这个规则比较好。

我们知道，在创建的实例里面是有data数据的，其实在组件里也可以写数据：
```js
Vue.component('my-component'{
    templent:'<div><input type="button"></div>',
    data:function(){
        return{
            msg:'hello'
        }
    }
})
```
注意！组件里的data必须是一个函数，并将属性返回
### 局部注册
不必在全局注册每个组件。通过使用组件实例选项注册，可以使组件仅在另一个实例/组件的作用域中可用：
```html
<div class="exp">
    <my-component></my-component>
</div>
<script>
    var child={
        template:'<div>这是一个局部组件</div>'
    }
    new Vue({
        el:'.exp',
        components:{
            'my-component':child
        }
    })
</script>
```
渲染结果和上一个例子是一样的。

我们来看看全局注册和局部注册的区别：
```html
<div class="exp">
    <my-component></my-component>
</div>
<div class="exp1">
    <first></first>
</div>
<script>
    //全局注册
    Vue.component('my-component',{
        template:'<div>这是一个全局组件</div>'
    });
    new Vue({
        el:'.exp',
    });

    // 局部注册
    var child={
        template:'<div>这是一个局部组件</div>'
    };
    new Vue({
        el:'.exp1',
        components:{
            'first':child
        }
    });
</script>
```
局部注册的组件，只能在 .exp1 内使用，拿给 .exp 是不可以的，当然我们把全局注册的组件给 .exp1 ，是可以生效的。

### DOM 模板解析说明
当使用 DOM 作为模板时 (例如，将 el 选项挂载到一个已存在的元素上), 你会受到 HTML 的一些限制，因为 Vue 只有在浏览器解析和标准化 HTML 后才能获取模板内容。尤其像这些元素 < ul >，< ol >，< table >，< select > 限制了能被它包裹的元素，而一些像 < option > 这样的元素只能出现在某些其它元素内部。

比如：
```html
<div class="exp">
    <table>
        <my-component></my-component>
    </table>
    
</div>

<script>
    Vue.component('my-component',{
        template:'<tr><td>1</td><td>2</td></tr>'
    });
    new Vue({
        el:'.exp',
    });
</script>
```
如上，这样的写法在DOM里渲染时会反正错误：
```html
<tr>...</tr>
<table></table>
```
为了解决，就需要使用 is 属性
```html
<table>
    <tr is="my-component"></tr>
</table>
```
### 模板字符串

有时候我们可能要在组件里写很多的标签，如果统统像上面那样写的话，看起来很费劲，这时候就可以使用模板字符串：
> < script type="text/x-template" >< /script >

```html
<div class="exp">
    <table>
        <tr is="my-component"></tr>
    </table>
    
</div>
<!-- 模板字符串 -->
<script type="text/x-templent" id="tmp">
    <tr>
        <td>1</td>
        <td>3</td>
        <td>4</td>
        <td>3</td>
    </tr>
</script>
<script>
    Vue.component('my-component',{
        template:'#tmp'//调用模板字符串的 id 
    });
    new Vue({
        el:'.exp',
    });
</script>
```
这样写的话就比较直观了。
## Prop
### 使用 prop 传递数据
组件实例的作用域是孤立的。这意味着不能 (也不应该) 在子组件的模板内直接引用父组件的数据。要让子组件使用父组件的数据，我们需要通过子组件的 props 选项。

子组件要显式地用 props 选项声明它期待获得的数据：
```html
<div class="exp">
    <tk msg="hello"></tk>
</div>
<script type="text/x-templent" id="tmp">
    <div>
        <input type="button" v-on:click="alertMsg" value="弹框">
    </div>
</script>
<script>
    var tkTmp={
        template:"#tmp",
        //声明props
        props:['msg'],
        methods:{
            alertMsg:function(){
                alert(this.msg)
            }
        }
    };
    new Vue({
        el:'.exp',
        components:{
            'tk':tkTmp
        }
    });
</script>
```
在上面的例子中，我们要在子组件 tk 里传一个参数，就需要使用 prop 属性.

这种方式是静态绑定的方法，还有就是利用 v-bind 指令进行动态绑定：
```html
<tk v-bind:msg="msg"></tk>
```
### 驼峰式命名和短横线隔开式命名
HTML 特性是不区分大小写的。所以，当使用的不是字符串模板，camelCased (驼峰式) 命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名：
```js
ue.component('child', {
  // camelCase in JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```
```html
<!-- kebab-case in HTML -->
<child my-message="hello!"></child>
```
## 自定义事件
### 使用 v-on 绑定自定义事件
每个 Vue 实例都实现了事件接口 (Events interface)，即：
* $on(enentName) 监听事件
* $emit(eventName) 触发事件

我们可以使用 $emit 来用子组件触发父组件的事件：
```html
<div class="exp">
    <tk v-bind:msg="msg"></tk>
    <tk msg="HELLO WORDL" v-on:jieshou="jieshoufn"></tk>
</div>
<script type="text/x-templent" id="tmp">
    <div>
        <input type="button" v-on:click="alertMsg" value="弹框">
    </div>
</script>
<script>
    var tkTmp={
        template:"#tmp",
        props:['msg'],
        methods:{
            alertMsg:function(){
                alert(this.msg),
                this.$emit("jieshou","abc","def")
            }
        }
    };

    new Vue({
        el:'.exp',
        data:{
            msg:'hello'
        },
        components:{
            'tk':tkTmp
        },
        methods:{
            jieshoufn:function(){
                alert(2);
            }
        }
    });
</script>
```
当我们点击第二个按钮时，就会触发父组件内的 alert 事件。

其实我们也可以传递参数，由父组件接收：
```js
 this.$emit("jieshou","abc")
```
### 非父子组件通讯
有时候两个组件也需要通信 (非父子关系)。在简单的场景下，可以使用一个空的 Vue 实例作为中央事件总线：
```html
<div class="exp">
    <tk></tk>
    <pop></pop>
</div>
<script type="text/x-templent" id="tmp">
    <div>
        <input type="button" v-on:click="alertMsg" value="弹框">
    </div>
</script>
<script>
    var bus=new Vue();//空的 vue 实例

    var tkTmp={
        template:"#tmp",
        methods:{
            alertMsg:function(){
                bus.$emit("pass",1,2)//触发事件
            }
        }
    };
    var pop={
        template:'<div>2333</div>',
        mounted:function(){
            bus.$on("pass",function(arg1,arg2){//监听事件
                console.log(arg1)
                console.log(arg2)
            })
        }
    }
    new Vue({
        el:'.exp',
        components:{
            'tk':tkTmp,
            'pop':pop
        }
    });
</script>
```
## 使用 Slot 分发内容
这部分官方文档说的有点啰嗦，我这边就合到一块说吧。直接上个例子：
```html
    <style>
        .box{
            margin: 10px;
            width: 150px;
            border: 1px solid #ccc;
        }
        .box-header, .box-footer{
            height: 30px;
            background: sandybrown;
        }
        .box-body{
            min-height: 100px;
        }
    </style>
<body>
    <div class="exp">
        <box>
            <h2 slot="title">Slot内容分发<h2>
            <p>为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 内容分发 (或 “transclusion” 如果你熟悉 Angular)。Vue.js 实现了一个内容分发 API，参照了当前 Web 组件规范草案，使用特殊的 <slot> 元素作为原始内容的插槽。</p>
            <p slot="foot">分发完成</p>
        </box>
    </div>

    <script type="text/x-template" id="tmp">
        <div class="box">
            <div class="box-header"><slot name="title"></slot></div>
            <div class="box-body">
                <slot></slot>
            </div>
            <div class="box-footer"><slot name="foot"></slot>
            </div>
        </div>
    </script>
    <script>
        var box={
            template:"#tmp",
        }

        new Vue({
            el:'.exp',
            components:{
                "bilibili":box
            }
        })
    </script>
</body>
```
< slot >被成为插口，在组件中，我们需要使用 slot 插口预留一个位置，以方便分发内容。如上，在放置正文内容的地方，我们直接插入一对 slot 标签标记出正文内容的位置。对于页面标题和页脚等特殊的位置，我们就需要"具名Slot"，用一个特殊的属性 name 来配置如何分发内容。多个 slot 可以有不同的名字。具名 slot 将匹配内容片段中有对应 slot 特性的元素。