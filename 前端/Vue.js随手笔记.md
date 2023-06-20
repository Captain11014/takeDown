# 前端随手笔记

### vue.js

#### 组件的使用

局部注册组件：在需要使用组件的文件里引用

```vue
<script>
//引入组件
import test from "@/views/system/sysRole/test.vue";

export default {
    //注册组件
  components:{
    test
  },
}
</script>
```

全局注册组件

vue工程下：在main.js文件注册全局

```js
// 分页组件
import test from "@/views/system/sysRole/test.vue";
// 全局组件挂载
Vue.component('test', test);
```

使用：

```vue
<test></test>
```

这些组件是**全局注册的**。也就是说它们在注册之后可以用在任何新创建的 Vue 根实例 (`new Vue`) 的模板中。比如：

```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

#### 组件之间传递参数：父组件向子组件传递

##### props

所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解。

额外的，每次父级组件发生变更时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你**不**应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。

**这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。**在这种情况下，最好定义一个本地的 data property 并将这个 prop 用作其初始值：

```vue
<test :msg="'芜湖'"></test>
```

```vue
<!--test组件-->
<template>
  <div>
        <h1 @click="ss()">{{message}}</h1>
  </div>
</template>

<script>
export default {
    name:'test',
    //props传递的值不允许更改
    props:{
        msg:String,
    },

    data(){
        return{
            message:this.msg
        }
    },
    methods:{
        ss(){
            this.message = "ddddd"
        }
    }
}
```

**这个 prop 以一种原始的值传入且需要进行转换。**在这种情况下，最好使用这个 prop 的值来定义一个计算属性：

```vue
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

##### 子组件向父组件传递参数

不同于组件和 prop，事件名不存在任何自动化的大小写转换。而是触发的事件名需要完全匹配监听这个事件所用的名称。

this.$emit（）；

```js
 methods:{
        ss(){
            this.$emit('myEvent',params);
        }
   }
```

使用：

```js
<test :msg="'芜湖'" @myEvent="testClick"></test>
```

```js
export default {

  components:{
    test
  },


  methods: {
    testClick(msg1){
        alert("子组件想父组件传递值"+msg1);
    },
  }
};
```

#### vue.js关于富文本编辑器quill设置图片放大缩小

引入依赖添加相关属性之后报错：Cannot read property 'imports' of undefined

在vue.config.js中插入如下配置

```
const webpack = require('webpack')
module.exports中插入如下代码
configureWebpack: {
plugins: [
    new webpack.ProvidePlugin({
        'window.Quill': 'quill/dist/quill.js',
        'Quill': 'quill/dist/quill.js'
    })
]

```

#### 路由传递参数

第一种：使用`path`来匹配路由，然后通过`query`来传递参数，这种情况下`query`传递的参数会显示在`url`后面会跟`?id=`

```js
this.$router.push({
    path: '/user/profile',
    query: {
      id: id
    }
})
```

获取参数方式：`this.$route.query.id`

第二种：使用`name`来匹配路由，使用`params`传参，可以在路由的`path`里加参数。

```js
this.$router.push({
    name: 'UserProfile',
    params: {
      id: id
    }
})
```

获取参数方式：`this.$route.params.id`

第三种：直接让路由携带参数跳转

```js
this.$router.push({
  path: '/user/profile/:id(\\d+)'
})
```

获取参数方式：`this.$route.params.id`

#### vue项目中添加背景图片报错

Module not found: Error: Can't resolve './@/assets/images/login.png'

```css
 报错代码
 background-image: url("@/assets/images/login-background.jpg");
 修改为
 background-image: url("~@/assets/images/login-background.jpg");
```

### CSS

#### 使用 `calc()`函数

给一个盒子设置宽高，长度是100%加减一个像素值，一般height:100% - 100px;这样写是不会生效的。
calc()：这个函数就是用来计算长度值的，可以加减乘除一个像素值

```css
height: calc(100% - 100px);
```

