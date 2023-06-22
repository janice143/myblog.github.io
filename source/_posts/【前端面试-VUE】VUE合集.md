---
title: 【前端面试-VUE】VUE合集
date: 2022-05-20 10:30:30
tags: vuejs
categories: vue
toc: true
---



## vuex

```markdown
### 谈谈vuex原理？
1 执行Vue.use。会执行vuex的install方法，会往全局混入一个mixin，只有一个属性beforeCreate，它的作用是让每个组件可以访问到this.$store属性。

2 执行new Vuex.Store。将传入的配置进行格式化处理，会递归注册每个module的state、mutations、actions、getters属性，将每个module的mutations、actions、getters放入一个对象里，对应的key前面会加上模块名，而state会放入一个有上下级关系的对象里。

3 内部会重写commit和dispatch，当前模块触发状态变更时，自动在要触发的commit和dispatch前面加上模块名。

4 最后会提供一些map开头的语法糖使用。




### vuex是什么？怎么使用？哪种功能场景使用它？
1 状态管理模式。可以看成一个数据仓库，集中管理项目公共数据的，所有组件都可以访问并更改。（统一管理，数据共享，程序可维护性高）

2 具有state、mutations、actions、getters、module属性。
state 有数据； 
mutations 唯一能更改数据的地方，有点像vue实例的方法；(只能写同步的方法，异步操作写字actions上)
actions 提交mutations，不能直接修改state;
getters 计算属性;
moudle 小仓库，store分割成模块，每个模块拥有自己的 state、mutations、actions、getters、甚至是嵌套子模块

3 单向数据流

4 大型项目中比较常用，非关系组件传递数据比较方便

5 使用
5.1 Vue.use 使用vuex插件
5.2 new Vuex.Store 创建一个store 实例：里面有state，mutations,actions，getters属性。state初始化数据，mutations、actions和getters可以写方法，actions需要提交mutations才能改变state，getters是计算属性，用来简化数据

5.3 在Vue 组件中， 可以通过 this.$store 访问store实例，
mutations的方法可以通过`store.commit`调用，触发状态变更。
actions  的方法可以通过 `store.dispatch` 方法触发，如果要改变数据的话，需要再提交mutations
getters  的方法直接通过`store.getters`调用

5.4 使用mapState、mapMutations、mapActions、mapGetters等辅助函数可以一次性获取仓库的数据或者属性等
```

<img src="/myblog.github.io/images/vue_vuex.png" alt="在这里插入图片描述" style="zoom: 80%;" />

```markdown
### 双向绑定和 vuex 是否冲突
1 问题出现的场景：

<input v-model="obj.message">

这里的 obj 是在计算属性中返回的一个属于 Vuex store 的对象，在用户输入时，v-model 会试图直接修改 obj.message。在严格模式中，由于这个修改不是在 mutation 函数中执行的, 这里会抛出一个错误。

所以确实会有冲突

2 解决方案
2.1 在input中绑定value(vuex中的state)，然后监听input的change或者input事件，在事件回调中调用mutation修改state的值
<input :value="message" @input="updateMessage">

// ...
computed: {
  ...mapState({
    message: state => state.obj.message
  })
},
methods: {
  updateMessage (e) {
    this.$store.commit('updateMessage', e.target.value)
  }
}


2.2 使用带有setter的双向绑定计算属性。
<input v-model="message">

// ...
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}


### 聊聊Vuex 的设计思想

把组件的共享状态抽取出来，以一个全局单例模式管理。在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！

通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。

### 为什么 Vuex 的 mutation 不能做异步操作
1 设计理念。人为规定不能在mutation中使用异步，强行建议做同步操作，将它作为一个无副作用的函数，使得状态可预测的，方便维护。将有副作用的函数放在action中，同步修改放在mutation中

2 异步操作的副作用。vuex中在mutation中使用异步，程序也不会报错，所以对结果是没有影响的。但是异步操作是成功还是失败不可预测，什么时候进行异步操作也不可预测；当异步操作成功或失败时，如果不 commit(mutation) 或者 dispatch(action)，Vuex 就不能捕获到异步的结果，从而进行相应的操作

### vuex持久化存储
1 不能持久化存储，页面一刷新或者关闭就没了
2 解决方案：
2.1 localStorage
2.2 使用插件，比如vue-persist（本质上也是localStorage)



```



## MVVM

```markdown
### 对于MVVM的理解
一个框架，一个理念

1 为什么有这个框架
前端的历史，不从web1.0，web2.0讲起，而是将MVC框架，因为MVVM本质上是MVC的改进版

1.1 MVC
Model View Controller，即模型-视图-控制器。用户访问操作网页会请求服务端路由，路由会调用对应的控制器来处理,控制器会获取数据。将结果返回给前端,页面重新渲染。

1.2 MVC特点
特点：单向通信；View和Model直接进行通信

1.3 痛点
随着前端应用的复杂程度越来越复杂，View和Model之间随着业务量的不断庞大，那么MVC就暴露出了三个痛点问题：

◆ 开发者在代码中大量调用相同的 DOM API，处理繁琐 ，操作冗余，使得代码难以维护。
◆ 大量的DOM 操作使页面渲染性能降低，加载速度变慢，影响用户体验。
◆ 当 Model 频繁发生变化，开发者需要主动更新到View ；当用户的操作导致 Model 发生变化，开发者同样需要将变化的数据同步到Model 中，这样的工作不仅繁琐，而且很难维护复杂多变的数据状态。

所以MVVM就出现了，并且可以解决上述问题。

2 MVVM这个框架是什么

MVVM 是 Model-View-ViewModel 的缩写。
Model: 代表数据模型，数据层
View: 视图层，用户操作界面。
ViewModel：业务逻辑层。
View通过View-Model的DOM Listeners将事件绑定到Model上，而Model则通过Data Bindings来管理View中的数据，View-Model从中起到一个连接桥的作用。

MVVM框架与MVC框架的主要区别有两点：
◆ 实现数据与视图的分离
◆ 数据双向绑定。
通过数据来驱动视图，开发者只需要关心数据变化，DOM操作被封装了。


总结： MVVM模式简化了界面与业务的依赖，解决了数据频繁更新。MVVM 在使用当中，利用双向绑定技术，使得 Model 变化时，ViewModel 会自动更新，而 ViewModel 变化时，View 也会自动变化。






### 为什么使用前端框架?

1 组件化: 高度的组件化可以是我们的工程易于维护、易于组合拓展。
2 天然分层: JQuery 时代的代码大部分情况下是面条代码,耦合严重,现代框架不管是 MVC、MVP还是MVVM 模式都能帮助我们进行分层，代码解耦更易于读写。
3 生态: 现在主流前端框架都自带生态,不管是数据流管理架构还是 UI 库都有成熟的解决方案





```

```markdown
### MVVM-脏数据检测



### 当新入职一家公司时，如何快速搭建开发环境并让应用跑起来


```

## MVVM-数据劫持

```markdown
### Object.defineProperty

`语法` Object.defineProperty(obj,prop,descriptor)

`参数`
    obj:目标对象
    prop:需要定义的属性或方法的名称
    descriptor:目标属性所拥有的特性
`可供定义的特性列表`

    value:属性的值
    writable:如果为false，属性的值就不能被重写。
    get: 一旦目标属性被访问就会调回此方法，并将此方法的运算结果返回用户。
    set:一旦目标属性被赋值，就会调回此方法。
    configurable:如果为false，则任何尝试删除目标属性或修改属性性以下特性（writable, configurable, enumerable）的行为将被无效化。
    enumerable:是否能在for...in循环中遍历出来或在Object.keys中列举出来。


-----------------------------------------------------------------------------------------------------------
下面这段代码演示了object.defineProperty的使用，可以发现当我们访问或者修改对象的属性的时候，可以触发getter和setter函数
--------------------------------------------------------------------------------------------------------

var data = {
    name:'lhl'
}

Object.keys(data).forEach(function(key){
    Object.defineProperty(data,key,{
        enumerable:true,
        configurable:true,
        get:function(){
            console.log('get');
        },
        set:function(){
            console.log('监听到数据发生了变化');
        }
    })
})

data.name //控制台会打印出 “get”
data.name = 'hxx' //控制台会打印出 "监听到数据发生了变化"


从上可以发现，当我们访问(get)或设置(set)对象的属性的时候，都会触发相对应的函数，然后在这个函数里返回或设置属性的值。既然如此，我们当然可以在触发函数的时候动一些手脚做点我们自己想做的事情，这也就是“劫持”操作。

### 总结

Vue通过Object.defineProperty来劫持对象属性的setter和getter操作，每次访问(get)或设置(set)对象的属性的时候，可以做一些具体的实现自己目的的操作。vue进行数据劫持主要是实现数据双向绑定，所以在劫持到setter和getter的时候，可以“种下”一个监听器，当数据发生变化的时候发出通知。这样就可以实现。

```

## 数据双向绑定原理

```markdown
### 总结
数据双向绑定的原理离不开数据劫持，Vue通过Object.defineProperty来劫持对象属性的setter和getter操作，每次访问(get)或设置(set)对象的属性的时候，可以做一些具体的实现自己目的的操作。要实现数据双向绑定，就要在劫持到setter和getter的时候，“种下”一个监听器，当数据发生变化的时候发出通知。这样就可以实现。

-----------------------------------------------------------------------------------------------------------
object.defineProperty实现数据劫持的可行性
--------------------------------------------------------------------------------------------------------


var data = { name: 'yck' }
observe(data)
let name = data.name // -> get value
data.name = 'yyy' // -> change value

function observe(obj) {
  // 判断类型
  if (!obj || typeof obj !== 'object') {
    return
  }
  Object.keys(obj).forEach(key => {
    defineReactive(obj, key, obj[key])
  })
}

function defineReactive(obj, key, val) {
  // 递归子属性
  observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      console.log('get value')
      return val
    },
    set: function reactiveSetter(newVal) {
      console.log('change value')
      val = newVal
    }
  })
}
注*：observe方法观测对象和数组，给其添加一个observel实例

### 添加发布订阅
上面代码只是展示了数据劫持的可行性，并没有真正实行，要实现数据双向绑定，还得做一些初始化工作，给model的属性添加发布订阅


// 通过 Dep 解耦
class Dep {
  constructor() {
    this.subs = []
  }
  // 订阅，标记要更新的数据
  addSub(sub) {
    // sub 是 Watcher 实例
    this.subs.push(sub)
  }
  // 通知（发布），更新数据
  notify() {
    this.subs.forEach(sub => {
      sub.update()
    })
  }
}
// 全局属性，通过该属性配置 Watcher
Dep.target = null

function update(value) {
  document.querySelector('div').innerText = value
}

class Watcher {
  constructor(obj, key, cb) {
    // 将 Dep.target 指向自己
    // 然后触发属性的 getter 添加监听
    // 最后将 Dep.target 置空
    Dep.target = this
    this.cb = cb
    this.obj = obj
    this.key = key
    this.value = obj[key]
    Dep.target = null
  }
  update() {
    // 获得新值
    this.value = this.obj[this.key]
    // 调用 update 方法更新 Dom
    this.cb(this.value)
  }
}

### 真正实现数据劫持
function defineReactive(obj, key, val) {
  // 递归子属性
  observe(val)
  let dp = new Dep()
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      console.log('get value')
      // 将 Watcher 添加到订阅
      if (Dep.target) {
        dp.addSub(Dep.target)
      }
      return val
    },
    set: function reactiveSetter(newVal) {
      console.log('change value')
      val = newVal
      // 执行 watcher 的 update 方法
      dp.notify()
    }
  })
}

```

## 数据双向绑定问题

```markdown
### Proxy 相比于 defineProperty 的优势

1 Object.defineProperty 缺陷

◆ 只能对属性进行数据劫持，所以需要深度遍历整个对象
◆ 对于数组不能监听到数据的变化

const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)
// hack 以下几个函数
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
methodsToPatch.forEach(function (method) {
  // 获得原生函数
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator (...args) {
    // 调用原生函数
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // 触发更新
    ob.dep.notify()
    return result
  })
})


2 Proxy
◆ 原生支持监听数组变化，
◆ 并且可以直接对整个对象进行拦截（不只是属性）

let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver);
    },
    set(target, property, value, receiver) {
      setBind(value);
      return Reflect.set(target, property, value);
    }
  };
  return new Proxy(obj, handler);
};

let obj = { a: 1 }
let value
let p = onWatch(obj, (v) => {
  value = v
}, (target, property) => {
  console.log(`Get '${property}' = ${target[property]}`);
})
p.a = 2 // bind `value` to `2`
p.a // -> Get 'a' = 2


### v-model原理
v:model 在模板编译的时候转换代码
v-model 本质是 :value 和 v-on，但是略微有点区别。在输入控件下，有两个事件监听，输入中文时只有当输出中文才触发数据赋值
v-model 和:bind 同时使用，前者优先级更高，如果 :value 会出现冲突
v-model 因为语法糖的原因，还可以用于父子通信


```



## vue源码

```markdown
### diff算法

请说下VUE diff算法
1 虚拟节点vnode
vnode是描述了节点的对象，vnode提供一种简单对象去代替复杂的 dom 对象，从而优化 dom ,不用不断操作DOM，因为这样很耗性能。

2 diff算法
diff算法去比较新旧vnode的差异，将变化的地方更新到真实的dom上。

3 总结
框架会将所有的结点先转化为虚拟节点Vnode，在发生更改后将VNode和原本页面的OldNode进行对比，然后以VNode为基准，在oldNode上进行准确的修改。（修改准则：原本没有新版有，则增加；原本有新版没有，则删除；都有则进行比较，都为文本结点则替换值；都为静态资源不处理；都为正常结点则替换）

### 介绍下虚拟 DOM，对虚拟 DOM 的理解

1 Virtual DOM

Virtual DOM(虚拟DOM)，是由普通的JS对象来描述DOM对象。真实DOM成员，先打印出所有的DOM成员

--------------------------------------------------------------------------------------------------------
let element = document.querySelector('#app')
let s = ''
for(let key in element){
	s += key + ','
}
console.log(s)

一个DOM的成员是非常多的，创建一个真实DOM成本非常高

--------------------------------------------------------------------------------------------------------
虚拟DOM

{
  sel:'div',
  data:{},
  children:undefined,
  text:'hello world',
  elm:undefined,
  key:undefined
}


创建成本比真实DOM低很多





### 组件

在 Vue 中，子组件为何不可以修改父组件传递的 Prop，如果修改了，Vue 是如何监控到属性的修改并给出警告的

### 响应式
Vue 响应式原理

### 原理
vue 中 computed 的原理是什么

vue-loader 的实现原理是什么

### key
react 与 vue 数组中 key 的作用是什么
在 react/vue 中数组是否可以以在数组中的次序为 key
在虚拟 DOM 中进行 diff 算法时，介绍当根据 key 对数组进行重用时的算法


```

## 组件通信

```markdown
组件之间通讯分为三种: 父传子、子传父、兄弟组件之间的通讯

### 父子组件间 
1 props 和$emit
这种方式通常用于父子组件之间的传值，父组件通过属性的方式将值传递给子组件，子组件通过props进行接收。子组件通过自定义事件的方式向父组件传递数据。

2 $parent获取父组件值
这种方式可以让子组件非常方便的获取父组件的值，不仅仅包括数据，还可以是方法。

3 $children和$refs获取子组件值
这两种方式和$parent非常的类似，它们可以直接获取子组件的相关属性或方法，不仅限于数据。

### 兄弟组件
1 全局事件总线 
this.$bus.$emit
this.$bus.$on
事件中心的思想也比较简单：中间中心主要就两个作用：触发事件和监听事件。假如两个组件之间需要传递数据，组件A可以触发事件中心的事件，组件B监听事件中心的事件，从而让两个组件之间产生关联，实现数据传递。

2 Vuex

Vuex是状态管理器，它存储的数据不是持久化存储，一旦刷新页面或者关闭项目数据便不见了。
Vuex存储的数据是响应式的。

3 localstorage

loacalStorage是HTML5中的一种数据存储方式，持久化存储，存储的数据不是响应式的。

### 总结
Vue中组件通讯的方式有很多种，每一种应用的场景可能都有一些不一样，我们需要在合适的场景下选择合适的通讯方式。

父子组件间通讯：props和emit、emit、emit、parent、refs和refs和refs和children、v-model
兄弟组件间通讯：事件总线、Vuex、localStorage
隔代组件间通讯：provide和inject
无相关组件间通讯：事件总线、Vuex、localStorage


```

## 路由

```markdown
### Vue的路由实现：hash模式 和 history模式
1 hash模式: 在浏览器的url中有符号“#”

2 history 模式下，前端的 URL 必须和实际向后端发起请求的 URL 一致，如 http://www.xxx.com/items/id。后端如果缺少对 /items/id 的路由处理，将返回 404 错误。


### vue-router 有哪几种导航守卫?
1 全局守卫
router.beforeEach 全局前置守卫 进入路由之前
router.beforeResolve 全局解析守卫
router.afterEach 全局后置钩子 进入路由之后

 // main.js 入口文件
    import router from './router'; // 引入路由
    router.beforeEach((to, from, next) => { 
      next();
    });
    router.beforeResolve((to, from, next) => {
      next();
    });
    router.afterEach((to, from) => {
      console.log('afterEach 全局后置钩子');
    });

2 路由独享守卫

const router = new VueRouter({
      routes: [
        {
          path: '/foo',
          component: Foo,
          beforeEnter: (to, from, next) => { 
            // 参数用法什么的都一样,调用顺序在全局前置守卫后面，所以不会被全局守卫覆盖
            // ...
          }
        }
      ]
    })

3 路由组件内的守卫（用得较少）

beforeRouteEnter 进入路由前, 在路由独享守卫后调用 不能 获取组件实例 this，组件实例还没被创建
beforeRouteUpdate (2.2) 路由复用同一个组件时, 在当前路由改变，但是该组件被复用时调用 可以访问组件实例 this
beforeRouteLeave 离开当前路由时, 导航离开该组件的对应路由时调用，可以访问组件实例 this


### 路由鉴权
登录页和其他页面分开，登录以后实例化 Vue 并且初始化需要的路由
动态路由，通过 addRoute 实现
简述路由原理

### React/Vue 中的 router 实现原理如何


### vue-router 实现懒加载的方法？
1 vue-router 实现懒加载的作用：性能优化，不用到该路由，不加载该组件
2 ES6的impot方式: component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  VUE中的异步组件进行懒加载方式: component: resolve=>(require(['../views/About'],resolve))
```

## 生命周期

```markdown
### 请详细说下你对vue生命周期的理解
1 beforeCreate 
data和methods中的数据或方法还未初始化，无法调用，只能使用一些默认事件。

2 created
data和methods已经初始化完成了，但是模板还没有编译，也就是我们还不能获取到DOM

3 beforeMount
模板进行编译，会调用render函数生成vDom，也就是虚拟DOM，此时我们同样无法获取DOM节点。无法获取DOM节点的，因为此时只存在VDOM，还在JS级别

4 mounted
模板编译好了，而且挂载到真实DOM树上面去了，也就是我们的页面可以显示了。

5 beforeUpdate
页面或者组件发生变化时，便会执行对应的更新阶段的钩子函数。

6 updated
页面也渲染更新完成了，显示的就是最新的数据

7 beforeDestory
此时组件实际上还没有被销毁，还可以正常使用。我们通常会在这个钩子函数里面解除一些全局或者自定义事件

8 destoryed
此时所有的组件包括子组件都被销毁了。

9 补充
通常情况下我们切换路由等一些操作时，组件都会被销毁，切换回来的时候组件又重新渲染。但是有时候我们为了提高性能，我们可以在切换路由的时候不必销毁组件，这个时候我们就需要用到Vue的一个内置组件keep-alive。
activated
页面渲染的时候执行

deactivated
页面被隐藏或者页面即将被替换成新的页面时被执行

### Vue 的父组件和子组件生命周期钩子执行顺序是什么

父组件和子组件钩子执行顺序
加载渲染过程：父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted
父组件挂载完毕肯定是等里面的子组件都挂载完毕后才算父组件挂载完毕了，所以父组件的mounted在最后。
子组件更新过程(子组件更新影响到父组件的情况)：父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
子组件更新过程(子组件更新不影响父组件的情况)：子beforeUpdate -> 子updated
父组件更新过程(父组件影响子组件的情况)：父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
父组件更新过程(父组件不影响子组件的情况)：父beforeUpdate -> 父updated
销毁过程：父beforeDestroy->子beforeDestroy->子destroyed->父destroyed


```

<img src="/myblog.github.io/images/vue_lifeCycle.png" alt="Vue 实例生命周期" style="zoom: 50%;" />

## 性能优化

```markdown
### vue 渲染大量数据时应该怎么优化
数据量大的时候，可以做分页处理。翻页一次请求10-20条数据
按需加载局部数据, 虚拟列表，无限下拉刷新
js运行异步处理:
分割任务，实现时间切片处理, 类似react fiber, 每次执行记录时间, 超过一定执行时间则settimeout或requestAnimation推迟到下一个时间片,一般一个时间片为16ms
大量纯展示的数据,不需要追踪变化的 用object.freeze冻结


### vue 如何优化首页的加载速度？vue 首页白屏是什么问题引起的？如何解决呢？
1 回答1
首页白屏的原因：
单页面应用的 html 是靠 js 生成，因为首屏需要加载很大的js文件(app.js vendor.js)，所以当网速差的时候会产生一定程度的白屏

**解决办法**
优化 webpack 减少模块打包体积，code-split 按需加载
服务端渲染，在服务端事先拼装好首页所需的 html
首页加 loading 或 骨架屏 （仅仅是优化体验）

2 回答2
VUE首页加载过慢，其原因是因为它是一个单页应用，需要将所有需要的资源都下载到浏览器端并解析。

**考虑解决办法**
1.使用首屏SSR + 跳转SPA方式来优化
2.改单页应用为多页应用，需要修改webpack的entry
3.改成多页以后使用应该使用prefetch的就使用
4.处理加载的时间片，合理安排加载顺序，尽量不要有大面积空隙
5.CDN资源还是很重要的，最好分开，也能减少一些不必要的资源损耗
6.使用Quicklink，在网速好的时候 可以帮助你预加载页面资源
7.骨架屏这种的用户体验的东西一定要上，最好借助stream先将这部分输出给浏览器解析
8.合理使用web worker优化一些计算
9.缓存一定要使用，但是请注意合理使用
10.大概就这么多，最后可以借助一些工具进行性能评测，重点调优，例如使用performance自己实现下等
```

##  手写源码

```markdown
### 如何使用 vue 实现一个 message API

### 写一个发布订阅 EventEmitter方法简单

```

## 其他

```markdown
### vue 在 v-for 时给每项元素绑定事件需要用事件代理吗？为什么？
1 事件代理
需要对很多元素添加事件的时候，可以通过将事件添加到它们的父节点而将事件委托给父节点来触发处理函数

2 作用
将事件处理程序代理到父节点，减少内存占用率
动态生成子节点时能自动绑定事件处理程序到父节点

### watch 和 computed 的区别和运用的场景
1 computed
计算属性，值有缓存，只有当计算值变化才变化触发渲染;
computer 就是简单计算一下，适用于渲染页面

2 watch
后者监听到值得变化就会执行回调;
watch 适合做一些复杂业务逻辑



### Vue 中 $nextTick 作用与原理？
下次 DOM 更新循环结束之后执行延迟回调，用于获得更新后的 DOM。
也支持 Promise 的使用，会判断是否实现了 Promise
Promise
MutationObserver
setImmediate
setTimeout


### vue 中 v-if 和 v-show 的区别是什么
v-show 总是会进行编译和渲染的工作 - 它只是简单的在元素上添加了 display: none; 的样式。
v-show 具有较高的初始化性能成本上的消耗，但是使得转换状态变得很容易。 

v-if 才是真正「有条件」的：它的加载是惰性的，因此，若它的初始条件是 false，它就不会做任何事情。这对于初始加载时间来说是有益的，当条件为 true 时，v-if 才会编译并渲染其内容。
切换 v-if 下的块儿内容实际上时销毁了其内部的所有元素，比如说处于 v-if 下的组件实际上在切换状态时会被销毁并重新生成，因此，切换一个较大 v-if 块儿时会比 v-show 消耗的性能多。



 
```





