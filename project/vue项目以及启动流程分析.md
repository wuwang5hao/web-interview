# 常见vue工程目录结构
```js
|--config/           #Vue基本配置文件，可以设置监听端口，打包输出等
|--node_modules/     # 依赖包
|--public
   |--favicon.ico    #标签图标
   |--index.html     #当前项目的唯一页面
|--src
   |--assets         #静态资源img,css,js
   |--api            #
   |--components     #小组件
   |--store 
      |--index.js    #根状态管理集
      |--modules     #模块化状态管理
      |--actions     #进行异步操作，提交mutation方法
      |--getters     #加工state中的成员变量并传至外界
      |--mutation-types.js #项目存放mutations方法常量的文件，按需加入
      |--mutations   #操作state数据的方法集合，比如对数据的增删改等等
      |--index.js
   |--views
      |--xxx.vue
   |--router
      |--index.js    #路由脚本文件，配置路由url与页面组建的映射关系
   |--App.vue        #根组件
   |--main.js        #入口文件
   |--package.json   #项目的配置信息，启动方式等
   ```


   # vue运行流程
   ```js
   index.html-->main.js-->App.vue-->router/index.js
   ```

   ## index.html 项目入口
   作用：内容简单，主要提供一个div给vue挂载。是项目入口

   ## main.js 入口文件
   作用：主要是引入vue、App、router模块，创建一个vue对象，并把App.vue模版的内容挂载到index.html的id为app的div标签下，并绑定一个路由配置。
- 主模板文件配置方式1

```js
import Vue from 'vue'    /*引入vue框架*/
import App from './App'     /*引入根组件*/
import router from './router'    /*引入路由设置*/
import store from './store'    /*引入根状态管理集文件*/
Vue.config.productionTip = false    /*关闭生产模式下给出的提示*/

//定义根组件，用render读取一个.vue文件，$mount渲染替换index.html中的占位
new Vue({
el:'#app', //此处的el对应的是index.html中的div标签的id
router, 
store,
render:h=>h(App),  //render函数以js的方式(虚拟节点)去渲染组件  //App是组件,是项目的主组件
}).$mount('#app');
```
```js
render: h => h(App)
//es6语法箭头函数的简写形式
//h又是createElement的另一种写法,这是vue规定的,没有为什么

//相当于
render: (h) => {
    return h(App)
}

//相当于
//箭头函数转为普通函数
render: function(h) {
    return h(App)
}

//相当于
render: function(createElement) {
    return createElement(App)
}



```

- 主模板文件配置方式2
```js
import Vue from 'vue';

import Clipboard from 'v-clipboard';
import BaseTable from "@/components/table/BaseTable";

import store from './store/store'
import App from './App';
import router from './router';

// 使用 vuex、vue-router、Clipboard 等插件时，需要通过Vue.use('xxx')方法才起作用
Vue.use(Clipboard);
// 注册一个全局组件，就可以在全局其他组件中直接对这个组件进行使用
Vue.component('BaseTable', BaseTable)

/* eslint-disable no-new */
new Vue({
  el: '#app', //此处的el对应的是index.html中的div标签的id
  router,
  store,
  components: { App },
  template: '<App/>',  // 此处template应该是被内部拿去使用了,没有看到显示的app标签
});

```

## App.vue 根组件
- 在Vue中经常要建立后缀名为.vue的文件，.vue文件通常由`<template></template>,<script></script>和<style></style>`三部分组成。
- 其中`<template></template>`通常建立我们要用的网页界面，`<script></script>`则是跟数据打交道，面向逻辑，而`<style></style>`主要是负责`<template></template>`标签中的样式。
- 上面三者的结合，就构成了我们所见到的网页，一般我们在开发新页面的时需要创建一个新的.vue文件，在文件中完成三个环节的编写。
- 上面main.js将App.vue模版的内容放置在index.html的div标签内，如下，App.vue中有一个待放置内容的<router-view/>，<router-view/>的内容将由router配置决定。
```js
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: "app"
};
</script>
<style>
.add-container {
  margin-bottom: 20px;
}
</style>

```

书写vue文件注意：
- 一个.vue文件就是一个组件
- html结构：template(模版)中只能包含一个父节点，即顶层的div只能有一个（例如上图，父节点为#app的div,没有其他兄弟节点）。<router-view></router-view>是子路由视图，后面的路由页面都显示在此处。
- js逻辑：对于script标签，vue通常用es6来写，然后用export default导出，内部可以包含数据data、生命周期(mounted等)，方法(methods)等。
- css样式：样式是通过style标签<style></style>包裹，默认作用域是全局的，如需定义作用域在该组件下起作用，需在标签上加上scoped(<style scoped></style>)。


## router.js 路由配置
在router文件夹下，有一个index.js文件，即为路由配置文件。
文件中配置了一个路由，在访问路径/的时候，会把Home模版的内容放置到上面的router-view中。 

```js
import Vue from 'vue';
import Router from 'vue-router';
import Home from 'views/home'
import About from 'views/about'
Vue.use(Router);
export default new Router({
    // vue-router的路由模式可以通过指定mode属性值控制，可选值："hash" 、"history"、 "abstract" ， 默认："hash" (浏览器环境) ， "abstract" (Node.js 环境)
    // mode: 'history' 
    // 以下是hash模式
    base:'/'
    routes:[
        {
            path:'/',
            name:'home',
            component:Home
        },
        {
            path:'/about'
            name:'about',
            component:About
        }
    ]
});

// 用户在浏览器中访问的路由是/，
// router插件就会加载home.vue文件，同理/about就是加载about.vue文件。
// 用加载的home.vue文件或者about.vue文件去替换App.vue文件中的<router-view/>占位符。

```



