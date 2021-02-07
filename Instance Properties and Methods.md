# Instance Properties
## $data
Type: Object
Details: 
See Also: https://v3.vuejs.org/api/options-data.html#data-2

## $props
Type: Object
Details: 


## $el
Type: any
Read only
Details: 
1、当前组件实例管理的根元素
2、对于使用fragments的组件，$el是占位符DOM节点，用来记录组件在DOM中的位置
3、推荐使用模板的ref来访问DOM元素，而不是依赖$el

## $options
Type: Object
Read only
Details: 为当前组件实例提供的，对于在options添加本地属性是很有用的
```js
const app = new createApp({
    customOption: 'foo',
    created(){
        console.log(this.$options.customOption) // => 'foo'
    }
})
```

## $parent
Type: Component instance
Read only
Details: 父组件实例

## $root
Type: Component instance
Read only
Details: 当前组件树的根组件实例，如果当前组件实例没有父节点，那么就是其本身

## $slots
Type: { [name: string]: (...args: any[]) => Array<VNode> | undefined }
Read only
Details: 
1、以编程方式访问通过$slots分配的内容，每一个具名插槽都有自己相应的属性
2、例如：the contents of v-slot:foo will be found at this.$slots.foo()
3、default属性包含具名插槽中不包含的节点或v-slot:default内容
4、使用渲染函数写组件的时候，this.$slots是非常有用的

Example: 
```html
<blog-post>
  <template v-slot:header>
    <h1>About Me</h1>
  </template>

  <template v-slot:default>
    <p>
      Here's some page content, which will be included in $slots.default.
    </p>
  </template>

  <template v-slot:footer>
    <p>Copyright 2020 Evan You</p>
  </template>
</blog-post>
```
```js
const app = Vue.createApp({})

app.component('blog-post', {
  render() {
    return Vue.h('div', [
      Vue.h('header', this.$slots.header()),
      Vue.h('main', this.$slots.default()),
      Vue.h('footer', this.$slots.footer())
    ])
  }
})
```

## $refs
Type: Object
Read only
Details: 一个DOM元素或组件实例的对象通过ref属性注册
See Also:
1、template ref：https://v3.vuejs.org/guide/component-template-refs.html
2、Special attribute：https://v3.vuejs.org/api/special-attributes.html#ref


## $attrs
Type: Object
Read only
Details: 
1、包含父作用域属性绑定和作为本地props和事件的不被识别的事件
2、When a component doesn't have any declared props or custom events, this essentially contains all parent-scope bindings, 
and can be passed down to an inner component via v-bind="$attrs" - useful when creating higher-order components.

See Also:
1、Non-Prop Attribute: https://v3.vuejs.org/guide/component-attrs.html#attribute-inheritance
2、inherritAttrs: https://v3.vuejs.org/api/options-misc.html#inheritattrs

# Instance Methods
## $watch
Arguments: 
+   {string | Function} source
+   {Function | Object} callback
+   {Object} options (optional)
    +   {boolean} deep
    +   {boolean} immediate
    +   {string} flush

Returns: {Function} unwatch
Usage:  
1、监听组件实例上的响应式属性或计算方法的变化，使用给定属性的新值和原始值调用回调
2、只能传递顶层data，prop，computed属性作为字符串
3、对于复杂的表达式或嵌套属性用函数代替

案例：
```js
const app = Vue.createApp({
  data() {
    return {
      a: 1,
      b: 2,
      c: {
        d: 3,
        e: 4
      }
    }
  },
  created() {
    // top-level property name
    this.$watch('a', (newVal, oldVal) => {
      // do something
    })

    // function for watching a single nested property
    this.$watch(
      () => this.c.d,
      (newVal, oldVal) => {
        // do something
      }
    )

    // function for watching a complex expression
    this.$watch(
      // every time the expression `this.a + this.b` yields a different result,
      // the handler will be called. It's as if we were watching a computed
      // property without defining the computed property itself
      () => this.a + this.b,
      (newVal, oldVal) => {
        // do something
      }
    )
  }
})
```
4、当监听的值是一个对象或数组时，改变它的任何属性或元素都不会触发监听器，因为他们引用了相同的对象或数组
除非用一个全新的对象或数组完整替换

5、$watch返回一个unwatch函数，用于停止响应的监听
```js
const app = Vue.createApp({
  data() {
    return {
      a: 1
    }
  }
})

const vm = app.mount('#app')

const unwatch = vm.$watch('a', cb)
// later, teardown the watcher
unwatch()
```

6、监听嵌套属性
```js
vm.$watch('someObject', callback, {
  deep: true
})
vm.someObject.nestedValue = 123
// callback is fired
```
7、立即调用
```js
vm.$watch('a', callback, {
  immediate: true
})
// `callback` is fired immediately with current value of `a`
```
使用immediate: true后，将不能停止监视
```js
// This will cause an error
const unwatch = vm.$watch(
  'value',
  function() {
    doSomething()
    unwatch()
  },
  { immediate: true }
)
```
```js
let unwatch = null

unwatch = vm.$watch(
  'value',
  function() {
    doSomething()
    if (unwatch) {
      unwatch()
    }
  },
  { immediate: true }
)
```

7、Option：flush
flush选项允许对更好的控制回调的时机，可选值：'pre','post','sync'
默认值是pre，回调在渲染之前调用，允许回调在模板运行之前修改其他的值
值为post时，回调在渲染之后调用，使用场景：需要访问更新后的DOM或通过组件的$refs返回节点
值为sync时，在被监听的值变化的同时执行回调

当值为pre或post时，回调使用队列进行缓冲；
即时被监视的值修改多次，也只会被添加到队列中一次
缓冲回调不仅提升性能，而且保证了数据的一致性，在执行数据更新的代码完成之前，不会触发观察程序
'sync' watchers should be used sparingly, as they don't have these benefits.

See Also:
1、effect flush timing: https://v3.vuejs.org/guide/reactivity-computed-watchers.html#effect-flush-timing
2、waters: https://v3.vuejs.org/guide/computed.html#watchers

## $emit
Arguments: 
{string} eventName
...args (optional)

案例: 
```html
<div id="emit-example-argument">
  <advice-component v-on:advise="showAdvice"></advice-component>
</div>
```
```js
const app = Vue.createApp({
  methods: {
    showAdvice(advice) {
      alert(advice)
    }
  }
})

app.component('advice-component', {
  emits: ['advise'],
  data() {
    return {
      adviceText: 'Some advice'
    }
  },
  template: `
    <div>
      <input type="text" v-model="adviceText">
      <button v-on:click="$emit('advise', adviceText)">
        Click me for sending advice
      </button>
    </div>
  `
})

app.mount('#emit-example-argument')
```

## $forceUpdate
强制重新渲染组件实例，对子组件无影响
仅对当前组件实例和由slot组件的子组件有影响

## $nextTick
Arguments: {Function} callback (optional)
将回调延迟到下一次DOM更新完成后，
与全局nextTick不同的是，将回调的this上下文自动绑定到调用此方法的实例上
```js
Vue.createApp({
  // ...
  methods: {
    // ...
    example() {
      // modify data
      this.message = 'changed'
      // DOM is not updated yet
      this.$nextTick(function() {
        // DOM is now updated
        // `this` is bound to the current instance
        this.doSomethingElse()
      })
    }
  }
})
```

