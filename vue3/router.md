## 路由配置

安装路由器

```shell
npm i vue-router
```

注意：路由组件应该放到`pages`或者`views`目录下面

router.ts配置：

```ts
import {createRouter, createWebHistory} from "vue-router";
import Home from '@/pages/Home.vue';
import News from '@/pages/News.vue';
import About from '@/pages/About.vue';
export default createRouter({
    history: createWebHistory(),
    routes: [
        {
            path: '/home',
            component: Home
        },
        {
            path: '/news',
            component: News
        },
        {
            path: '/about',
            component: About
        }
    ]
});
```

main.ts配置：

```ts
import { createApp } from 'vue'
import App from './App.vue'
import router from '@/router/index';
const app = createApp(App);
app.use(router);
app.mount('#app');
```

路由使用：

```js
<template>
  <div class="show">
    <div class="title">Vue3路由测试</div>
    <div class="navigator">
      <RouterLink to="/home" active-class="active">首页</RouterLink>
      <RouterLink to="/news" active-class="active">新闻</RouterLink>
      <RouterLink to="/about" active-class="active">关于</RouterLink>
    </div>
    <div class="content">
      <RouterView></RouterView>
    </div>
  </div>

</template>

<script setup lang="ts">
import { RouterView, RouterLink } from "vue-router";
</script>
```

命名路由：

```js
<RouterLink :to="{ name: 'news' }" active-class="active">新闻</RouterLink>
```

## query传参

传参与`Vue2`基本一样

```js
<template>
  <div class="news">
    <div class="news-navigator">
      <div class="news-title" v-for="news in newsInfo" :key="news.id">
        <!-- query形式传参第一种形式 -->
        <!-- <RouterLink :to="`/news/detail?title=${news.title}&detail=${news.detail}`" >{{ news.title }} </RouterLink> -->
        <!-- query形式传参第二种形式 -->
        <RouterLink :to="{
          path: '/news/detail',
          query: {
             title: news.title,
            detail: news.detail
          }
        }" >{{ news.title }} </RouterLink>
      </div>
    </div>
    <div class="news-content">
      <RouterView></RouterView>
    </div>
  </div>
</template>
```

接参：

`route`是个浅代理对象（`shallowReactive`），即只代理第一层。

个人猜测路由跳转时底层实现是将`query`整体替换成新的对象（普通对象）。

```js
<template>
    <ul>
        <li>{{ route.query.title }}</li>
        <li>{{ route.query.detail }}</li>
    </ul>
</template>

<script lang="ts">
    export default {
        name: 'Detail'
    }
</script>

<script setup lang="ts">
   import { useRoute } from 'vue-router';
   const route = useRoute();
</script>
```

如果想用解构方式接参，则`query`不具有响应式，因为`query`是个普通对象，所以无法更新界面数据。需要通过`toRefs`增加关联关系，即`query = toRef(useRoute(), 'query')`。`route`里面做过依赖收集，当`route`的`query`被替换后，触发更新，此时`query.title`获取的数据是`useRoute().query.title`，所以获取的是最新数据，界面渲染的结果也是最新的数据。

```js
<template>
    <ul>
        <li>{{ query.title }}</li>
        <li>{{ query.detail }}</li>
    </ul>
</template>

<script lang="ts">
    export default {
        name: 'Detail'
    }
</script>

<script setup lang="ts">
   import { useRoute } from 'vue-router';
   import { toRefs } from 'vue'; 
   const { query } = toRefs(useRoute());
</script>
```

## params传参

路由配置中要加上占位符：

```js
{
    name: 'detail',
    path: 'detail/:title/:detail?', // 问号表示detail参数可以不传入
    component: Detail
}
```

传参基本与`Vue2`一样：

```js
<template>
  <div class="news">
    <div class="news-navigator">
      <div class="news-title" v-for="news in newsInfo" :key="news.id">
        <!-- params形式传参第一种形式 -->
        <!-- <RouterLink :to="`/news/detail/${news.title}/${news.detail}`" >{{ news.title }} </RouterLink> -->
        <!-- params形式传参第二种形式 
          这种写法，只能使用name，不能用path，并且params中的数据不能有数组
        -->
        <RouterLink :to="{
          name: 'detail',
          params: {
            title: news.title,
            detail: news.detail
          }
        }" >{{ news.title }} </RouterLink>
      </div>
    </div>
    <div class="news-content">
      <RouterView></RouterView>
    </div>
  </div>
</template>
```

接参：

```js
<template>
    <ul>
        <li>{{ route.params.title }}</li>
        <li>{{ route.params.detail }}</li>
    </ul>
</template>

<script lang="ts">
    export default {
        name: 'Detail'
    }
</script>

<script setup lang="ts">
   import { useRoute } from 'vue-router';
   const route = useRoute();
</script>
```

## props传参

与`Vue2`基本一样

```js
export default createRouter({
    history: createWebHistory(),
    routes: [
        {
            path: '/home',
            component: Home
        },
        {
            name: 'news',
            path: '/news',
            component: News,
            children: [
                {
                    name: 'detail',
                    path: 'detail/:title/:detail?', // 问号表示detail参数可以不传入
                    component: Detail,
                    // 第一种写法，直接配置true，表明将params参数结构后传入目标组件。query参数不能这么传。
                    props: true
                    // 第二种写法，函数式。将返回结果作为props传入目标组件。query传参可以这么写。
                    // props(route) {
                    //     return route.query; // return route.params;
                    // }
                    // 第三种写法，对象式。将对象最为props传入目标组件。说明对象是一个固定值，很少用到。
                    // props: {
                    //     a: 1,
                    //     b: 2
                    // }
                }
            ]
        },
        {
            path: '/about',
            component: About
        }
    ]
});
```

接参：

```js
<template>
    <ul>
        <li>{{ title }}</li>
        <li>{{ detail }}</li>
    </ul>
</template>

<script lang="ts">
    export default {
        name: 'Detail'
    }
</script>

<script setup lang="ts">
   defineProps(['title', 'detail'])
</script>
```

## 编程式路由导航

```js
<template>
  <div class="news">
    <div class="news-navigator">
      <div class="news-title" v-for="news in newsInfo" :key="news.id">
        <button @click="showDetail(news)">查看详情</button>
        <RouterLink :to="{
          name: 'detail',
          params: {
            title: news.title,
            detail: news.detail
          }
        }" >{{ news.title }} </RouterLink>
      </div>
    </div>
    <div class="news-content">
      <RouterView></RouterView>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  name: "News"
}
</script>

<script setup lang="ts">
import { RouterView, RouterLink, useRouter } from 'vue-router';
const newsInfo = [
  { id: 1, title: '今天是个好日子', detail: '天气很好'},
  { id: 2, title: '明天干嘛呢', detail: '要开始新的生活了'}
];

const router = useRouter();
interface News {
  title: string,
  detail?: string
}
// route.push route.replace
function showDetail(news: News) {
  router.push({
    name: 'detail',
    params: {
      title: news.title,
      detail: news.detail
    }
  })
}
</script>
```
