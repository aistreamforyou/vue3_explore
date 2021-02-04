# 使用vite搭建vue3项目
1、安装create-vite-app
```text
npm i -g create-vite-app
```

2、创建项目
```text
create-vite-app projectName
```

3、安装依赖
```text
npm i
```

4、启动
```text
npm run dev
```


# 全局API


## createApp 创建应用程序实例
```js
const app = Vue.createApp({
  data() {
    return {
      ...
    }
  },
  methods: {...},
  computed: {...}
})
```
注意事项：
1、根组件的选项中data也必须是函数
2、第一个参数：根组件的选项对象
3、第二个参数：root props

### typing 使用场景？？？

## h 渲染函数
### 关于渲染函数：https://v3.vuejs.org/guide/render-function.html#the-dom-tree
```js
h(tagName, props, children)
```
tagName：组件标签名（必需）
props：传给子组件的数据
children：子节点

注意事项：(子节点的构建方式)
```js
h('div', {}, [
  'Some text comes first.',//用字符串构建的文本虚拟节点
  h('h1', 'A headline'),//使用h函数
  h(MyComponent, {//带有插槽的对象？？？？？？？？？
    someProp: 'foobar'
  })
])
```

## defineComponent 定义组件
参数构造：
1、具有组件选项的对象
```js
import {defineComponent} from 'vue'
const myComponent = defineComponent({
    data(){
        return {
            
        }
    },
    methods: {
        increment(){
            //@todo
        },
    }
})
```

2、利用setup功能，函数名将作为组件名
```js
import {defineComponent, ref} from 'vue'
let HellowWorld = defineComponent(function HellowWorld(){
    const count = ref(0)
    return {count}
})
```


## defineAsyncComponent 定义异步组件
需要时加载

1、先定义，后注册
```js
import {defineAsyncComponent} from 'vue'
const asyncComp = defineAsyncComponent(()=>{
    import('./components/example.vue')
})
app.component('async-component', asyncComp)
```
2、本地注册
### 关于本地注册：https://v3.vuejs.org/guide/component-registration.html#local-registration
```js
import {createApp, defineAsyncComponent} from 'vue'
createApp({
    components: {
        asyncComp: defineAsyncComponent(()=>{
            import('./components/example.vue')
        })
    }
})
```
3、高级用法：可以接收一个对象
```js
import {defineAsyncComponent} from 'vue'
const asyncComp = defineAsyncComponent({
    // The factory function
    loader: () => import('./components/example.vue'),
    // A component to use while the async component is loading
    loadingComponent: loadingComponent,
    // A component to use if the load fails
    errorComponent: errorComponent,
    // Delay before showing the loading component. Default: 200ms.
    delay: 200,
    // The error component will be displayed if a timeout is
    // provided and exceeded. Default: Infinity.
    timeout: 3000,
    // Defining if component is suspensible. Default: true.
    suspensible: false,
    /**
    *
    * @param {*} error Error message object
    * @param {*} retry A function that indicating whether the async component should retry when the loader promise rejects
    * @param {*} fail  End of failure
    * @param {*} attempts Maximum allowed retries number
    */
    error(error, retry, fail, attempts){
        if(error.message.match(/fetch/&&attempts<=3)){
            // retry on fetch errors, 3 max attempts
            retry()
        }else{
            // Note that retry/fail are like resolve/reject of a promise:
            // one of them must be called for the error handling to continue.
            fail()
        }
    }
})
```

## resolveComponent 解析组件
注意事项：
1、resolveComponent只能在render和setup功能内使用
2、如果名称在当前应用程序实例内可用，则可以按名称解析
```js
import {createApp, resolveComponent, render} from 'vue'
const app = createApp({})
//注册组件
app.component('MyComponent', {})

//解析组件
render()
{
    const myComponent = resolveComponent('MyComponent')
}

```

## resolveDynamicComponent 解析动态组件
注意事项：
1、resolveDynamicComponent只能在render和setup功能内使用
2、使用相同的机制来解析`<component :is="">`
3、参数可以是字符串或组件选项对象

```js
import { resolveDynamicComponent } from 'vue'
render () {
  const MyComponent = resolveDynamicComponent('MyComponent')
}

```

### 关于动态组件：https://v3.vuejs.org/guide/component-dynamic-async.html#dynamic-components-with-keep-alive

## resolveDirective 解析指令
注意事项：
1、resolveDirective只能在render和setup功能内使用
2、参数只能是字符串

案例通“resolveComponent”

## withDirectives 
注意事项：
1、resolveDirective只能在render和setup功能内使用

将指令运用于虚拟节点，然后返回带有已应用指令的虚拟节点
```js
import {resolveDirective, withDirectives} from 'vue'
const fooDirective = resolveDirective('foo')
const barDirective = resolveDirective('bar')

return withDirectives(h('div',[
    [fooDirective, this.x]
    [barDirective, this.y]
]))
```
第一个参数：虚拟节点
第二个参数：列表，元素的类型仍然是列表，包含四个元素，例如：[directive, value, arg, modifiers]
```js
const testDirective = resolveDirective('testDirective')
const nodeWithDirectives = withDirectives(h('div'),[
    [
        testDirective, //指令
        100, //传给指令的值
        'click', //触发操作
        {prevent: true}//修饰符
        ]
])
```

## createRenderer 创建渲染器
接收两个通用参数：HostNode, HostElement
For example, for runtime-dom, HostNode would be the DOM Node interface and HostElement would be the DOM Element interface.

注意事项：自定义渲染器可以传入特定于平台的类型
```js
import {createRenderer} from 'vue'
const {render, createApp} = createRenderer<Node, Element>({
    patchProp,
    ...nodeOps
})
```

## nextTick 
```js
import {createApp, nextTick} from 'vue'
const app = createApp({
    setup(){
        const message = ref('helloworld')
        const changeMessage = async newMessage => {
            message.value = newMessage
            await nextTick()
            console.log('new Dom is updated')
        }
    }
})
```
关于$nextTick：https://v3.vuejs.org/api/instance-methods.html#nexttick

## mergeProps 合并props
注意事项：
1、后传的参数，其属性优先处理

```js
import {h, mergeProps} from 'vue'
export default{
    inheritAttrs: false,
    render(){
        const props = mergeProps({
            // The class will be merged with any class from $attrs
            class: 'active'
        }, this.$attrs)
        return h('div', props)
    }
}
```

## useCssModule 
注意事项：
1、resolveDirective只能在render和setup功能内使用
2、允许在单文件组件的setup函数中访问css模块
3、style标签上，添加属性module
```html
<script>
    import {h, useCssModule} from 'vue'
    export default{
        setup(){
            const style = useCssModule()
            return () => h('div', {
                class: style.success
            },'Task complete!')
        }
    }
</script>
<style module>
.success{
    color: #000;
}
</style>
```
关于VUE Loader   —— CSS Modules：https://vue-loader.vuejs.org/guide/css-modules.html


# Reactivity API 响应式API
关于响应式的API，官方案例都是基于单文件组件语法的
## Basic Reactivity API
### reactive
注意事项：
1、返回对象的响应式副本
2、深度转换（包括所有的嵌套属性在内）
3、类型
```ts
function reactive<T extends Object>(Target:T):unwrapNestedRefs<T>
```
4、reactive将打开所有的深度refs，并维持ref的响应式
```js
import {ref, reactive} from 'vue'
const count = ref(1)
const obj = reactive({count})

// ref will be unwrapped
console.log(obj.count===count.value)//true
// it will update `obj.count`
count.value++
console.log(count.value) // 2
console.log(obj.count) // 2
// it will also update `count` ref
obj.count++
console.log(obj.count) // 3
console.log(count.value) // 3
```

5、将一个ref数据赋值给响应式属性，ref将自动拆包
```js
const count = ref(1)
const obj = reactive({})
obj.count = count
console.log(obj.count) // 1
console.log(obj.count === count.value) // true
```

关于ref：https://v3.vuejs.org/api/refs-api.html#ref

### readonly
获取一个普通对象或响应式对象，返回一个对原始对象的只读代理（作用于所有嵌套属性）
```js
const original = reactive({count: 0})
const copy = readonly(original)
watchEffect(()=>{
    // works for reactivity tracking
    console.log(original.count)
})

// mutating original will trigger watchers relying on the copy
original.count++
// mutating the copy will fail and result in a warning
copy.count++ // warning!

```

### isProxy 检查一个对象是否通过reactive或readonly创建

### isReactive 
检查一个对象是否是通过reactive创建
注意事项：
1、检查以前，将对象转换成只读的，依然等正确检测出来源
```js
import {reactive, isReactive} from 'vue'
export default{
    setup(){
        const state = reactive({
            name: 'john'
        })
        const plain = readonly({
            name: 'joke'
        })
        console.log(isReactive(plain)) // -> false
        // readonly proxy created from reactive proxy
        const stateCopy = readonly(state)
        console.log(isReactive(stateCopy)) // -> true
    }
}
```

### isReadonly 检查一个对象是否是通过readonly创建

### toRaw（慎用）
返回通过reactive或readonly代理的对象的原始数据，
适用场景：
1、临时读取数据，并不产生代理访问/跟踪的开销
2、写入时不产生变更触发
禁忌：
1、持有对永久对象的引用
```js
const foo = {}
const reactiveFoo = reactive(foo)
console.log(toRaw(reactiveFoo)===foo)//true
```

### markRaw (？？？？？？？？？)
给一个对象添加标记，使其不能被转换成一个代理
返回值：对象本身

***markRaw and the shallowXXX APIs below allow you to 
selectively opt-out of the default deep reactive/readonly conversion 
and embed raw, non-proxied objects in your state graph.***

使用场景：
1、不应该被设置为响应式，例如：复杂的第三方类实例，vue组件对象
2、渲染不可变大列表时，跳过代理可以提升性能
***They are considered advanced because the raw opt-out is only at the root level,
 so if you set a nested, non-marked raw object into a reactive object and then access it again, you get the proxied version back. 
 This can lead to identity hazards - 
i.e. performing an operation that relies on object identity but using both the raw and the proxied version of the same object:***

```js
const foo = markRaw({
    nested: {}
})
const bar = reactive({
    // although `foo` is marked as raw, foo.nested is not.
    nested: foo.nested
})
console.log(foo.nested === bar.nested) // false
```

### shallowReactive 
```js
const state = shallowReactive({
    foo: 1,
    nested: {
        bar: 2
    }
})
// mutating state's own properties is reactive
state.foo++
// ...but does not convert nested objects
isReactive(state.nested)//false
state.nested.bar++ // non-reactive
```

### shallowReadonly     （？？？？）
```js
const state = shallowReadonly({
  foo: 1,
  nested: {
    bar: 2
  }
})

// mutating state's own properties will fail
state.foo++
// ...but works on nested objects
isReadonly(state.nested) // false
state.nested.bar++ // works
```

## refs
### ref
文档地址：https://v3.vuejs.org/api/refs-api.html#ref
接收一个内部值，返回一个响应式可变的对象，只有一个value属性，指向这个内部值

类型
```ts
interface Ref<T> {
  value: T
}

function ref<T>(value: T): Ref<T>
```
如果将一个对象赋值给ref的value，那么这个对象的深层属性也会做响应式代理

***Sometimes we may need to specify complex types for a ref's inner value.
 We can do that succinctly by passing a generics argument when calling ref to override the default inference:***
```ts
const foo = ref<string | number>('foo') // foo's type: Ref<string | number>

foo.value = 123 // ok!
```
ref使用泛型
```ts
function useState<State extends string>(Initial:State){
    const state = ref(initial) as ref(State) //state.value -> State extends string
    return state
}
```

### unref
接收一个ref对象，返回ref对象的value属性
如果传入的参数不是ref对象，则返回参数本身
This is a sugar function for val = isRef(val) ? val.value : val.

### toRef
为一个响应式对象的属性创建一个ref，将ref传递出去，保持对源对象的响应式链接（！！！）
```js
const state = reactive({
    foo: 1,
    bar: 2
})
const fooRef = toRef(state, 'foo')
fooRef.value++
console.log(state.foo)// 2
state.foo++
console.log(fooRef.value)// 3
```
使用场景：在setup函数中处理props
```js
export default {
    setup(props){
        useSomeFeature(toRef(props, 'foo'))
    }
}
```
即使属性不存在，仍然会创建可用的ref；尤其在处理可选参数时，不会被toRefs拾取

### toRefs
将响应式对象转换成一个普通对象，其中每个属性都是一个ref指向原始响应式对象的相应属性
```js
const state = reactive({
    foo: 1,
    bar: 2
})
const stateAsRefs = toRefs(state)
/*
Type of stateAsRefs:

{
  foo: Ref<number>,
  bar: Ref<number>
}
*/
// The ref and the original property is "linked"
state.foo++
console.log(stateAsRefs.foo.value) // 2

stateAsRefs.foo.value++
console.log(state.foo) // 3
```
使用场景：解构响应式对象时，使用toRefs可以维持其属性的相应特性
```js
function useFeatureX(){
    const state = reactive({
        foo: 1,
        bar: 2
    })
    return toRefs(state)
}
export default {
    setup(){
        const {foo, bar} = useFeatureX()
        return {
            foo,
            bar
        }
    }
}
```

### isRef
检查一个值是否是ref对象

### customRef
接收一个包含track，trigger作为参数，返回带有get和set属性的工厂函数，创建一个本地化的带有属性跟踪和变更触发的ref

```html
<input v-model="text" />
```
```js
function useDebouncedRef(value, delay=200){
    let timeout
    return customRef((track,trigger)=>{
        return {
            get(){
                track()
                return value
            },
            set(newValue){
                clearTimeout(timeout)
                timeout = setTimeout(()=>{
                    value = newValue
                    trigger()
                }, delay)
            }
        }
    })
}
export default{
    setup(){
        return {
            text: useDebouncedRef('hello')
        }
    }
}
```
类型：
```ts
function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

type CustomRefFactory<T> = (
  track: () => void,
  trigger: () => void
) => {
  get: () => T
  set: (value: T) => void
}
```

### shallowRef

### triggerRef（？？？）
执行手动绑定到shallowRef上的所有副作用
```js
const shallow = shallowRef({
  greet: 'Hello, world'
})

// Logs "Hello, world" once for the first run-through
watchEffect(() => {
  console.log(shallow.value.greet)
})

// This won't trigger the effect because the ref is shallow
shallow.value.greet = 'Hello, universe'

// Logs "Hello, universe"
triggerRef(shallow)
```

## computed and watch
1、接收一个getter函数，返回一个不可变的ref对象（用于代理来自于getter的返回值）

```js
const count = ref(1)
const plusOne = computed(()=>{count.value + 1})
console.log(plusOne.value) // 2
plusOne.value++ // error
```
2、接收一个带有get和set函数的对象，返回一个可变的ref对象
```js
const count = ref(1)
const plusOne = computed(()=>{
    get: () => count.value + 1,
    set: (newValue) => {
        count.value = newValue - 1
    }
})
plusOne.value = 1
console.log(count.value)//0
```

类型：
```ts
// read-only
function computed<T>(getter: () => T): Readonly<Ref<Readonly<T>>>

// writable
function computed<T>(options: { get: () => T; set: (value: T) => void }): Ref<T>
```

### watchEffect
响应式地跟踪依赖，立即执行一个函数，无论依赖的值是否改变
```js
const count = ref(0)

watchEffect(() => console.log(count.value))
// -> logs 0

setTimeout(() => {
  count.value++
  // -> logs 1
}, 100)
```
类型：
```ts
function watchEffect(
  effect: (onInvalidate: InvalidateCbRegistrator) => void,
  options?: WatchEffectOptions
): StopHandle

interface WatchEffectOptions {
  flush?: 'pre' | 'post' | 'sync' // default: 'pre'
  onTrack?: (event: DebuggerEvent) => void
  onTrigger?: (event: DebuggerEvent) => void
}

interface DebuggerEvent {
  effect: ReactiveEffect
  target: any
  type: OperationTypes
  key: string | symbol | undefined
}

type InvalidateCbRegistrator = (invalidate: () => void) => void

type StopHandle = () => void
```

### watch
watch和Options API中的$watch是完全等效的

与watchEffect相比，watch可以：
1、Perform the side effect lazily;
2、Be more specific about what state should trigger the watcher to re-run;
3、Access both the previous and current value of the watched state.

#### watching a single source
监听器的数据源可以是getter函数（返回一个值），也可以是ref
```js
//watching a getter
const state = reactive({count: 1})
watch(()=>state.count, (count,prevCount)=>{
    ...
})
// watching a ref
const count = ref(1)
watch(count, (count,prevCount)=>{})

```

#### Watching multiple sources
```js
watch([fooRef, barRef], ([foo,bar],[prevFoo, prevBar])=>{})
```

#### Shared behavior with watchEffect（？？？）
shares behavior with watchEffect in terms of manual stoppage, 
side effect invalidation (with onInvalidate passed to the callback as the 3rd argument instead),
 flush timing and debugging.

类型：
```ts
// watching single source
function watch<T>(
  source: WatcherSource<T>,
  callback: (
    value: T,
    oldValue: T,
    onInvalidate: InvalidateCbRegistrator
  ) => void,
  options?: WatchOptions
): StopHandle

// watching multiple sources
function watch<T extends WatcherSource<unknown>[]>(
  sources: T
  callback: (
    values: MapSources<T>,
    oldValues: MapSources<T>,
    onInvalidate: InvalidateCbRegistrator
  ) => void,
  options? : WatchOptions
): StopHandle

type WatcherSource<T> = Ref<T> | (() => T)

type MapSources<T> = {
  [K in keyof T]: T[K] extends WatcherSource<infer V> ? V : never
}

// see `watchEffect` typing for shared options
interface WatchOptions extends WatchEffectOptions {
  immediate?: boolean // default: false
  deep?: boolean
}
```








