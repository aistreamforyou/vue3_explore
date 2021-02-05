# Directives 指令
## v-text
Expects: string
Details: 使用插值语法更新部分文本

案例：
```html
<span v-text="msg"></span>
<!-- same as -->
<span>{{msg}}</span>
```

相关链接：https://v3.vuejs.org/guide/template-syntax.html#text


## v-html
Expects: string
Details: 更新元素的innerHTML,这些内容会被当成普通的html，而不会被视为模板
注意事项：
1、在网页内动态渲染任意html是很危险的，容易导致XSS攻击
2、v-html用在信任的内容中，绝对不要用在用户提供的内容中
3、在单文件组件中，scoped styles不会应用到v-html内容里，因为html在vue的模板编译器内是不执行的；
    如果想将样式作用到v-html内，可以使用css module
    或 an additional, global <style> element with a manual scoping strategy such as BEM.
案例：（略）
相关链接：https://v3.vuejs.org/guide/template-syntax.html#raw-html


## show
Expects: any
Details:
1、条件渲染的一种
2、不支持template元素，因为template本身不会被渲染
3、切换时，只是控制样式
案例：（略）
相关链接：https://v3.vuejs.org/guide/conditional.html#v-show

## if
Expects: any
Details: 
1、切换时，伴随着组件或元素的销毁和重新构造
2、如果v-if作用于template元素时，那么它的innerHTML会被抽象成一个条件块
3、当v-if和v-for一起使用时，v-if优先级高于v-for，不建议在一个元素内同时使用这两个指令
关于列表渲染指南：https://v3.vuejs.org/guide/list.html#v-for-with-v-if
案例：（略）
相关链接：https://v3.vuejs.org/guide/conditional.html#v-if

## else
Expects: 无
Details:
1、上一个兄弟节点必须包含指令：v-if或v-else-if
案例：(略)
相关链接：https://v3.vuejs.org/guide/conditional.html#v-else

## if-else-if
Expects: any
Details: 
1、上一个兄弟元素必须包含指令：v-if或v-else-if
案例：（略）
相关链接：https://v3.vuejs.org/guide/conditional.html#v-else

## v-for
Expects: Array | Object | number | string | Itarable
Details:
1、强制重排元素列表时，要为每一项设置key
2、v-for可以作用于实现Iterable接口的对象，例如：map和set
案例：（略）
相关链接：https://v3.vuejs.org/guide/list.html#mapping-an-array-to-elements-with-v-for
1、在v-for块内可以访问父级作用域的属性
2、可以使用以下用法：
```html
<div v-for="item of items"></div>
```
用of代替in，更接近迭代器的js语法
3、v-for遍历对象
```html
<li v-for="(value, name) in myObject">
  {{ name }}: {{ value }}
</li>
<li v-for="(value, name, index) in myObject">
  {{ index }}. {{ name }}: {{ value }}
</li>
```
注意事项：
遍历对象时，遍历顺序是基于Object.keys()的枚举顺序的，不能保证与js引擎实现保持一致

4、维护状态
1）更新用v-for渲染的列表时，默认使用“in-place patch”策略
2）当数据项的顺序变化时，不是修改DOM元素的顺序去适应数据顺序，而是patch each element in-place，保证特定索引号该呈现的内容
3）默认的更新模式是高效的，但仅适用于列表渲染输出不依赖子组件状态或临时DOM状态（例如：输入框输入的数据）
4）不要使用非简单值(non-primitive values)作为key

5、列表变更探测
1）能触发响应式更新的数组方法
+   push
+   pop
+   shift
+   unshift
+   splice
+   sort
+   reverse

2）替换数组
不会触发数组响应式机制但会返回新数组的方法：filter, concat, slice
Vue实现了一些智能启发方法来最大化重用DOM元素，因此用另一个包含重叠元素的数组替换原始数组是一个高效的操作

3）显示过滤/重排后的数组
用计算属性返回过滤/排序后的数组，而不修改原始数组
```html
<li v-for="n in evenNumbers" :key="n">{{ n }}</li>
```
```js
export default {
    data() {
      return {
        numbers: [ 1, 2, 3, 4, 5 ]
      }
    },
    computed: {
      evenNumbers() {
        return this.numbers.filter(number => number % 2 === 0)
      }
    }
}
```
在计算属性不可行的场景（嵌套v-for）：使用methods中的方法（？？？？？）
```html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)" :key="n">{{ n }}</li>
</ul>
```
```js
export default {
    data() {
      return {
        sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
      }
    },
    methods: {
      even(numbers) {
        return numbers.filter(number => number % 2 === 0)
      }
    }
}
```
5、v-for使用range
6、v-for用在template上
7、v-for用在自定义组件上


## v-on
shorthand：@
Expects: Function | Inline Statement | Object
argument: 事件
modifiers: 
+   .stop           calling event.stopPropagation()
+   .prevent        calling event.preventDefault()
+   .capture        在捕捉模式下，添加事件监听
+   .self           仅当从此元素调用事件时才触发事件处理程序
+   .{keyAlias}     只在某一些key上触发事件处理程序
+   .once           只触发一次
+   .left           仅针对鼠标左键触发的事件处理程序
+   .right          仅针对鼠标右键触发的事件处理程序
+   .middle         仅针对鼠标中键触发的事件处理程序
+   .passive        给dom事件添加属性{passive: true}

Details: 
1、如果使用内联语句，语句可以访问特殊属性$event
```html
v-on:click="handle('ok', $event)"
```
2、绑定一个对象：v-on="{ mousedown: doThis, mouseup: doThat }"，处理程序不传参，不支持任何修饰符

案例：
```html
<!-- method handler -->
<button v-on:click="doThis"></button>

<!-- dynamic event -->
<button v-on:[event]="doThis"></button>

<!-- inline statement -->
<button v-on:click="doThat('hello', $event)"></button>

<!-- shorthand -->
<button @click="doThis"></button>

<!-- shorthand dynamic event -->
<button @[event]="doThis"></button>

<!-- stop propagation -->
<button @click.stop="doThis"></button>

<!-- prevent default -->
<button @click.prevent="doThis"></button>

<!-- prevent default without expression -->
<form @submit.prevent></form>

<!-- chain modifiers -->
<button @click.stop.prevent="doThis"></button>

<!-- key modifier using keyAlias -->
<input @keyup.enter="onEnter" />

<!-- the click event will be triggered at most once -->
<button v-on:click.once="doThis"></button>

<!-- object syntax -->
<button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
```

相关链接：




## v-bind
Expects: 
Details:

案例：


相关链接：




## v-model
Expects: 
Details:

案例：


相关链接：



## v-slot
Expects: 
Details:

案例：


相关链接：




## v-pre
Expects: 
Details:

案例：


相关链接：




## v-cloak
Expects: 
Details:

案例：


相关链接：




## v-once
Expects: 
Details:

案例：


相关链接：





## v-is
Expects: 
Details:

案例：


相关链接：









# Special Attribute 特殊属性
## key
值的类型：number | string



## ref



## is
































