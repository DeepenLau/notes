
## 1. vue 响应式数据的理解

   数组和对象类型当值变化的时候如何劫持到，对象内部通过defineReactive 方法，使用Object.defineProperty将属性劫持（只会劫持已经存在属性），数组则是通过重写数组方法来实现

   Object.defineProperty 和 Proxy 区别

   - Object.defineProperty 提前递归，性能稍差
   - Proxy 不需要提前递归，取值的时候代理

   依赖收集原理

   - 每个属性都拥有自己的dep属性，存放它所依赖的watcher，当属性变化后，通知自己对应的watcher去更新

   性能相关

   - 对象层级过深，性能稍差
   - 不需要响应式的数据不放到data中
   - Object.freeze() 也可以避免被Object.defineProperty，冻结数据
```html
<div id="app"></div>
```

```js
let state = {count: 0}

defineReactive(state)
let active

function defineReactive(obj) {
  for(let key in obj) {
    let value = obj[key]
    let dep = []
    Object.defineProperty(obj, key, {
      get() {
        if (active) {
        	dep.push(active)
        }
        return value
      },
      set(newValue) {
        value = newValue
        dep.forEach(watcher => watcher())
      },
    })
  }
}

watcher(() => {
  app.innerHTML = state.count
})

function watcher(fn) {
  active = fn
  fn()
  active = null
}
```

## 2. Vue如何检测数组变化

考虑到性能原因没有对数组的每一项进行拦截，而是重写数组方法（push,shift,splice,unshift,sort,reverse, 会改变原数组的方法）



改变 length 和 下标 无法更新，改变数组要更新，只有通过劫持的方法和 $set() (用重写splice 实现)

```js
let state = [1,2,3]

let originalArray = Array.prorotype
let arrayMethods = Object.create(originalArray)

function defineReactive(obj) {
  arrayMethods.push = function (...args) {
    render()
    originalArray.push.call(this, ...args)
  }
  
  obj.__proto__ = arrayMethods
}

defineReactive(state)

function render() {
  app.innerHTML = state
}

render()
setTimeout(() => {
  state.push(4)
}, 1000)
```

## 3.模板编译原理

1. template => ast - parseHTML

   正则匹配html标签字符串操作

2. 对静态语法做静态标记 - markUp

3. 重新生成代码 通过 new Function 变成 render函数 - codeGen



vue 整体template到真实dom整体转换过程

template => ast => render函数字符串 =>通过 new Function 变成 render函数 => 虚拟dom => diff(update 时) =>真实dom

## 4.声明周期钩子是如何实现的

声明周期就是回调函数，在创建组件实例的过程中会调用对应的钩子方法

钩子函数会合并转换成数组，因为会有mixin或者默认选项合并钩子

mergeOptions

mergeHook

## 5. Vue.mixin 的使用场景和原理

抽离公共的业务逻辑，原理类似对象的继承，全局和局部相同属性，就近原则获取

缺点：命名冲突，依赖问题，数据来源问题

原理：mergeOptions

## 6. nextTick 原理

nextTick的回调是在下次dom更新循环结束之后执行的延迟回调，在修改数据之后立即使用这个方法，获取更新后的dom，原理就是异步方法经常和事件循环一起来问

vue 多次更新数据，最终会进行批处理更新，内部调用的就是 nextTick 实现了延迟更新，用户自定义的nextTick中的回调会被延迟到更新完成后调用，从而可以获取更新后的dom

```js
function render() {
  console.log("渲染");
}
let arr = [];
let pending = false;
function flushCallbacks() {
  arr.forEach((fn) => fn());
  pending = false;
}
function nextTick(fn) {
  arr.push(fn)
  if (!pending) {
    pending = true;
    Promise.resolve().then(flushCallbacks);
  }
}

console.log("改数据");
console.log("改数据");
console.log("改数据");
console.log("改数据");


nextTick(render);
nextTick(render);
nextTick(render);
```

将一个宏任务执行并出队 => 将一队微任务执行并出队 => 执行渲染操作，更新界面 => 处理 work 相关任务

## 7.vue为什么需要虚拟dom

虚拟dom就是用js对象来描述真实dom，是对真实dom的抽象，由于直接操作dom性能低但是js层的操作效率高，可以将dom操作优化成对象操作，最终通过diff算法对比差异进行更新dom（减少了对真实dom的醋熬做）

虚拟dom不依赖真实平台环境从而也可以实现跨平台

## 8.vue 中的diff原理（key的作用）

- 平级比较，不考虑跨级比较 O(n)
- 深度优先递归方式 + 双指针

比较子节点才是核心diff算法 updateChildren()

key 的作用，通过key创建映射表,找到可服用的元素，提高性能

## 9.Vue.set 方法如何实现

1. 数组：通过 splice ，因为splice已经被改写，会通知更新
2. 对象：不存在属性就 调用 defineReactive() 然后手动通知更新

## 10. 生命周期方法有哪些，一半在哪一步发起请求及原因

beforeCreate            setup

created                      setup

beforeMount

mounted                   服务端渲染不支持 mounted

beforeUpdate

updated

beforeDestroy.           beforeUnmount

Destroyed                   unmount

## 11.vue组件间的数据通信方式以及之间的区别

- props, $emit / $on

  initProps() / defineReactive(vm._props) / 再代理到 vm 上

  <my @click="fn"> => my.$on('click', fn), my.$emit('click')

- $parent, $children

- $attrs / $listeners

- provide / inject

- $refs

- eventBus

- vuex

- slot ，作用域插槽



## 12. $attrs 是为了什么解决什么问题，应用场景，provide/inject 不能解决它的问题吗

$attrs 主要的作用就是实现批量传递数据，project/inject 更适合应用在插件中，主要是实现跨级数据数据传递

## 13. Vue组件渲染流程

1. 父子组件的先后顺序

   组件的创建过程是先父后子，卸载过程是先子后父

2. 组件是如何渲染到页面上的



- 在渲染父组件的时候会创建父组件的虚拟节点，其中可能包含子组件的标签
- 在创建虚拟节点时，获取组件的定义，使用Vue.extend 生成组件的构造函数
- 将虚拟节点转化成真实节点时，会创建组件的实例并且调用组件的$mount方法
- 所以组件的创建过程是先父后子

父 beforeCreate

父created

​     子 beforeCreate

​     子 created



## 14.vue组件的data为什么是一个函数

每次使用组件时，都会对组件进行实例化操作，并且调用data函数返回一个对象作为组件的数据源，这样可以保证多个组件间的数据互不影响

## 15. v-if 和 v-show 的区别

v-if 在编译的过程中就被编译成三元表达式，控制是否渲染dom

v-show 是指令，仅控制显示隐藏

## 16. v-if 和 v-for 为啥不能一起用 

> **永远不要把 `v-if` 和 `v-for` 同时用在同一个元素上。*

1. 当 Vue 处理指令时，v-for 比 v-if 具有更高的优先级

   - 通过三目语句处理v-if指令
   - 如果条件为true，则创建li及子节点，否则执行`_e`（createEmptyVNode）创建一个空节点

   也就是说每次循环重新渲染，每个都会判断一次，false 则生成空节点

   生成的代码不一样

   先循环，在循环里判断

   建议把判断提到上一层，整理循环里就没有判断，都是渲染的

   或者用计算属性，预先计算出需要渲染的列表

2. 哪怕我们只渲染出一小部分用户的元素，也得在每次重渲染的时候遍历整个列表，不论活跃用户是否发生了变化。

   因为一个组件一个watcher，当通知更新watcher，就会重新执行组件render方法，重新生成vnode，这里就需要重新遍历一遍数据，要判断组件里哪些数据变化，应该更新哪些节点，就需要新老 vnode 做 diff

   所以推荐用计算属性，因为有只要属性没改变，就不会重新计算，有缓存，或者把v-if提高到上一层

## 17. Vue.use 是干什么的，原理是什么

注册插件 插件需有 apply 方法，参数有 Vue 构造函数

## 18. Vue-router 有几种钩子

全局守卫 - 全局 beforeEach afterEach

路由守卫 - 写在路由表里面的

组件守卫 - 写在组件里面的

## 19.Vue-router 两种模式

hash 模式

history 模式

抽象模式 - 给非浏览器环境用的

## 20.组件中的写 name 的好处以及作用

- 实现递归组件
- 实现缓存功能 keep-alive
- 识别组件（跨级通信时非常重要）

## 21. Vue 事件修饰符有哪些，实现原理是什么

.stop .native .capture .enter .self .prevent .once

有些是编译时「ast」标记

- .capture => !fn
- .once => ~fn
- .passive => &fn

有些是时间帮顶的时候处理的

- .prevent => \`$event.preventDefault\`

## 22. Vue.directive 的实现

- 指令的定义保存到 Vue.options上

## 23. 如何理解自定义指令

1. 在生成 ast 语法树，遇到指令会给当前元素添加 directives 属性
2. 通过 genDirectives 生成指令代码
3. 在 patch 前将指令的钩子提取到 cbs 中，在patch过程中调用对应的钩子
4. 当执行指令对应钩子函数时，调用对应指令定义的方法

等于预先定义钩子函数，然后渲染或者更新dom的时候调用这些钩子并且传值

## 24. vuex 的个人理解

vuex 是专门为vue提供的全局状态管理系统，用于多个组件中数据共享，数据缓存等

内部通过创造一个 全局实例 new Vue

## 25. Vue 中的slot是如何实现的？什么时候用它

- 普通插槽

  模板传入组件中，数据采用父组件数据

  父组件直接渲染出节点

  渲染时机：在父组件中渲染后替换

- 作用域插槽

  在父组件中访问到子组件的数据

  渲染时机：父组件渲染出一个函数，在子组件调用的这个函数渲染，然后替换父组件的位置

## 26. keep-alive的原理

缓存，LRU算法，最近最久未使用法

缓存的是 vnode

缓存之后，切换组件的时候，就不需要$mount （render）直接patch

## 27.vue中使用了哪些设计模式

1. 工厂模式

   createElement 根据不同的参数创建不同的实例

2. 单例模式

   vue-router   vuex

3. 发布订阅

   $emit / $on

4. 观察者

   watcher / dep

5. 代理模式

   data代理到 vm上

6. 装饰模式 装饰器

7. 中介者模式 => vuex

8. 策略模式

   根据不同的属性使用不同的策略

## 28.Vue3 和 Vue2 区别

Vue3 好处

1. typescript 的支持友好，基于函数
2. 不再大量 api 挂载在 Vue对象的原型上，难以实现 TreeShaking
3. 架构层面对跨平台支持更好（自定义渲染器）
4. composition api
5. 虚拟dom重写，模板编译的优化（比如静态节点标记）