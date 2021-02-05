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
说明：修饰符出现的目的——让事件处理程序中全部是逻辑代码
```html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```
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

<!-- both one() and two() will execute on button click -->
<button @click="one($event), two($event)">
  Submit
</button>

```

相关链接：
1、Event Handling：https://v3.vuejs.org/guide/events.html#listening-to-events
2、试验：
using @click.prevent.self will prevent all clicks while @click.self.prevent will only prevent clicks on the element itself.
3、修饰符once，对于自定义组件同样适用
4、如果包含修饰符passive，则表示事件处理程序（listener）不会调用preventDefault，
    如果listener调用了preventDefault，那么用户代理除了警告不会做任何事情
    在移动端尤其有用
    .passive和.prevent不要一起使用，语义冲突
5、.exact修饰符
```html
<!-- this will fire even if Alt or Shift is also pressed -->
<button @click.ctrl="onClick">A</button>

<!-- this will only fire when Ctrl and no other keys are pressed -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- this will only fire when no system modifiers are pressed -->
<button @click.exact="onClick">A</button>
```
6、使用v-on和@的几个好处
+   通过浏览器模板，更容易定位事件处理程序
+   因为不用在js代码中手动关联事件监听，viewModel中都是纯逻辑，便于测试
+   当viewModel销毁时，所有的事件监听自动删除

## v-bind
shorthand： :
Expects: any(with argument) | Object(without argument)
Argument: attr or prop (optional)
Modifier: .camel 将kebab-case属性名转换成驼峰式
Details: 
1、如果使用字符串模板或使用vue-loader/vueify编译，则不需要.camel
案例：
```html
<!-- bind an attribute -->
<img v-bind:src="imageSrc" />

<!-- dynamic attribute name -->
<button v-bind:[key]="value"></button>

<!-- shorthand -->
<img :src="imageSrc" />

<!-- shorthand dynamic attribute name -->
<button :[key]="value"></button>

<!-- with inline string concatenation -->
<img :src="'/path/to/images/' + fileName" />

<!-- class binding -->
<div :class="{ red: isRed }"></div>
<div :class="[classA, classB]"></div>
<div :class="[classA, { classB: isB, classC: isC }]">
  <!-- style binding -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- binding an object of attributes -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- prop binding. "prop" must be declared in my-component. -->
  <my-component :prop="someThing"></my-component>

  <!-- pass down parent props in common with a child component -->
  <child-component v-bind="$props"></child-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
</div>
```

相关链接：
1、class and style binding: https://v3.vuejs.org/guide/class-and-style.html#binding-html-classes
2、components-props: https://v3.vuejs.org/guide/component-basics.html#passing-data-to-child-components-with-props



## v-model
Expects: 输入表单元素的值，组件的输出
Limited-to: input, select, textArea, components
Modifiers: 
1、.lazy: 替代input监听change事件
2、.number: 将有效输入字符串转换成数字
3、.trim: 将输入去除多余的空格

相关链接：
1、Form input binding: https://v3.vuejs.org/guide/forms.html#basic-usage
2、  Form input components using custom events:
https://v3.vuejs.org/guide/component-custom-events.html#validate-emitted-events

Details: 

案例：



## v-slot
shorthand: #
Expects: 有效的js表达式，只有在向插槽传递props时，才是必须的
Argument: 插槽名称（默认为default）
Limited-to: 
1、仅适用于<template>
2、components (for a lone default slot with props)
Details:

案例：
```html
<!-- Named slots -->
<base-layout>
  <template v-slot:header>
    Header content
  </template>

  <template v-slot:default>
    Default slot content
  </template>

  <template v-slot:footer>
    Footer content
  </template>
</base-layout>

<!-- Named slot that receives props -->
<infinite-scroll>
  <template v-slot:item="slotProps">
    <div class="item">
      {{ slotProps.item.text }}
    </div>
  </template>
</infinite-scroll>

<!-- Default slot that receive props, with destructuring -->
<mouse-position v-slot="{ x, y }">
  Mouse position: {{ x }}, {{ y }}
</mouse-position>
```

相关链接：
slot: https://v3.vuejs.org/guide/component-slots.html#slot-content




## v-pre
Expects: none
Details:
1、对其内容及子节点跳过编译
2、跳过大量没有指令的节点，可以加快编译速度

## v-cloak
Expects: none
Details: 
1、这个指令会一直存在，直到关联的组件实例编译完成
2、结合[v-cloak] { display: none }等css规则，这个指令用来隐藏未编译的mustacle绑定，直到组件实例准备完成

案例：
```css
[v-cloak] {
  display: none;
}
```
```html
<div v-cloak>
  {{ message }}
</div>
```

## v-once
Expects: none
Details:

## v-is
注意事项：
1、仅会对直接写在html中的vue模板产生影响
Expects: string literal
Limited-to: native HTML elements
Details: 
When using in-DOM templates, the template is subject to native HTML parsing rules.
Some HTML elements, such as <ul>, <ol>, <table> and <select> have restrictions on what elements can appear inside them, 
and some elements such as <li>, <tr>, and <option> can only appear inside certain other elements. 
As a workaround, we can use v-is directive on these elements:
案例：
v-is functions like a dynamic 2.x :is binding - so to render a component by its registered name, 
its value should be a JavaScript string literal:
```html
<!-- Incorrect, nothing will be rendered -->
<tr v-is="blog-post-row"></tr>

<!-- Correct -->
<tr v-is="'blog-post-row'"></tr>
```


# Special Attribute 特殊属性
## key
值的类型：number | string

强制替换节点的场景：
1、适当的触发组件的生命周期钩子
2、触发过渡

案例：
```html
<transition>
  <span :key="text">{{ text }}</span>
</transition>
```
When text changes, the <span> will always be replaced instead of patched,
 so a transition will be triggered
 
## ref
Expect: string | Function
1、如果用在一个组件，引用将成为组件的实例
2、用在普通的DOM元素上，这个引用将是这个元素
```html
<!-- vm.$refs.p will be the DOM node -->
<p ref="p">hello</p>

<!-- vm.$refs.child will be the child component instance -->
<child-component ref="child"></child-component>

<!-- When bound dynamically, we can define ref as a callback function, passing the element or component instance explicitly -->
<child-component :ref="(el) => child = el"></child-component>
```

An important note about the ref registration timing: 
because the refs themselves are created as a result of the render function, 
you cannot access them on the initial render - they don't exist yet! $refs is also non-reactive, 
therefore you should not attempt to use it in templates for data-binding.

相关链接：
child component refs: https://v3.vuejs.org/guide/component-template-refs.html

## is
Expects: string | Object(component's option object)

用于动态组件

```html
<!-- component changes when currentView changes -->
<component :is="currentView"></component>
```

相关链接:
1、Dynamic Components：https://v3.vuejs.org/guide/component-dynamic-async.html#dynamic-components-with-keep-alive
2、DOM Template Parsing Caveats： https://v3.vuejs.org/guide/component-basics.html#dom-template-parsing-caveats






























