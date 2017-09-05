# 可复用性和组合
## 混合
混合 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混合对象可以包含任意组件选项。以组件使用混合对象时，所有混合对象的选项将被混入该组件本身的选项。

![image](gif.gif)
```html
<div id="app">
    <popup></popup>
    <more></more>
</div>
<script type="text/x-template" id="popup-tmp">
    <div>
        <input type="button" value="弹出" v-on:click="togg">
        <div class="popup" v-show="viv">
            2333
            <input type="button" v-on:click="hide" value="shutDown">
        </div>
    </div>
</script>

<script type="text/x-template" id="more-tmp">
    <div>
        <a @mouseover="show" @mouseout="hide">更多</a>
        <ul v-show="viv">
            <li>从前有座山</li>
            <li>山上有座庙</li>
            <li>老和尚</li>
            <li>wot</li>
        </ul>
    </div>
    
</script>
<script>
    var base={//定义一个混合对象
        data:function(){
            return{
                viv:false
            }
        },
        methods:{
            togg:function(){
                this.viv=!this.viv
            },
            hide:function(){
                this.viv=false
            },
            show:function(){
                this.viv=true
            }
        }
    }
    var Popup={
        template:'#popup-tmp',
        mixins:[base],//使用混合对象
        data:function(){
            return {viv:true}
        }
    }

    var Menu={
        template:"#more-tmp",
        mixins:[base]//使用混合对象
    }
    new Vue({
        el:'#app',
        components:{
            "popup":Popup,
            "more":Menu
        }
    })
</script>
```
在上面的例子中，我们创建了一个点击弹出框和一个滑过弹出框，因为其调用的数据和方法都是重复的，因此就定义一个混合对象，让两个不同的弹出框分别调用。

官方例子：
```js
// 定义一个混合对象
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}
// 定义一个使用混合对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
})
var component = new Component() // => "hello from mixin!"
```

# 自定义指令
Vue允许自定义指令。

举个例子，现在这里有个input输入框，当页面加载时，输入框就已经获取焦点，并将输入框固定在视口的某一位置上：
```js
Vue.directive('focus',{
    inserted:function(el){
        el.style.position="fixed";
        el.style.left="10px";
        el.style.top="10px";
        el.focus()
    }
})
```
这样在模板中就可以使用了
```html
<input type="text" v-focus>
```
# 插件
## 插件的开发
Vue.js 的插件应当有一个公开方法 install 。这个方法的第一个参数是 Vue 构造器 , 第二个参数是一个可选的选项对象:
```js
var Myplugin={};
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```
## 使用插件
```js
Vue.use(MyPlugin)
```