## Vuex原理图

图![](D:\document\笔记\notes\img\vuex原理图.png)

## 基础配置

安装： `npm i vuex@3 --save`

`index.js`简单配置

```javascript
import Vuex from 'vuex'
import Vue from 'vue'
// 一定要在生成Stroe实例之前use vuex组件
Vue.use(Vuex)

// 相应组件中的动作
const actions = {}
// 处理数据
const mutations = {}
// 存储数据
const state = {}

export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

main.js引用：

```js
import Vue from "vue"
import App from './App.vue'
import store from './store/index'
Vue.config.productionTip = false;

new Vue({
    el: "#app",
    render: h => h(App),
    store
})
```

## 应用

index.js文件：

`state`是数据，可以理解为数据库，`mutations`是数据处理层，类似于后台与数据库打交道的那一层，`actions`就是服务层，处理业务逻辑的。虽然`vue`组件中可以通过`this.$store.state`或者`actions`中可以通过`context`可以访问到`state`，但是不建议在这2处处理数据，数据处理都交由`mutations`处理，保持框架清晰，也便于`vuex`工具统计数据变化。

```javascript
import Vuex from 'vuex'
import Vue from 'vue'
// 一定要在生成Stroe实例之前use vuex组件
Vue.use(Vuex)

// 相应组件中的动作
const actions = {
    // increase(context, value) {
    //     context.commit('INCREASE', value);
    // },
    // decrease(context, value) {
    //     context.commit('DECREASE', value);
    // },
    oddIncrease(context, value) {
        if (context.state.sum % 2) {
            context.commit('ODDINCREASE', value);
        }
    },
    waitIncrease(context, value) {
        setTimeout(() => {
            context.commit('WAITINCREASE', value);
        }, 1000)
    }
}
// 处理数据
const mutations = {
    INCREASE(state, value) {
        state.sum += value;
    },
    DECREASE(state, value) {
        state.sum -= value;
    },
    ODDINCREASE(state, value) {
        state.sum += value;
    },
    WAITINCREASE(state, value) {
        state.sum += value;
    }
}
// 存储数据
const state = {
    sum: 0
}

export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

组件：

```html
<template>
  <div>
    <h1>当前计数：{{this.$store.state.sum}}</h1>
    <select v-model.number="num">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
    <button @click="increase">+</button>
    <button @click="decrease">-</button>
    <button @click="oddIncrease">当前求和为奇数再加</button>
    <button @click="waitIncrease">等一等再加</button>
  </div>
</template>

<script>
export default {
    name: 'Count',
    data() {
      return {
        num: 1,
        sum: 0
      }
    },
    methods: {
      increase() {
        // this.$store.dispatch('increase', this.num);
        this.$store.commit('INCREASE', this.num)
      },
      decrease() {
        // this.$store.dispatch('decrease', this.num);
        this.$store.commit('DECREASE', this.num)
      },
      oddIncrease() {
        this.$store.dispatch('oddIncrease', this.num);
      },
      waitIncrease() {
        this.$store.dispatch('waitIncrease', this.num);
      }
    }
}
</script>

<style scoped>
</style>
```

## getters

`getters`类似于计算属性，通过函数名获取值

index.js中配置：

```javascript
const getters = {
    bigSum(state) {
        return state.sum * 10;
    }
}
```

注册：

```javascript
export default new Vuex.Store({
    actions,
    mutations,
    getters,
    state
})
```

使用：

```html
<h4>当前计数x10：{{this.$store.getters.bigSum}}</h4>
```

## 4个map方法

`mapState()`和`mapGetters()`是计算属性的快速生成方式。参数可以传入对象和数组。如果是对象的`key`和`value`相同可以简写成数组。对象的`key`是计算属性名，`value`是`$store.state`的属性或者是`$store.getters`的函数。

返回的也是一个对象，`key`是属性名，`value`是函数，其作用就是用来生成计算属性，如下;

```js
// mapState()的结果
Object
school: ƒ mappedState()
sum: ƒ mappedState()
[[Prototype]]: Object
// mapGetters()的结果
{bigSum: ƒ}
bigSum: ƒ mappedGetter()
[[Prototype]]: Object
```

`mapActions()`和`mapMutations()`是method函数的快速生成方式。参数可以传入对象和数组。如果是对象的`key`和`value`相同可以简写成数组。对象的`key`是`method`中要生成的函数名，`value`是`$store._actions`的属性或者是`$store._mutations`的函数。

返回的是一个对象， `key`是`methods`名，`value`是函数， 其作用就是用来简写`methods`·的函数

```js
// mapMutations()的结果
{increase: ƒ, decrease: ƒ}
decrease: ƒ mappedMutation()
increase: ƒ mappedMutation()
[[Prototype]]: Object
// mapActions()的结果
{oddIncrease: ƒ, waitIncrease: ƒ}
oddIncrease: ƒ mappedAction()
waitIncrease: ƒ mappedAction()
[[Prototype]]: Object
```

导入语句：

```js
import { mapGetters, mapState, mapActions, mapMutations } from 'vuex'
```

### mapState

```javascript
// 计算属性写法
computed: {
    sum() {
        return this.$store.state.sum;
    },
    school() {
        return this.$store.state.school;
    }
}
// mapState写法
computed: {
    // mapState对象写法：
    ...mapState({sum: 'sum', school: 'school'}),
    // mapState数组写法：属性名和属性值相同时才能采用数组写法，对象写法中sum：'sum'属性名和属性值都是sum，所以可以简写
    ...mapState(['sum', 'school'])
}
```

### mapGetters

```javascript
// 计算属性写法
computed: {
    bigSum() {
        return this.$store.getters.bigSum;
    }
}
// mapGetters写法
computed: {
    // mapGetters对象写法：
    ...mapGetters({ bigSum: 'bigSum'}),
    // mapGetters数组写法：属性名和属性值相同时才能采用数组写法，对象写法中sum：'sum'属性名和属性值都是sum，所以可以简写
    ...mapGetters(['bigSum'])
}
```

### mapMutations

`mapMutations`生成的函数包含如下代码`this.$store.commit('xxx', value)`，如下：

```javascript
increase(val) {
    this.$store.commit('INCREASE', this.num)
},
```

具体使用案例：

```javascript
methods: {
   // mapMutations对象实现
   ...mapMutations({ increase: 'INCREASE', decrease: 'DECREASE'}),
   // mapMutations数组实现
   ...mapMutations(['INCREASE', 'DECREASE'])
}
```

### mapActions

`mapActions`生成的函数包含如下代码`this.$store.dispatch('oddIncrease', value)`，如下：

```javascript
oddIncrease(value) {
    this.$store.dispatch('oddIncrease', value);
}
```

具体使用案例：

```javascript
methods: {
    // mapActions对象实现
    ...mapActions({ oddIncrease: 'oddIncrease', waitIncrease: 'waitIncrease'}),
    // mapActions数组实现
    ...mapActions(['oddIncrease', 'waitIncrease'])
}
```

## 模块化编码

### 配置

vuex多用于模块化，通过module配置不同的模块，`index.js配置如下`：

```javascript
import Vuex from 'vuex'
import Vue from 'vue'
import countOptions from './count'
import studentOptions from './student'
Vue.use(Vuex)
export default new Vuex.Store({
    modules: {
        countInfo: countOptions,
        studentInfo: studentOptions
    }
})
```

其它配置另起`js`文件配置`count.js`：

```javascript
// count操作
export default {
    namespaced: true,
    actions: {
        oddIncrease(context, value) {
            if (context.state.sum % 2) {
                context.commit('ODDINCREASE', value);
            }
        },
        waitIncrease(context, value) {
            setTimeout(() => {
                context.commit('WAITINCREASE', value);
            }, 1000)
        }
    },
    mutations: {
        INCREASE(state, value) {
            state.sum += value;
        },
        DECREASE(state, value) {
            state.sum -= value;
        },
        ODDINCREASE(state, value) {
            state.sum += value;
        },
        WAITINCREASE(state, value) {
            state.sum += value;
        }
    },
    getters: {
        bigSum(state) {
            return state.sum * 10;
        }
    },
    state: {
        sum: 0,
        school: 'ai'
    }
}
```

`student.js`:

```javascript
import axios from "axios";
import { nanoid } from "nanoid";
// student操作
export default {
    namespaced: true,
    actions: {
        addChen(context, value) {
            if (value.name.indexOf('陈') === 0) {
                context.commit('ADDSTUDENT', value);
            }
        },
        addRandom(context) {
            axios.get('https://api.uixsj.cn/hitokoto/get?type=social').then((val) => {
                context.commit('ADDSTUDENT', { name: val.data, id: nanoid() });
            }).catch(() => {});
        }
    },
    mutations: {
        ADDSTUDENT(state, value) {
            state.student.unshift(value);
        }
    },
    getters: {
        firstStudent(state) {
            return state.student[0];
        }
    },
    state: {
        student: [{ name: 'no1', id: '1' }]
    }
}
```

### 使用

简写方式使用：

```javascript
export default {
    name: 'Count',
    data() {
      return {
        num: 1      
      }
    },
    computed: {
      // 这种方式只能拿到countInfo， studentInfo
      ...mapState(['countInfo', 'studentInfo']),
      // 这种方式是从countInfo的state中查找sum、school
      ...mapState('countInfo', ['sum', 'school']),
      // 从studentInfo的state查找student
      ...mapState('studentInfo', ['student']),
      // 从countInfo的gatters中查找bigSum
      ...mapGetters('countInfo', ['bigSum'])
    },
    methods: {
      // 从countInfo的mutations中查找INCREASE和DECREASE，建立映射关系
      ...mapMutations('countInfo', { increase: 'INCREASE', decrease: 'DECREASE'}),
      // 从countInfo的actions中查找oddIncrease和oddIncrease，建立映射关系
      ...mapActions('countInfo', ['oddIncrease', 'waitIncrease'])
    }
}
```

非简写形式使用：

```javascript
export default {
    name: 'Student',
    data() {
      return {
        name: ''
      }
    },
    computed: {
      student() {
        // 从studentInfo的state中取数
        return this.$store.state.studentInfo.student;
      },
      sum() {
        // 从countInfo的state中取数
        return this.$store.state.countInfo.sum;
      },
      firstStudent() {
        // 从studentInfo的getters中取firstStudent形式是xxx/yyy
        return this.$store.getters['studentInfo/firstStudent'].name;
      }
    },
    methods: {
      addStudent() {
        // 从studentInfo的mutations中查找ADDSTUDENT，形式xxx/yyy
        this.$store.commit('studentInfo/ADDSTUDENT', { name: this.name, id: nanoid()});
        this.name = '';
      },
      addChen() {
        // 从studentInfo的actions中查找addChen，形式xxx/yyy
        this.$store.dispatch('studentInfo/addChen', { name: this.name, id: nanoid() });
        this.name = '';
      },
      addRandom() {
        // 从studentInfo的actions中查找addRandom，形式xxx/yyy
        this.$store.dispatch('studentInfo/addRandom');
      }
    }
}
```
