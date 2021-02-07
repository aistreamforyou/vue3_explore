# Application Config
每一个组件实例都会暴露一个配置文件
```js
const app = Vue.createApp()
console.log(app.config)
```
在mount应用之前，可以修改config
## errorHandler
Type: Function
Default: undefined
Usage: 
```js
app.config.errorHandler = (err, vm, info) => {
  // handle error
  // `info` is a Vue-specific error info, e.g. which lifecycle hook
  // the error was found in
}
```
1、为组件渲染期间的未捕获错误和监视器指定处理程序
2、使用error和组件实例调用处理程序

错误跟踪服务Sentry和Bugsnag使用此选项提供官方集成（？？？）

## warnHandler
Type: Function
Default: undefined
Usage: 
```js
app.config.warnHandler = function(msg, vm, trace) {
  // `trace` is the component hierarchy trace
}
```
1、为运行时Vue警告指定处理程序
2、只在开发模式下起作用

## globalProperties
Type: [key: string]: any
Default: undefined
Usage: 
```js
app.config.globalProperties.foo = 'bar'

app.component('child-component', {
  mounted() {
    console.log(this.foo) // 'bar'
  }
})
```
1、应用内任何组件内都可以访问的全局属性
2、当全局属性与组件属性冲突时，组件属性优先级高于全局属性
3、全局属性可以用来替代vue2中的扩展vue.prototype
```js
//before
Vue.prototype.$http = () => {}

//after
const app = createApp({})
app.config.globalProperties.$http = () => {}

```

## isCustomElement
Type: (tag: string) => boolean
Default: undefined
Usage: 
```js
// any element starting with 'ion-' will be recognized as a custom one
app.config.isCustomElement = tag => tag.startsWith('ion-')
```
如果组件满足一定的条件，则不需要本地/全局注册组件，Vue也不会像未知本地元素一个抛出警告

注意事项：
1、所有原生的HTML和SVG不需要满足这个条件，Vue会自动检查
2、只有运行时编译器工作时，才会使用此配置项
3、如果使用runtime-only构建方式，在构建setup时isCustomElement必须传递给@vue/compiler-dom

See also:
1、option in vue-loader: https://vue-loader.vuejs.org/options.html#compileroptions

## optionMergeStategies
Type: { [key: string]: Function }
Default: {}
Usage: 
```js
const app = Vue.createApp({
  mounted() {
    console.log(this.$options.hello)
  }
})

app.config.optionMergeStrategies.hello = (parent, child, vm) => {
  return `Hello, ${child}`
}

app.mixin({
  hello: 'Vue'
})

// 'Hello, Vue'
```
定义本地选项的合并策略

See also: 
1、Custom Option Merge Strategy: https://v3.vuejs.org/guide/mixins.html#custom-option-merge-strategies

## performance
Type: Boolean
Default: false
Usage: 
将此选项设置为true，在浏览器devtool的performance/timeline面板中启用组件的初始化，编译，渲染和更新的跟踪 


# Application API
全局修改Vue行为的API被移到通过CreateApp创建的应用实例中，他们的影响局限在特定应用实例内
应用实例提供了一个上下文，应用实例上挂载的整个组件树共享这一个上下文
这个上下文提供的配置对应vue2.x中的global


## component
Arguments: 
{string} name
{Function | Object} definition (optional)

Returns: 
1、如果传入definition参数，则返回应用实例
2、如果没有传入definition参数，则返回组件定义

Usage: 
注册或得到一个全局组件，注册时会根据name参数自动设置为组件名

Example: 
```js
import { createApp } from 'vue'

const app = createApp({})

// register an options object
app.component('my-component', {
  /* ... */
})

// retrieve a registered component
const MyComponent = app.component('my-component')
```
See also: 
components: https://v3.vuejs.org/guide/component-basics.html#base-example

## config
Usage: 
包含应用配置的一个对象

Example:
```js
import { createApp } from 'vue'
const app = createApp({})

app.config = {...}
``` 

## directive
Arguments: 
{string} name
{Function | Object} definition (optional)

Returns: 
1、如果传入definition参数，则返回应用实例
2、如果没有传入definition参数，则返回组件定义

Usage: 
注册或得到一个全局的指令

Example: 

See also: 

## mixin
Arguments: 
Returns: 
Usage: 
Example: 
See also: 

## mount
Arguments: 
Returns: 
Usage: 
Example: 
See also: 

## provide
Arguments: 
Returns: 
Usage: 
Example: 
See also: 

## unmount
Arguments: 
Returns: 
Usage: 
Example: 
See also: 

## use
Arguments: 
Returns: 
Usage: 
Example: 
See also: 
