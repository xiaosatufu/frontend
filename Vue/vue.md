---
typora-root-url: ..
---

# Vue

[TOC]

## 对vue生命周期的理解

> vue生命周期总共分为8个阶段:创建前/后 载入前/后 更新前/后 销毁前/后

- beforeCreate 创建前,vue实例的挂载元素$el和数据对象data都是undefined 还未初始化
- created 创建后,完成了data数据的初始化,el还未初始化
- beforeMount 载入前,ue实例的`$el`和`data`都初始化了, 相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。注意此时还没有挂载html到页面上。
- mounted 载入后,在`el` 被新创建的 `vm.$el`替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的`html`内容替换`el`属性指向的`DOM`对象。完成模板中的html渲染到html页面中。此过程中进行ajax交互
- beforeUpdate 更新前,在数据更新之前调用，发生在虚拟DOM重新渲染和打补丁之前。可以在该钩子中进一步地更改状态，不会触发附加的重渲染过程。
- updated 更新后 ,在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。调用时，组件DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
- beforeDestroy 销毁前,在实例销毁之前调用。实例仍然完全可用。
- destroyed 销毁后,在实例销毁之后调用。调用后，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。

## 常用开发指令

- v-model :一般用于表达输入,很轻松的实现表单控件的数据的双向绑定
- v-html:更新原色的innerHTML
- v-show与v-if:条件渲染
- v-on:click :可以简写为@click,@绑定一个事件,如果事件触发了,就可以指定事件的处理函数.
- v-for:基于源数据多次渲染元素或模块
- v-bind:当表达式的值改变时,将其产生的连带影响,响应式的作用于DOM

> v-bind:title="msg"  -> :title="msg"

## vue双向绑定的原理

> vue.js 采用数据劫持结合发布订阅者模式,通过`Object.defineProperty()`来劫持各个属性的setter,getter,在数据变动时发布消息给订阅者,触发相应的监听回调.

## Proxy相比于definePropetry的优势

Object.defineProperty()的问题主要有三个:

- 不能监听数组的变化
- 必须遍历对象的每个属性
- 必须深层遍历嵌套的对象

Proxy在ES2015规范中被正式加入,它有以下几个特点:

- 针对对象:针对整个对象,而不是对象的某个属性,所以也就不需要对keys进行遍历.这解决了上述Object.defineProperty()的第二个问题
- 支持数组:Proxt不需要对数组的方法进行重载
- Proxy的第二个参数有13种拦截方法,这比起Object.defineProperty()要更加丰富

## 组件之间的传值通信方式

### 父组件给子组件传值

使用props,父组件可以使用props向子组件传递数据

```vue
//father.vue
<template>
	<child :msg="message" />
</template>
<script>
	import child from "./child.vue"
    export default {
        components:{
            child
        },
        data(){
            return {
				message:'father message'
            }
        }
    }
</script>
//child.vue
<template>
<div>{{message}}</div>
</template>
<script>
    export default {
        props:{
			message:{
                type:String,
                required:true
            }
        }
    }
</script>

```

### 子组件向父组件通信

父组件向子组件传递事件方法,子组件通过$emit触发事件,回调给父组件

```vue
//father.vue
<template>
    <child @msgFunc="func"></child>
</template>

<script>
import child from './child.vue';
export default {
    components: {
        child
    },
    methods: {
        func (msg) {
            console.log(msg);
        }
    }
}
</script>
//child.vue
<template>
    <button @click="handleClick">点我</button>
</template>

<script>
export default {
    props: {
        msg: {
            type: String,
            required: true
        }
    },
    methods () {
        handleClick () {
            //........
            this.$emit('msgFunc');
        }
    }
}
</script>

```

### 非父子,兄弟组件之间的通信

```js
//Bus.js
import Vue from 'vue'
export default new Vue()
```



在需要通信的组件都引入Bus.js

```vue
<template>
	<button @click="toBus">
    	子组件传给兄弟组件
    </button>
</template>
<script>
    import Bus from '../common/js/bus.js'
    export default {
		methods:{
			toBus() {
                Bus.$emit('on','来自兄弟组件')
            }
        }
    }
</script>
```

另一个组件也import Bus.js 在钩子函数中监听on事件

```js
import Bus from '../common/js/bus.js'
export default {
    data() {
      return {
        message: ''
      }
    },
    mounted() {
       Bus.$on('on', (msg) => {
         this.message = msg
       })
     }
   }

```

## vue-router

### 导航守卫

#### 全局前置守卫

可以使用router.beforeEach()注册一个全局前置守卫:

```js
const router = new VueRouter({...})
router.beforeEach((to,from,next)=>{
	//...
})
```

确保调用next方法,否则钩子就不会被resolved

#### 全局解析守卫

在 2.5.0+ 你可以用 `router.beforeResolve` 注册一个全局守卫。这和 `router.beforeEach` 类似，区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用。

#### 全局后置钩子

你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

```js
router.afterEach((to, from) => {
  // ...
})
```

  

#### 路由独享守卫

在路由配置直接定义beforeEnter守卫:

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```



#### 组件内的守卫

可以在路由组件内直接定义以下路由导航守卫：

- beforeRouteEnter
- beforeRouteLeave
- beforeRouteUpdate(2.2新增)

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

`beforeRouteEnter` 守卫 **不能** 访问 `this`，因为守卫在导航确认前被调用,因此即将登场的新组件还没被创建。

不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以**不支持**传递回调，因为没有必要了。



```js
beforeRouteUpdate (to, from, next) {
  // just use `this`
  this.name = to.params.name
  next()
}
```

  这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。



```js
beforeRouteLeave (to, from , next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

  

## vuex

1. vuex就是一个仓库,仓库里放了很多对象.其中state就是数据源存放地,对应于一般vue对象里面的data
2. state里面存放的数据时响应式的,vue组件从store读取数据,若是store中的数据发生改变,依赖这项数据的组件也会发生更新
3. 它通过mapState把全局的state和getters映射到当前组件的computed计算属性

![](/Vue/微信图片_20190626143637.png)

Vuex有5中属性:state getter mutation action module

### state

Vuex使用单一状态树,即每个应用将仅仅包含一个store实例,但单一状态树和模块化并不冲突.存放的数据状态,不可以直接修改里面的数据

### mutations

mutations定义的方法动态修改Vuex的store中的状态或数据.

### getters

类似vue的计算属性,主要用来过滤一些数据

### actions

actions可以理解为通过将mutations里面处里数据的方法变成可异步的处理数据的方法，简单的说就是异步操作数据。view 层通过 store.dispath 来分发 action。

### module







