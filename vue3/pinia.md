## 基础配置

`main.ts`引用：

```ts
import { createApp } from 'vue'
import App from './App.vue'
import { createPinia } from "pinia";

const app = createApp(App);
const pinia = createPinia();
app.use(pinia);
app.mount('#app');
```

`count.ts`的`store`配置：

```ts
import { defineStore } from 'pinia';
export const useCountStore = defineStore('count', {
    // 配置响应动作
    actions: {
      add(value: number) {
          // this是当前的store对象
          this.sum += value;
      }
    },
    state() {
        return {
            sum: 2,
            title: 'pinia测试'
        }
    }
});
```

`Count.vue`应用：

```js
<template>
    <div class="wrapper">
        <div class="title">{{ countStore.title }}</div>
        <div>值：{{ countStore.sum }}</div>
        <select class="select-step" v-model.number="step">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
        </select>
        <div>
            <button @click="add">加</button>
            <button @click="sub">减</button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'Count'
}
</script>

<script setup  lang="ts">
import { ref } from 'vue';
import { useCountStore } from '@/store/count';

// countStore是个vue proxy对象，sum是个ObjectRefImpl
// proxy下有ref对象，vue做了处理，不用countStore.sum.value取值
// 直接countStore.sum就可以获取数据
let countStore = useCountStore ();
console.log(countStore)
let step = ref(1);
function add() {
  // 第一种修改方式
  // countStore.sum += step.value;
  // 第二种写法 批量修改数据
  // countStore.$patch({
  //   sum: countStore.sum + step.value,
  //   title: 'pinia'
  // });
  // 第三种写法 通过store中配置的action触发数据修改
  countStore.add(step.value);
}

function sub() {
  countStore.sum -= step.value;
}

</script>
```

## storeToRefs

使用`storeToRefs`让取数更优雅：

```js
<template>
    <div class="wrapper">
        <div class="title">{{ title }}</div>
        <div>值：{{ sum }}</div>
        <select class="select-step" v-model.number="step">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
        </select>
        <div>
            <button @click="add">加</button>
            <button @click="sub">减</button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'Count'
}
</script>

<script setup  lang="ts">
import { ref, toRefs } from 'vue';
import { useCountStore } from '@/store/count';
import { storeToRefs } from 'pinia';

// 使用toRefs可以将useCountStore()的store数据转为ObjectRefImpl对象, 出现2层ObjectRefImpl对象
// 但同时也会将非store数据，比如action方法转为了ObjectRefImpl对象，效率低，所以不用toRefs
// let {sum, title} = toRefs(useCountStore());
// storeToRefs只会将store的属性转为ObjectRefImpl对象，不会将方法转为ObjectRefImpl对象，效率好。
// 同时相当于将原useCountStore()的store数据的ObjectRefImpl对象直接取出来另存到普通对象中
// 没有像toRefs再包一层ObjectRefImpl对象， 这样还是只有1层ObjectRefImpl对象，效率好
let { sum, title } = storeToRefs(useCountStore());
let step = ref(1);
function add() {
  sum.value += step.value;
}

function sub() {
  sum.value -= step.value;
}

</script>
```

## getters

`count.ts`配置

```ts
import { defineStore } from 'pinia';
export const userCountStore = defineStore('count', {
    // 配置响应动作
    actions: {
      add(value: number) {
          // this是当前的store对象
          this.sum += value;
      }
    },
    state() {
        return {
            sum: 2,
            title: 'pinia测试'
        }
    },
    getters: {
        bigNum(state) {
            // this也是state，所以也可以写return this.sum * 10;
            console.log(state === this); // true
            return state.sum * 10;
        }
    }
});
```

取数时当做`store`属性值直接取就行：

```js
let { sum, title, bigNum } = storeToRefs(userCountStore());
```

## $Subscribe

`$subscribe`可以监视`state`数据是否变化，变化后触发回调函数。

```js
<template>
    <div class="wrapper">
        <div class="title">{{ title }}</div>
        <div>值：{{ sum }}</div>
        <div>放大十倍：{{ bigNum }}</div>
        <select class="select-step" v-model.number="step">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
        </select>
        <div>
            <button @click="add">加</button>
            <button @click="sub">减</button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'Count'
}
</script>

<script setup  lang="ts">
import { ref, toRefs } from 'vue';
import { useCountStore } from '@/store/count';
import { storeToRefs } from 'pinia';
const counotStore = useCountStore();
let { sum, title, bigNum } = storeToRefs(counotStore);
let step = ref(1);

// 监视store数据变化
counotStore.$subscribe((mutate, state) => {
    // counotStore中的数据变化时，会触发，类似于watch 
    // mutate是改变数据的事件，state是整个store数据（vue proxy对象）
    console.log(mutate, state);
});

function add() {
  sum.value += step.value;
}

function sub() {
  sum.value -= step.value;
}
</script>

```

## 组合式配置方式

基础配置中使用了`options`形式来配置`store`，这里使用组合方式配置`store`。

```ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

// 组合式
export const useCountStore = defineStore('count', () => {
    // store数据
    let sum = ref(2);
    let title = ref('pinia测试');

    // 响应动作
    function add(value: number) {
        sum.value += value;
    }
    
    // getters
    const bigNum = computed(() => {
        return sum.value * 10;
    });

    return {
        sum,
        title,
        add,
        bigNum
    }
});
```
