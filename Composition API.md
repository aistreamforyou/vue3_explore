# Composition API
## setup
在props解析完成之后，在组件的created之前执行的组件选项，，setup会作为Composition API的入口
参数为：props，SetupContext

```ts
interface Data {
  [key: string]: unknown
}

interface SetupContext {
  attrs: Data
  slots: Slots
  emit: (event: string, ...args: unknown[]) => void
}

function setup(props: Data, context: SetupContext): Data
```
注意事项：
1、使用defineComponent，可以知道传递给setup的参数类型接口

案例：
1、使用模板
```html
<!-- MyBook.vue -->
<template>
  <div>{{ readersNumber }} {{ book.title }}</div>
</template>
```
```js
import {ref, reactive} from 'vue'
export default{
    setup(){
        const readersNumber = ref(0)
        const book = reactive({title: 'heheda'})
        return {
            readersNumber,
            book
        }
    }
}
```

2、使用渲染函数
```js
import {ref, reactive} from 'vue'
export default{
    setup(){
        const readersNumber = ref(0)
        const book = reactive({title: 'hahaxiogn'})
        return () => h('div', [readersNumber, book.title])
    }
}
```

## Lifecycle Hooks
生命周期钩子函数可以通过直接导入来注册
```js
import {onMounted, onUpdated, onUnmounted} from 'vue'
const MyComponent = {
    setup(){
        onMounted(()=>{}),
        onUpdated(()=>{}),
        onUnmounted(()=>{})
    }
}
```
1、生命周期钩子注册函数，只需要在setup内同步调用即可，
因为它们依赖内部的全局状态去定位当前活动的实例（实例的setup正在被调用的）

2、设置组件实例的上下文，是在生命周期钩子的同步执行期间
3、当组件卸载时，生命周期钩子内部同步创建的监听器和计算属性会自动销毁

Options API生命周期选项和Composition API之间的映射

beforeCreate                        use  setup()
created                             use setup()
beforeMount                         onBeforeMount
mounted                             onMounted
beforeUpdate                        onBeforeUpdate
updated                             onUpdated
beforeUnmount                       onBeforeUnmount
unmounted                           onUnmounted
errorCaptured                       onErrorCaptured
renderTracked                       onRenderTracked
renderTriggered                     onErrorTriggered

## Provide / Inject
provide和inject支持依赖注入，两者都可以通过当前活动实例在setup内被调用
```ts
interface InjectionKey<T> extends Symbol {}

function provide<T>(key: InjectionKey<T> | string, value: T): void

// without default value
function inject<T>(key: InjectionKey<T> | string): T | undefined
// with default value
function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T
// with factory
function inject<T>(
  key: InjectionKey<T> | string,
  defaultValue: () => T,
  treatDefaultAsFactory: true
): T

```

如果key是字符串或符号，那么必须显示声明
```ts
const foo = inject<string>('foo') // string | undefined
```

### getCurrentInstance
当前实例：对于高级用法，库创建者等很有用
```js
import {getCurrentInstance} from 'vue'
export default {
    setup(){
        const currentInstance = getCurrentInstance()
        currentInstance.appContext.config.globalProperties
    }
}
```

只在setup或生命周期钩子执行期间（代码内，或代码内调用执行getCurrentInstance的函数）可以执行
























