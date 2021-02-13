# data
## data
Type: Function
Details: 

Example: 
```js
// direct instance creation
const data = { a: 1 }

// The object is added to a component instance
const vm = Vue.createApp({
  data() {
    return data
  }
}).mount('#app')

console.log(vm.a) // => 1
```
See Also:

## props
Type: Array<string> | Object
Details: 
基于对象的语法，可以使用以下属性：
1、type： 
2、default：默认值必须来自一个工厂函数
3、required：
4、validator：
Example: 
```js
const app = Vue.createApp({})

// simple syntax
app.component('props-demo-simple', {
  props: ['size', 'myMessage']
})

// object syntax with validation
app.component('props-demo-advanced', {
  props: {
    // type check
    height: Number,
    // type check plus other validations
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: value => {
        return value >= 0
      }
    }
  }
})
```
See Also:


## computed
Type: { [key: string]: Function | { get: Function, set: Function } }
Details: 
在计算属性中，this不指向组件实例，但可以通过第一个参数访问
如果计算属性依赖的属性不在实例的作用域内，则计算属性不会更新

Example: 
```js
const app = Vue.createApp({
  data() {
    return { a: 1 }
  },
  computed: {
    // get only
    aDouble() {
      return this.a * 2
    },
    // both get and set
    aPlus: {
      get() {
        return this.a + 1
      },
      set(v) {
        this.a = v - 1
      }
    }
  }
})

const vm = app.mount('#app')
console.log(vm.aPlus) // => 2
vm.aPlus = 3
console.log(vm.a) // => 2
console.log(vm.aDouble) // => 4
```
See Also:


## methods
Type: { [key: string]: Function }
See Also:


## watch
Type: { [key: string]: string | Function | Object | Array}
Details: 
1、值可以是methods中的方法
2、
Example: 
```js
const app = Vue.createApp({
  data() {
    return {
      a: 1,
      b: 2,
      c: {
        d: 4
      },
      e: 'test',
      f: 5
    }
  },
  watch: {
    a(val, oldVal) {
      console.log(`new: ${val}, old: ${oldVal}`)
    },
    // string method name
    b: 'someMethod',
    // the callback will be called whenever any of the watched object properties change regardless of their nested depth
    c: {
      handler(val, oldVal) {
        console.log('c changed')
      },
      deep: true
    },
    // the callback will be called immediately after the start of the observation
    e: {
      handler(val, oldVal) {
        console.log('e changed')
      },
      immediate: true
    },
    // you can pass array of callbacks, they will be called one-by-one
    f: [
      'handle1',
      function handle2(val, oldVal) {
        console.log('handle2 triggered')
      },
      {
        handler: function handle3(val, oldVal) {
          console.log('handle3 triggered')
        }
        /* ... */
      }
    ]
  },
  methods: {
    someMethod() {
      console.log('b changed')
    },
    handle1() {
      console.log('handle 1 triggered')
    }
  }
})

const vm = app.mount('#app')

vm.a = 3 // => new: 3, old: 1
```
See Also:


## emits
Type: Array<string> | Object
Details: 

Example: 
```js
const app = Vue.createApp({})

// Array syntax
app.component('todo-item', {
  emits: ['check'],
  created() {
    this.$emit('check')
  }
})

// Object syntax
app.component('reply-form', {
  emits: {
    // no validation
    click: null,

    // with validation
    submit: payload => {
      if (payload.email && payload.password) {
        return true
      } else {
        console.warn(`Invalid submit event payload!`)
        return false
      }
    }
  }
})
```
See Also:



# DOM
## template
Type: string
Details: 
如果组件选项中出现了渲染函数，那么模板就会被忽略
See Also:


## render
Type: Function
Details: 
字符串模板的替代方案，可以使用js的所有变成能力

Example:
```html
<div id="app" class="demo">
  <my-title blog-title="A Perfect Vue"></my-title>
</div>
``` 
```js
const app = Vue.createApp({})

app.component('my-title', {
  render() {
    return Vue.h(
      'h1', // tag name,
      this.blogTitle // tag content
    )
  },
  props: {
    blogTitle: {
      type: String,
      required: true
    }
  }
})

app.mount('#app')
```
See Also:
注意事项：
The render function has priority over the render function compiled from template option 
or in-DOM HTML template of the mounting element


# Lifecycle Hooks
## beforeCreate
Type: Function
Details: 
在实例初始化完成后，data被observed、事件和watcher建立以后，同步地执行

See Also:

## created
Type: Function
Details: 
实例创建完成以后，同步地调用；
data observation, computed properties, methods, watch/event callbacks已经setup
挂载阶段还没有开始，$el属性还不能访问

See Also:

## beforeMount
Type: Function
Details: 
挂载开始时，render函数会在第一时间被调用

See Also:

## mounted
Type: Function
Details: 
实例完成挂载后，传递给Vue.createApp({}).mount()的元素会被刚创建vm.$el替代
注意事项：
mounted执行不代表所有的子组件都完成挂载
如果要保证所有的视图都被渲染，可以在mounted内使用使用vm.$nextTick
```js
mounted() {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
```
See Also:

## beforeUpdate
Type: Function
Details: 
data发生变化后，dom还没有patch
这是访问未更新dom节点的好时机
例如：手动添加事件监听

See Also:

## updated
Type: Function
Details: 
1、避免在这个阶段修改状态
2、想要完成状态变更，可以使用计算属性或watcher替代
注意事项：
在这个阶段可以使用vm.$nextTick，来保证变更的数据被重新渲染

See Also:

## activated
Type: Function
Details: 
keep-alive组件激活时调用，服务端渲染时不会调用

See Also:

## deactivated
Type: Function
Details: 
当keep-alive组件消除激活状态时调用

See Also:

## beforeUnmount
Type: Function
Details: 
卸载完成之前
在服务端渲染期间，这个钩子不会被调用

See Also:

## unmounted
Type: 
Details: 
在这个阶段，组件实例的所有指令会被解绑，事件监听会被删除，所有子组件实例会被卸载

See Also:

## errorCaptured
Type: (err: Error, instance: Component, info: string) => ?boolean
Details: 
当捕获后代组件中的错误时调用
err： 错误信息
instance：触发错误的实例
info： 关于出发错误的位置信息字符串
钩子可以返回false，阻止错误进一步
注意事项：
You can modify component state in this hook. However, 
it is important to have conditionals in your template 
or render function that short circuits other content when an error has been captured;
 otherwise the component will be thrown into an infinite render loop.
 
错误冒泡规则：
1、默认情况下，所有的错误都会发送道全局的config.errorHandler
2、如果errorCaptured组件的继承链或父链上存在多个钩子，则将在相同的错误上调用所有它们。
3、如果errorCaptured钩子自己抛出错误，那么两个错误都会被发送给config.errorHandler
4、
See Also:

## renderTracked
Type: (e: DebuggerEvent) => void
Details: 
Called when virtual DOM re-render is tracked. The hook receives a debugger event as an argument. 
This event tells you what operation tracked the component and the target object and key of that operation.
```html
<div id="app">
  <button v-on:click="addToCart">Add to cart</button>
  <p>Cart({{ cart }})</p>
</div>
```
```js
const app = Vue.createApp({
  data() {
    return {
      cart: 0
    }
  },
  renderTracked({ key, target, type }) {
    console.log({ key, target, type })
    /* This will be logged when component is rendered for the first time:
    {
      key: "cart",
      target: {
        cart: 0
      },
      type: "get"
    }
    */
  },
  methods: {
    addToCart() {
      this.cart += 1
    }
  }
})

app.mount('#app')
```

See Also:

## renderTriggered
Type: (e: DebuggerEvent) => void
Details: 
Called when virtual DOM re-render is triggered.Similarly to renderTracked, 
receives a debugger event as an argument. 
This event tells you what operation triggered the re-rendering 
and the target object and key of that operation.

Example: 
```html
<div id="app">
  <button v-on:click="addToCart">Add to cart</button>
  <p>Cart({{ cart }})</p>
</div>
```
```js
const app = Vue.createApp({
  data() {
    return {
      cart: 0
    }
  },
  renderTriggered({ key, target, type }) {
    console.log({ key, target, type })
  },
  methods: {
    addToCart() {
      this.cart += 1
      /* This will cause renderTriggered call
        {
          key: "cart",
          target: {
            cart: 1
          },
          type: "set"
        }
      */
    }
  }
})

app.mount('#app')
```

See Also:

# Assets
## directives
Type: Object
Details: 

Example: 
```js
const app = Vue.createApp({})

app.component('focused-input', {
  directives: {
    focus: {
      mounted(el) {
        el.focus()
      }
    }
  },
  template: `<input v-focus>`
})
```
See Also:

## components
Type: Object
Details: 
Example: 
```js
const Foo = {
  template: `<div>Foo</div>`
}

const app = Vue.createApp({
  components: {
    Foo
  },
  template: `<Foo />`
})
```
See Also:


# Composition
## mixins
Type: Array<Object>
```js
const mixin = {
  created: function() {
    console.log(1)
  }
}

Vue.createApp({
  created() {
    console.log(2)
  },
  mixins: [mixin]
})

// => 1
// => 2
```

## extends
Type: Object | Function
Details: 
Example: 
```js
const CompA = { ... }

// extend CompA without having to call `Vue.extend` on either
const CompB = {
  extends: CompA,
  ...
}
```
See Also:

## provide/inject
Type: 
provide: Object | () => Object
inject: Array<string> | { [key: string]: string | Symbol | Object }

Details: 
Example: 
```js
// parent component providing 'foo'
const Provider = {
  provide: {
    foo: 'bar'
  }
  // ...
}

// child component injecting 'foo'
const Child = {
  inject: ['foo'],
  created() {
    console.log(this.foo) // => "bar"
  }
  // ...
}
```
```js
const s = Symbol()

const Provider = {
  provide() {
    return {
      [s]: 'foo'
    }
  }
}

const Child = {
  inject: { s }
  // ...
}
```
```js
const Child = {
  inject: ['foo'],
  props: {
    bar: {
      default() {
        return this.foo
      }
    }
  }
}
```
```js
const Child = {
  inject: ['foo'],
  data() {
    return {
      bar: this.foo
    }
  }
}
```
```js
const Child = {
  inject: {
    foo: { default: 'foo' }
  }
}
```
```js
const Child = {
  inject: {
    foo: {
      from: 'bar',
      default: 'foo'
    }
  }
}
```
```js
const Child = {
  inject: {
    foo: {
      from: 'bar',
      default: () => [1, 2, 3]
    }
  }
}
```


See Also:

## setup
Type: Function
Details: 
Invocation Timing（触发时机）
组件实例创建完成后，在beforeCreate钩子执行之前
```html
<template>
  <div>{{ count }} {{ object.foo }}</div>
</template>

<script>
  import { ref, reactive } from 'vue'

  export default {
    setup() {
      const count = ref(0)
      const object = reactive({ foo: 'bar' })

      // expose to template
      return {
        count,
        object
      }
    }
  }
</script>
```
在模板中使用setup返回通过ref处理的数据，不需要通过value属性访问数据
```js
import { h, ref, reactive } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const object = reactive({ foo: 'bar' })

    return () => h('div', [count.value, object.foo])
  }
}
```
```js
export default {
  props: {
    name: String
  },
  setup(props) {
    console.log(props.name)
  }
}
```
```js
export default {
  props: {
    name: String
  },
  setup(props) {
    watchEffect(() => {
      console.log(`name is: ` + props.name)
    })
  }
}
```
在setup中使用watchEffect和watch，props的数据是响应式的
```js
export default {
  props: {
    name: String
  },
  setup({ name }) {
    watchEffect(() => {
      console.log(`name is: ` + name) // Will not be reactive!
    })
  }
}
```
在setup的参数中解构props，会让数据失去响应式
```js
const MyComponent = {
  setup(props, context) {
    context.attrs
    context.slots
    context.emit
  }
}
```
```js
const MyComponent = {
  setup(props, { attrs }) {
    // a function that may get called at a later stage
    function onClick() {
      console.log(attrs.foo) // guaranteed to be the latest reference
    }
  }
}
```
setup参数：1、props 2、context（分成两个参数的原因）
It's much more common for a component to use props than the other properties, and very often a component uses only props.

Having props as a separate argument makes it easier to type it individually without messing up the types of other properties on the context. 
It also makes it possible to keep a consistent signature across setup, render and plain functional components with TSX support.

Example: 
See Also:

#  Misc
## name
Type: string
Details: 
1、当使用Vue.createApp({}).component({})注册组件时，全局的id会自动的设置为组件的name属性
2、

Example: 
See Also:

## delimiters
Type: Array<string
default: ['{{', '}}']
Details: 
Example: 
See Also:

## inheritAttrs
Type: boolean
Details: 
Example: 
See Also:
