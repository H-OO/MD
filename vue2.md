## Vue 2.x 使用相关

**安装必要的包**

```
  npm i vue -g
  npm i vue-cli -g
```
---------------------------------------------------------------------

**通过vue-cli的webpack模板搭建项目**
```
  vue init webpack projectName
```
---------------------------------------------------------------------

**创建vue实例**
```javascript
  new Vue({})
```
创建出根实例，可传入选项对象  
选项对象成员，如下：  
* `el`           --- isStr 实例挂载元素
* `data`         --- isObj 数据改变触发视图更新（注意需是在实例前存在的属性才是响应式的）
* `methods`      --- isObj 存放方法进实例对象中 
* `beforeCreate` --- isFn  生命钩子，在实例创建前调用
* `created`      --- isFn  生命钩子，在实例创建完毕后调用（常用）
* `mounted`      --- isFn  生命钩子，在实例挂载完毕后调用
* `destroyed`    --- isFn  生命钩子，实例被销毁时调用
* `updated`      --- isFn  生命钩子，虚拟DOM重新渲染后调用（注意此时可能未挂载会视图上）
* `computed`     --- isObj 计算属性，存放相关计算方法，方法需在模板中调用，通过return将计算结果返回
* `watch`        --- isObj 监听属性，可以监听data属性中某个成员的值是否发生改变，发生改变则调用

无论是属性还是生命钩子函数，this都指向根实例，也就是指向调用者  
注意：若内部还嵌有函数体时，内部嵌套的函数体this是不指向根实例的（若想使用根实例，可将根实例作为参数传入）
```javascript
  new Vue({
    data: {
      num: 0
    },
    computed: {
      // 成员需要在模板中引用
      message: function () {
        // this指向根实例
        // 最终将计算出的结果return回去给视图
        var result = ''
        return result
      }
    },
    watch: {
      // 监听成员，该成员的值发生改变就会执行函数
      num: function () {
        // this指向根实例
        // todosomething
      }
    }
  })
```
---------------------------------------------------------------------

**视图更新完毕后回调**  
```javascript
  this.$nextTick(function () {
    // this指向根实例
  })
```
* 在修改数据之后会被立即调用，回调则等待视图更新完毕后执行（回调中的this会自动指向根实例）

---------------------------------------------------------------------

**路由跳转**  
编程式跳转如下：
```javascript
  // 路径动态生成 （需要在路由文件中，对路径进行动态生成的配置，在路由路径最末添加 /:id）
  // 方式1 ==> .../home/xxx
  this.$router.push({
    name: 'Home',
    params: {id: 'xxx'}
  })
  // 方式2 ==> .../home/xxx
  this.$router.push({path: `/home/${id: 'xxx'}`})

  // 跳转路由带参数提供查询 ==> .../home?id=xxx
  this.$router.push({ path: '/home', query: {id: 'xxx'} });
  // 查询方法
  this.$route.path // 全路径
  this.$route.query // 参数对象
  this.$route.name // 组件名
```

使用router-link组件导航，如下：
```html
  <!-- <router-link></router-link>默认被渲染成a标签 -->
  <!-- 通过to属性传入跳转的路由地址 -->
  <router-link to="/home"></router-link>
```
---------------------------------------------------------------------

**类名绑定**  
* js
```javascript
  // 后缀为vue的组件写法，目的是返回组件对象，通过注册挂载到父组件上
  export default {
    data () {
      return {
        list: {
          liAction: 0 // 当前高亮元素的下标应该满足该值
        },
        isActive: true, // 是否高亮
        active1: 'active1', // 类名1
        active2: 'active2' // 类名2
      }
    }
  }
```
* template
```html
  <!-- 对象语法添加类名 -->
  <ul class="outer">
    <!-- 条件语句解析: 当右边判断语句成立为true，则className成功绑定 -->
    <!-- 为首个li添加高亮样式 -->
    <li :class="{className: index === list.liAction}" v-for="(item, index) in data" :key="index"></li>
  </ul>

  <!-- 数组语法添加类名 -->
  <!-- 可以使用三元表达式 -->
  <div :class="[isActive ? active1 : '', active2]"></div>
```
---------------------------------------------------------------------

**v-if and v-show**  
* js
```javascript
  export default {
    data () {
      return {
        num: 0,
        isShow: true
      }
    }
  }
```
* template
```html
  <!-- v-if v-else-if v-else -->
  <div v-if="num > 0">v-if</div>
  <div v-else-if="num === 0">v-else-if</div>
  <div v-else>v-else</div>

  <!-- v-show -->
  <div v-show="isShow">v-show</div>
```
v-if 与 v-show的区别：  
  `v-if` 只有在条件成立下元素才会渲染，确保切换的过程条件块内的事件监听器和子组件适当被销毁和重建  
  `v-show` 不管初始条件是什么，元素始终会渲染，只是进行简单的样式切换显隐  
使用场景：  
  高频切换需求场景下使用`v-show`，它的开销只在于初始渲染  
  低频切换需求场景下使用`v-if`，它存在更多的切换开销  

---------------------------------------------------------------------

**数组更新检测**  
可使用变异方法改变数据触发视图更新，相关数组变异方法如下：  
  * `push()`      --- 追加元素
  * `pop()`       --- 删除最后一个元素并返回
  * `shift()`     --- 删除首个元素并返回
  * `unshift()`   --- 获取最后一个元素（不修改原数组）
  * `splice()`    --- 单个或区间式截取数组并返回，参数三可选，为替换的内容
  * `sort()`      --- 对数组进行排序并返回
  * `reverse()`   --- 对数组进行反向排序并返回
  ```javascript
    // 数字类型正序排列
    this.arr.sort(function (x, y) {
      return x - y
    })
    // 数字类型反序排列
    this.arr.sort(function (x, y) {
      return y - x
    })
    // 字母类型排序，无需参数
    this.arr.sort()
  ```

非变异方法不修改原数组而是返回新数组，相关数组非变异方法如下：  
  * `concat()`    --- 数组拼接返回新数组
  * `slice()`     --- 从某个下标开始获取并返回新数组
  * `filter()`    --- 过滤作用返回新数组
  ```javascript
    // 过滤
    this.arr.filter(function (item) {
      // 设置输出条件
      if (item % 2 === 0) {
        return item
      }
    })
  ```
  注意：由于JS本身限制，以下修改数组的情况不能被检测，也就是视图不更新
  ```javascript
    // 视图不刷新情景
    // 1.通过索引改变数组中的某个元素时
    const index = 0
    this.arr[index] = 'newValue'

    // 2.通过数组length改变数组长度时
    const newLength = 1
    this.arr.length = newLength

    // 有了问题自然就要解决，解决方法如下：↓↓↓
    // 解决问题1：使用Vue.set(array, index, newValue)方法
    // 全局Vue.set方法的别名（this.$set）
    this.$set(this.arr, 0, 'x') // [1, 2, 3] ==> ['x', 2, 3]

    // 解决问题2：使用变异方法splice实现相同效果
    this.arr.splice(0) // []
  ```
  全局`Vue.set`方法的别名为`this.$set`  

---------------------------------------------------------------------

**对象更改检测**  
由于JS本身限制，无法检测对象成员的添加与删除  
对于已经创建的实例，是不能动态添加根级别的响应属性的；但可以向嵌套对象添加响应式属性  
使用`Vue.set(object, key, value)`方法  
```javascript
  // 使用该方法的别名
  this.$set(this.obj, 'newKey', 'newValue')
```
当想添加多个响应式属性时，使用原生`Object.assign({}, object, {})`方法  
```javascript
  // 一次添加多个属性
  // 该方法返回一个新对象，是两个对象的集合
  this.obj = Object.assign({}, this.obj, {newKey: 'newValue'})
```  

---------------------------------------------------------------------

**vuex状态管理**  
解决由于多个状态分散的跨越在许多组件和交互间各个角落，应用复杂度逐渐增长的问题  
好！开始进行vuex之旅，请系好安全带：`npm install vuex --save-dev`  
* 创建store.js
```javascript
  import Vue from 'vue'
  import Vuex from 'vuex'
  Vue.use(Vuex) // '注入'每一个子组件
  const store = new Vuex.Store({
    // 状态仓库
    state: {
      // 存放所有状态
      // 外部访问示例：this.$store.state
    },
    // 改变状态的方法（由外部通过commit方法进行触发）
    mutations: {
      // 调用commit方法，vuex内部会根据commit中的参数1选中下列匹配的成员进行触发
      // 外部调用示例：this.$store.commit('handler', 'xxx')
      handler: function (state, payload) {
        // 参数1为状态仓库
        // 参数2为传递过来的参数
      }
    }
  })
  export default store
```
* cd main.js
```javascript
  import store from 'your path/store'
  new Vue({
    store
  })
  // Vuex调用了Vue.use(Vuex)方法，将store对象注入到每个组件中，也就是全局化
  // 通过 this.$store 访问  
```
`this.$store.commit()` 参数不同写法：  
a. `('handler', 'xxx')` 最简写  
b. `('handler', {content: 'xxx'})` 可读性  
c. `({type: 'handler', content: 'xxx'})` 对象风格  
注意：handler方法的参数无需改变  

---------------------------------------------------------------------

**组件通信-父传子**  
`props`
```
  // child.vue
  export default {
    // 传值指令名称
    props: ['message']
  }

  // parent.vue
  import child from 'your path/child'
  export default {
    components: {
      child
    },
    data () {
      return {
        msg: 'xxx'
      }
    }
  }

  // 父组件中动态传值使用子组件
  <child :message="msg"></child>

```
---------------------------------------------------------------------

**组件通信-子传父**  
`v-on`监听自定义事件  
`$emit`触发自定义事件  
```
  // child.vue
  <button @click="trigger"></button>
  export default {
    methods: {
      trigger: function () {
        // 点击按钮后执行 $emit 方法，将信息传递给父组件
        this.$emit('msg', {message: 'xxx'})
      }
    }
  }

  // parent.vue
  <child @msg="handler"></child>
  export default {
    components: {
      child
    },
    methods: {
      // 监听自定义msg事件
      handler: function () {
        // arguments对象接收子组件传递过来的信息
      }
    }
  }

```

---------------------------------------------------------------------

**组件通信-平行组件传值**  
`bus`  
* bus.js
```javascript
  import Vue from 'vue'
  export default new Vue
```
* 平行组件通信
```javascript
  // 平行组件1 传值方
  import Bus from 'your path/bus'
  export default {
    methods: {
      trigger: function () {
        // 通过Bus触发自定义事件传值
        Bus.$emit('handler', {msg: 'xxx'})
      }
    }
  }

  // 平行组件2 接收方
  import Bus from 'your path/bus'
  export default {
    // 在钩子函数中监听自定义事件
    created () {
      // 监听自定义事件
      Bus.$on('handler', function () {
        // arguments接收值
        // 当前this不指向实例，有需要可使用钩子函数的this
      })
    }
  }
```
---------------------------------------------------------------------

**ref**  
在js中访问元素  
```
  <div ref="ele"></div>

  export default {
    methods: {
      getEle: function () {
        // this.$refs.ele
      }
    }
  }
```

---------------------------------------------------------------------
