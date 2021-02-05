# component
props：字符串或组件

用于渲染动态组件的元组件
```vue
<!-- a dynamic component controlled by -->
<!-- the `componentId` property on the vm -->
<component :is="componentId"></component>

<!-- can also render registered component or component passed as prop -->
<component :is="$options.components.child"></component>

<!-- can reference components by string -->
<component :is="condition ? 'FooComponent' : 'BarComponent'"></component>

<!-- can be used to render native HTML elements -->
<component :is="href ? 'a' : 'span'"></component>
```

# transition
props:
+   name: (string) 自动产生对应的过度css类
+   appear: (Boolean) 是否将过渡应用到初始渲染中，默认false
+   persisted: (Boolean) 如果值为true，则表明这个过渡不会插入/删除元素，而是切换显示/隐藏的状态；
                如果过渡钩子已注入，渲染器会忽略，但是本地指令可以通过已注入的过渡钩子控制过渡
+   css: (Boolean) 是否应用默认的过渡样式，默认是true；
                如果设置为false，则只会触发通过组件事件注册的js钩子
+   type: (string) Specifies the type of transition events to wait for to determine transition end timing.
                可选值：‘transition’和‘animation’，默认情况下会自动检测持续事件较长的类型
+   mode: (string) 控制进出过渡的时序，可选值：‘in-out’和‘out-in’，默认为并发
+   duration: (Number) 指定过渡的持续时间，默认情况下，vue等待根过渡的第一个transitionend或animationend事件
+   enter-from-class: (string)
+   leave-from-class: (string)
+   appear-class: (string)
+   enter-to-class: (string)
+   leave-to-class: (string)
+   appear-to-class: (string)
+   enter-active-class: (string)
+   leave-active-class: (string)
+   appear-active-class: (string)
                
事件
+   before-enter
+   before-leave
+   enter
+   leave
+   appear
+   after-enter
+   after-leave
+   after-appear
+   enter-cancelled
+   leave-cancelled     (v-show  only)
+   appear-cancelled


# transition-group
props:
+   tag: (string) 如果未定义，渲染时不包含根元素
+   move-class: 覆盖应用在移动过渡期间的样式
+   暴露transition中除mode以外所有的属性

事件：同transition

用法：
1、默认情况下不产生DOM元素，但可以通过tag属性设置
2、每一个过渡元素必须有一个独一无二的key
3、supports moving transitions via CSS transform. 
When a child's position on screen has changed after an update, 
it will get applied a moving CSS class (auto generated from the name attribute 
or configured with the move-class attribute). 
If the CSS transform property is "transition-able" when the moving class is applied, 
the element will be smoothly animated to its destination using the FLIP technique (opens new window)


# keep-alive
props：
+   include: (string|RegExp|Array) 只有匹配到名称的组件才会被缓存
+   exclude; (string|RegExp|Array) 任何匹配到名称的组件都不会被缓存
+   max: (Number|string)    缓存的组件实例最大个数

用法：
1、When a component is toggled inside <keep-alive>, 
its activated and deactivated lifecycle hooks will be invoked accordingly.

2、<keep-alive>是针对具有一个直接子组件被切换的情况而设计的。
如果您有v-for里面，它不起作用。
当有多个条件子项时，如上所述，<keep-alive>要求一次仅渲染一个子项。

3、include和exclude中的值为字符串时，字符串中的值必须由逗号分隔

4、匹配时，
    首先，检查组件的name选项，
    然后，检查本地注册名（the key in the parent's components option）
    匿名组件无法匹配

5、缓存组件数量超过max时，会销毁最近访问量最少的缓存组件实例

6、keep-alive无法与函数组件一起使用，因为函数组件没有实例

# slot
props：
+   name： 具名插槽的名称

用法：
1、slot用作组件模板中的内容分发出口，其本身会被代替

关于slot：https://v3.vuejs.org/guide/component-basics.html#using-v-model-on-components

# teleport  （传送）
props：
+   to: (string) 必须是有效的查询选择器，或一个页面元素，将teleport内的内容移动到指定的元素内
+   disabled: (Boolean) ，可选，如果值为true，则内容不会传送到任何位置，而是渲染在teleport的父元素内

```html
<teleport to="#popup" :disabled="displayVideoInline">
  <video src="./my-movie.mp4">
</teleport>
```

注意事项：
Notice that this will move the actual DOM nodes instead of being destroyed and recreated, 
and it will keep any component instances alive as well. All stateful HTML elements (i.e. a playing video) will keep their state.











