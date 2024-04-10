## 配置

安装： `npm i vue-router@3 --save`

配置路由器，注意是`routes`：

```javascript
import VueRouter from "vue-router";
import About from '../components/About'
import Home from '../components/Home'

export default new VueRouter({
    routes: [
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home
        }
    ]
})
```

`main.js`中配置`router`：

```javascript
import Vue from "vue"
import App from './App.vue'
// 引入VueRouter
import VueRouter from "vue-router"
// 引入router
import router from './router'

Vue.use(VueRouter)
Vue.config.productionTip = false

new Vue({
    el: "#app",
    render: h => h(App),
    router
})
```

通过`router-link`标签实现路由切换，`active-class`是激活的样式，`to`是前往的地址：

```html
<router-link active-class="xx" to="/about">About</router-link>
```

通过`router-view`标签展示去往的路由目的内容：

```html
<router-view></router-view>
```

## 嵌套路由（多级路由）

嵌套路由即多级路由。路由配置时增加`children`属性:

```javascript
export default new VueRouter({
    routes: [
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home,
            children: [
                {
                    path: 'games',
                    component: Games
                },
                {
                    path: 'foods',
                    component: Foods
                }
            ]
        }
    ]
})
```

使用上与一级路由没啥区别：

```html
<template>
    <div>
        <h2>Home的内容</h2>
        <ul class="nav nav-tabs">
            <li>
                <router-link class="list-group-item"  active-class="active" to="/home/games">Games</router-link>
            </li>
            <li>
                <router-link class="list-group-item"  active-class="active" to="/home/foods">Foods</router-link>
            </li>
        </ul>
        <router-view></router-view>
    </div>
</template>
```

## 路由传参-query

传参：

1. 路径中直接带参数：

```html
<router-link :to="`/home/games/detail?id=${gameInfo.id}&message=${gameInfo.message}`">{{gameInfo.name}}</router-link>
```

2. to对象形式传参：

```html
<router-link :to="{
     path: '/home/games/detail',
     query: {
         id: gameInfo.id,
         message: gameInfo.message
     }
}">
    {{gameInfo.name}}
</router-link>
```

接参：

通过`this.$router.query`取到参数

```html
<li>{{this.$route.query.id}}</li>
<li>{{this.$route.query.message}}</li>
```

## 命名路由

配置路由时，增加`name`属性：

```javascript
{
    path: 'games',
    component: Games,
    children: [
        {
            name: 'toDetail',
            path: 'detail',
            component: Detail
        }
    ]
}
```

使用时`to`传`name`属性：

```html
<router-link :to="{
    name: 'toDetail',
    query: {
    id: gameInfo.id,
    message: gameInfo.message
    }
}">
{{gameInfo.name}}
</router-link>
```

## 路由传参-params

路由配置：

```javascript
{
    path: 'games',
    component: Games,
    children: [
        {
            name: 'toDetail',
            path: 'detail/:id/:message',
            component: Detail
        }
    ]
}
```

传参：

路由配置：

```js
{
   path: 'games',
   component: Games,
   children: [
      {
         name: 'toDetail',
         path: 'detail/:id/:message', // path上需要携带占位符
         component: Detail
      }
   ]
}
```

1. 路径中直接带参数：

```html
<router-link :to="`/home/games/detail/${gameInfo.id}/${gameInfo.message}`">{{gameInfo.name}}</router-link>
```

2. to对象形式传参：

`params`传参，目的目的路由不要用`path`, 用`name`代替。因为原`path`用了`:id`等标识，要想访问该路由，需要写上完整的路径，很麻烦。

```html
<router-link :to="{
    name: 'toDetail', // 这里用name，不要用path
    params: {
        id: gameInfo.id,
        message: gameInfo.message
    }
}">
{{gameInfo.name}}
</router-link>
```

接参：

```html
<li>{{this.$route.params.id}}</li>
<li>{{this.$route.params.message}}</li>
```

## 传参优化-props

传参：

```javascript
{
    name: 'toDetail',
    path: 'detail/:id/:message',
    component: Detail,
    // 第一种写法，将key-value传给目标组件，但是只能传递固定值
    // props: { a: 1 }
    // 第二种写法，将params中的数据，以props传参方式传递给目标组件
    // props: true
    // 第三种写法，获取路由信息配置成对象数据传递
    props($route) {
        // 如果是query
        // return { id: $route.query.id, message: $route.query.message }
        // 如果是params
        return { id: $route.params.id, message: $route.params.message }
    }
}
```

接参时在目标组件中声明`props`属性接参:

```javascript
export default {
    name: 'Detail',
    // props: ['a'],
    props: ['id', 'message']
}
```

## router-link标签的replace属性

浏览器浏览记录存储有2种模式，一种是`push`模式，即不覆盖上一条记录，浏览器返回键可以返回到所有的记录；一种是`replace`模式，会覆盖上一条记录，浏览器返回键不能返回到上一条记录

`replace`开启方式很简单，只要在`router-link`标签上添加`replace`属性就行：

```html
<router-link replace :to="{
    name: 'toDetail',
    params: {
        id: gameInfo.id,
        message: gameInfo.message
    }
}">
{{gameInfo.name}}
</router-link>
```

## 编程式路由

1. 编程式路由，不适用`router-link`标签，使用路由器方法控制：

配置：

```javascript
methods: {
    routePush(gameInfo) {
    this.$router.push({
        name: 'toDetail',
        params: {
            id: gameInfo.id,
            message: gameInfo.message
        }
    });
    },
    routeReplace(gameInfo) {
        this.$router.replace({
            name: 'toDetail',
            params: {
                id: gameInfo.id,
                message: gameInfo.message
            }
        });
    }
}
```

使用：

```html
<button @click="routePush(gameInfo)">push模式</button>
<button @click="routeReplace(gameInfo)">replace模式</button>
```

2. 模拟浏览器的前进后退：

配置：

```javascript
methods: {
    goAhead() {
        this.$router.forward();
    },
    goBack() {
        this.$router.back();
    },
    go(step) {
        // go表示前进几步，forward是前进一步
        this.$router.go(step);
    }
}
```

使用：

```html
<button @click='goBack'>后退</button>
<button @click="goAhead">前进</button>
<button @click='go(3)'>go</button>
```

## 缓存路由组件

`keep-alive`保活，`include`表示哪些组件保活，注意这里是组件名。缓存多个写数组形式。不写`include`就是缓存所有这里切换的路由。

配置：

```html
<!-- 缓存一个  -->
<keep-alive include="Foods">
    <router-view></router-view>
</keep-alive>

<!-- 缓存多个 -->
<keep-alive :include="['Foods', 'Games']">
    <router-view></router-view>
</keep-alive>
```

## 小知识

1. 路由切换时，切换前的组件会被销毁
2. 每个被切换的组件`vc`对象上都有一个`$route`和`$router`，`$router`是路由器对象大家都公用一个，而`$route`是每个`vc`对象特有的路由

## 路由专有的生命周期钩子函数

当使用`keep-alive`缓存组件后，会给组件带来2个生命周期钩子

activated: 激活组件（路由切换到该组件）时触发。进入该组件后再进入该组件，不会触发。

deactivated: 去激活组件（路由切到其它组件）时触发。进入该组件后再进入该组件，不会触发。

```javascript
    activated() {
        this.timer = setInterval(()=>{
            this.opacity -= 0.01;
            if (this.opacity < 0) {
                this.opacity = 1;
            }
        }, 10);
    },
    deactivated() {
        clearInterval(this.timer);
    },
```

## 路由守卫

1、全局前置路由守卫

```javascript
// 全局前置路由守卫 初始化的时候被调用、每次路由切换之前被调用
router.beforeEach((to, from, next) => {
    // if 做业务逻辑判断是否给予路由跳转
    next();
});
```

2、全局后置守卫

```javascript
// 全局后置路由守卫 初始化的时候被调用、每次路由切换后被调用
router.afterEach((to, from, next) => {
    // 切换后的业务逻辑
});
```

3、独享守卫（该路由独享）

全局守卫和独享守卫同时存在时，先进全局守卫，再进独享守卫

```javascript
{
    path: 'foods',
    component: Foods,
    beforeEnter(to, form, next) {
        // 业务代码
        next();
    }
}
```

4、组件路由守卫

必须通过路由规则进入该组件，才会触发`beforeRouteEnter`

离开该组件时，才会触发`beforeRouteLeave`

```javascript
    // 通过路由规则，进入该组件时被调用
    beforeRouteEnter(to, from, next) {
        next();
    },
    // 通过路由规则，离开该组件时被调用
    beforeRouteLeave(to, from, next) {
        next();
    }
```

## hash 和 history模式

1、对一个`url`来说，什么是`hash`值？ —— `#`及其后面的内容就是`hash`值

2、`hash`值不会包含在`HTTP`请求中，即：`hash`值不会带给服务器（所以`hash`模式刷新不会报错）

3、`hash`模式：

        a. 地址中永远带着`#`号，不美观

        b. 若以后将地址通过第三方手机`app`分享，若`app`校验严格，则地址会被标记为不合法

        c. 兼容性较好

       d. `SEO`优化方面相对较差

4、`history`模式：

        a. 地址干净，美观

        b. 兼容性和`hash`模式相比略差

        c. 应用部署上线时需要后端人员支持，不然刷新的时候会发送请求，导致404报错

```javascript
 const router = new VueRouter({
    mode: 'hash', // 默认hash模式 可以改成history模式
    routes: [
        {
            path: '/about',
            component: About
        },
        {
```
