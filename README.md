# Vue3.0 Demo

此仓库记录了阅读 [Vue3 官方文档](https://staging-cn.vuejs.org/guide/introduction.html)的过程。

## 指南

### 开始

#### 简介

* Vue 提供了一套声明式的、组件化的编程模型。
* 渐进式框架。
* SFC。
* Vue 的组件有两种风格的 API ：选项式 API 和 组合式 API。
    * 选项式 API 通过包含多个选项的对象来描述组件的逻辑，如 `data`、`methods` 和 `mounted`。
    * 组合式 API 通常配合 `<script setup>` 使用，在内部编写逻辑函数。

#### 快速开始

* 采用构建工具安装，且使用 `TypeScript`、`JSX`、`Vue Router`、`Pinia`、`Vitest`、`Cypress`、`ESLint` 和 `Prettier`。
  ```bash
  pnpm create vue@latest
  ```
* 官方构建工具是基于 Vite 的。

### 基础

#### 创建一个 Vue 应用

* 每个 Vue 应用都是通过 `createApp` 函数创建的应用实例。
  ```javascript
  import { createApp } from 'vue'
  const app = createApp({
  // 根组件选项
  }) 
  ```
* 如果使用单文件组件，可以直接将组件文件传入 `createApp()` 中。
* 应用实例需要挂载到 DOM 容器上才会渲染，通过调用 `.mount()` 方法实现，该方法形参为实际的 DOM 元素或 CSS 选择器字符串。
* `.mount()` 方法的返回值为根组件实例，而非应用实例。
* 在未采用构建工具使用 Vue 时，可以直接在挂在容器中书写组件模板。
* 当根组件没有 `template` 选项时，Vue 将自动使用容器的 `innerHTML` 作为模板。
* 实例会暴露一个 `.config` 对象允许我们配置一些应用级的选项，例如定义一个应用级的错误处理器。
  ```javascript
  app.config.erroHandler = err => {
  // 处理错误
  }
  ```
* 应用实例还提供了一些方法来注册应用范围内可用的资源，如注册一个组件，使其在应用的任何地方都可用。
  ```javascript
  app.component('SadComponent', SadComponent)
  ```
* `createApp` API 允许多个 Vue 应用共存在同一个页面上，而且每个应用都拥有自己的用于配置和全局资源的作用域。
* 如果你正在使用 Vue 来增强服务端渲染 HTML，并且只想要 Vue 去控制一个大型页面中特殊的一小部分，应避免将一个单独的 Vue 应用实例挂载到整个页面上，而是应该创建多个小的应用实例，将它们分别挂载到所需的元素上去。

#### 模板语法

* 文本插值：`{{ sad }}`。
* 原始 HTML：`v-html="rawHtml"`。
* `v-bind` 用于属性绑定，简写为 `:`。
* 布尔型属性为真值或空字符串时，元素会包含这个属性，当为其他假值时，元素会忽略这个属性。
* 可以通过不带属性的 `v-bind` 绑定包含多个属性的对象来实现动态绑定多个值。
* 可以在模板的数据绑定中使用 `JavaScript` 表达式。
* 模板中的表达式将被沙盒化，仅能访问到有限的全局对象列表，可以在 `app.config.globalProperites` 显式的添加所需的全局对象。
* `v-if` 指令基于表达式的值的真假来移除或插入该元素。
* `v-on` 用于添加监听事件，简写为 `@`。
* 动态参数使用 `[]` 声明，内部会作为表达式动态执行，计算的值作为参数值。
* 在 HTML 中空格和引号作为属性名都是不合法的，所以当需要传入一个复杂的动态参数的时候，推荐使用计算属性来替换复杂的表达式。
* 当使用内嵌模板时，避免将具有大写字符的字符串作为属性名，因为浏览器会强制将其转换为小写。
* 修饰符是以点开头的特殊后缀，表明指令需要一些特殊的方式被绑定。
* `.prevent` 修饰符会告知 `v-on` 指令对出发的事件调用 `event.preventDefault()`。

#### 响应式基础

##### 选项式

* 通过 `data` 选项来声明组件的响应式状态。`data` 选项的值应为一个返回一个对象的函数。Vue 将在创建实例的时候调用此函数，
并将函数的返回对象封装到其响应系统中。此对象的任何顶层属性都被代理到组件实例上。
* 这些实例上的属性仅在实例首次创建的时候被添加，因此需要确保其在 `data` 函数返回的对象上，如果在创建的时候没有初始值，也要尽量初始化它的值。
* 不通过 `data` 函数，直接向实例上添加属性，将不会触发响应式更新。
* Vue 在组件实例上暴露的内置 API 使用 `$` 作为前缀，同时也为内部属性保留 `_` 前缀，因此应该避免在顶层 `data` 中使用这些字符作为前缀。
* Vue3 中，数据是基于 JavaScript Proxy 实现响应式的，所以当你将一个引用类型的变量复制给响应式数据后，这个数据不全等于那个引用变量。
* 通过 `methods` 选项来为组件添加方法。`methods` 的值是一个包含方法的对象。
* Vue 自动为 `methods` 中的方法绑定了永远指向组件实例的 this。
* 方法也可以在模板上访问。
* DOM 并不是在你修改响应状态后立马更新，而是缓冲到“下个时机”，确保无论进行了多少声明更改，每个组件只需要更新一次。
* 若要等待一个状态改变后的 DOM 更新完成，你可以使用 `nextTick()` 这个全局 API。
* 状态默认具有深层响应式，修改对象和数组都可以被检测到。
* 提供了创建浅响应对象的方法。
* 有时候我们可能需要动态的创建一个方法函数，但这种方法对于重用的组件是有问题的，因为多个组件可能会因为函数的状态相互干扰，我们可以在 `created`
声明周期钩子中通过 `_.` 来创建组件内部的方法。

##### 组合式

* 通过 `reactive()` 函数来声明响应式对象或数组。
* 要在组件模板中使用响应式状态，需要在 `setup()` 函数中定义并返回。
* 同时在 `setup()` 函数中也可以定义修改响应式状态的方法，同时也需要将其返回。
* 手动暴露状态和方法十分繁琐，当使用单文件组件时，可以通过 `<script setup>` 来简化大量代码。
* `reactive()` 方法返回的是一个 Proxy 对象，它和原始对象时不相等的，只有代理对象是响应式的，修改原始对象不会触发更新。
* 对同一个对象调用 `reactive()` 会返回相同的代理对象。
* 在一个代理上调用 `reactive()` 会返回相同的代理对象。
* 响应式对象的嵌套对象仍然是代理对象。
* `reactive()` API 的局限性：
  1. 仅对对象类型有效。
  2. 想要保持响应性，必须保持数据传递时的响应式对象的引用关系。
* 所以，我们在对响应式对象的非对象属性解构赋值或者直接赋值给本地变量时，此属性会失去响应性。
* 为了解决 `reactive()` 带来的限制，Vue 提供了 `ref()` 来允许我们创建任意类型的响应式 ref。
* `ref()` 从参数获取到值，并将其封装为一个带 `value` 属性的 ref 对象。
* ref 的 `.value` 在替换的时候也是具有响应式的。
* ref 在数据传递和解构赋值时都保持其响应性。
* ref 在模板中作为顶层访问时，会被自动解包，所以不需要使用 `.value` 也可以直接访问。
* 如果 ref 作为文本插值的计算最终值，那么也会被自动解包。
* ref 在响应式对象中被访问和修改时也自动解包。
* 将新的 ref 赋值给已经关联了旧的 ref 的属性，那么会替换掉旧的 ref，并失去和旧 ref 的关联。
* 将 ref 赋值给浅层响应式对象的属性时，不会发生解包。
* 当 ref 作为响应式数组或像 Map 这种原生集合类型的元素被访问时，不会进行解包。

#### 计算属性

* 模板表达式中不宜写太多逻辑，应理清所依赖的具体数据，通过计算属性实现逻辑的计算。

##### 选项式

* 通过 `computed` 选项来描述依赖响应式状态的复杂逻辑。
* 计算属性默认仅通过计算函数得出结果，不可修改。当你想修改时，可以通过 `getter` 和 `setter` 来实现。

##### 组合式
* 通过 `computed()` 函数参数为计算函数，返回一个计算属性 ref。
* 默认形参为函数对象，且返回计算结果。当你想修改是，可以通过传入具有 `get` 和 `set` 属性的对象作为形参，来实现修改。

* 计算属性和方法属性对比：计算属性会基于其响应式依赖被缓存，而方法属性总会在重新渲染时调用执行。
* 计算属性的计算函数应只做计算而没用其他副作用，不要在计算函数中做异步请求或更改 DOM。
* 计算属性的返回值应该只视为只读的，永远不应该更改——应该通过更跟依赖的源数据状态来触发新的计算。

#### 类和样式绑定

* Vue 为 `class` 和 `style` 的 `v-bind` 用法提供了特殊的功能增强，除了字符串外，表达式的结果还可以是数组或对象。
* 可以给 `:class` 传递一个对象来动态切换 class，属性名为类名，是否存在此类名由该属性值确定。
* 给 `:class` 传递的对象也可以是响应式对象，其数据结构与上述一致。
* 一个通用且好用的实践是传递一个返回对象的计算属性。
* 可以给 `:class` 传递一个数组来动态实现类名，数组值为响应式数据，数据值为类名。
* 数组内部也可以使用三目运算表达式。
* 数组内也可也使用对象语法。
* 对于只有一个根元素的组件，当你声明此组件的时候使用了类属性，这些类属性会添加到根元素上；如果组件有多个根元素，你可以使用 `$attrs.class`
哪根元素接受这些类属性。
* `:style` 支持绑定 JavaScript 对象值。
* 同样的，通用且好用的事件时传递一个返回对象的计算属性。
* `:style` 同时支持绑定包含多个样式对象的数组。
* 可以对一个样式属性提供一个数组，数组包含多个样式属性值，数组仅会渲染浏览器支持的最后一个值。

#### 条件渲染

* `v-if`、`v-else` 和 `v-else-if` 指令用于按条件渲染一个区块。
* 如果我们想同时将多个区块按条件渲染，可以使用 `<template>` 将多个区块包裹配合上述指令实现。
* `v-show` 指令用于切换元素的 `display` 属性，不支持在 `<template>` 上使用。

#### 列表渲染

* `v-for` 指令用于基于一个数组来渲染一个列表，第一个参数为数组成员，第二个参数为索引。
* 可以在定义 `v-for` 的变量别名时使用解构。
* 也可以用 `v-for` 指令来遍历一个对象的所有属性值，第一个参数为属性值，第二个参数为属性名，第三个参数为索引。
* 也可以传给 `v-for` 一个整数值，初值从 1 开始。
* 可以在 `<template>` 上使用 `v-for`。
* 同时使用 `v-if` 和 `v-for` 是不推荐的。
* Vue 默认按照“就地更新”的策略来更新通过 `v-for` 渲染的元素列表。当数据项的顺序改变时，Vue 不会随之移动 DOM 元素的顺序，而是就地更新每个元素，确保它们在原本指定的索引位置上渲染。
* 为了给 Vue 一个提示，以便它可以跟踪每个节点的标识，从而重用和重新排序现有的元素，你需要为每个项目提供一个唯一的 `:key` 属性。
* 可以在组件上使用 `v-for`，但是这时不会将任何数据传递给组件，为了将迭代后的数据传递到组件中，我们应该使用组件的属性为其传递数据。
* 数组的非变更方法，例如 `filter()`、`concat()` 和 `slice()`，这些方法都不会改变原数组，总是返回一个新的数组，我们需要对响应式数组进行重新赋值，如果这些响应式数组通过
`v-for` 渲染到 DOM 中，当重新赋值时，Vue 会最大程度的对 DOM 元素进行复用。
* 有时，我们希望显示数组经过过滤或排序后的内容，而不实际变更或重置原始数据。在这种情况下，你可以创建返回已过滤或已排序数组的计算属性。
* 在计算属性中使用 `reverse()` 和 `sort()` 请保持谨慎！这两个方法将变更原始数组，计算函数中不应该这么做。请在调用这些方法之前创建一个原数组的副本。

#### 事件处理

* 可以使用 `v-on` 或 `@` 指令来监听 DOM 事件和运行 JavaScript 代码。
* 事件处理器的值可以是内联事件处理器或方法事件处理器。
* 在内联事件处理器中调用方法，允许我们向方法传入自定义参数以代替原生事件。
* 在内联事件处理器中，可以通过传入 `$event` 参数或使用箭头函数传入 `event` 来实现访问事件参数。
* Vue 为 `v-on` 指令提供了事件修饰符。 事件修饰符可以链式调用。
  * `.stop`：停止冒泡传递；
  * `.prevent`：阻止默认事件；
  * `.self`：事件目标为元素本身才触发事件；
  * `.capture`：元素事件触发前被捕获先行处理；
  * `.once`：事件只触发一次；
  * `.passive`：表示监听器永远不会调用组织默认事件；
* 按键修饰符 `@keyup` 用于实现监听按键。
* `.exact` 修饰符允许控制触发一个事件所需的确定组合的系统按键修饰符。
* 鼠标按键修饰符用于实现监听鼠标按键。主要有 `.left`、`.right` 和 `.middle`。

#### 表单输入绑定

* Vue 的 `v-model` 指令帮我们实现了双向绑定。
* 插值表达式在 `<textarea>` 中将不会工作。请使用 `v-model` 来替代。
* 复选框的 `v-model` 将始终包含来自当前选中框的值。
* 单选框的 `v-model` 将始终为选中框的值。
* 单选的选择器的 `v-model` 将始终为选择的 option 的值。
* 多选的选择器的 `v-model` 将始终包含选泽的 option 的值，为一个数组。
* `v-model.lazy` 修饰符的作用是仅当 `change` 事件触发的时候再进行更新，例如失去焦点，按下回车等。
* `v-model.number` 修饰符的作用是将输入的值的类型变为数值类型，具体实现类似 parseFloat(value) || value。
* `v-model.trim` 修饰符用于去除用户输入内容两端的空格。

#### 生命周期

* 选项式 API 注册生命周期钩子
  ```javascript
  export default {
    mounted() {
        console.log(`the component is now mounted.`)
    }
  }
  ```
* 组合式 API 注册生命周期钩子
  ```vue
  <script setup>
  import { onMounted } from 'vue'

  onMounted(() => {
  console.log(`the component is now mounted.`)
  })
  </script>
  ```
* 最常用的钩子有：`mounted`、`updated` 和 `unmounted`。
* 官方文档生命周期图示
![官方文档生命周期图示](https://staging-cn.vuejs.org/assets/lifecycle.16e4c08e.png)

#### 侦听器

* 侦听器用于实现对响应式属性的监听，并在属性变化的时候触发一个函数。

##### 选项式 API

* `watch` 选项的默认值是一个包含一些以响应式属性作为函数名的函数的对象，默认的侦听是浅层的侦听，对嵌套属性无效。
* 如果需要对嵌套属性进行侦听，需要显示的将响应式属性声明为一个对象，这个对象包括 `handler` 处理函数和值为 `true` 的 `deep` 的属性。
* 深度侦听需要遍历被侦听对象中的所有嵌套的属性，当用于大型数据结构时，开销很大。因此请只在必要时才使用它，并且要留意性能。
* `watch` 默认是懒侦听的，仅在侦听源发生变化时才会触发，而有时我们需要在创建侦听的时候就进行触发，声明 `immediate` 属性为 `true` 便能实现创建时立刻触发。
* 当响应式数据发生改变的时候，可能会同时触发 Vue 组件的更新和侦听器函数，默认情况下，侦听器函数的执行会先于 Vue 组件更新，也就是说，此时的侦听器访问的 DOM 是旧的状态。
* 如果想先进行 Vue 组件更新，后触发侦听器函数，可以通过声明 `flush` 属性为 `'post'` 实现。
* 可以通过 `$watch()` 方法命令式地创建一个侦听器。
* 用 watch 选项或者 $watch() 实例方法声明的侦听器，会在宿主组件卸载时自动停止。
* 在少数情况下，你的确需要在组件卸载之前就停止一个侦听器，这时可以调用 $watch() API 返回的函数。

##### 组合式 API

* 组合式 API 可以直接监听一个 `ref`。
* 组合式 API 通过直接声明 watch 函数来实现监听，第一个参数可以为 ref（包括计算属性）、响应式对象、一个 getter 函数或多个来源组成的数组，第二个参数为触发的函数。
* 侦听响应式对象，会隐式地创建一个深层侦听器，可以实现对响应式对象属性的侦听，此时的 `newValue` 和 `oldValue` 是相等的，因为二者是同一个对象。
* 侦听响应式对象的属性必须使用显示的 getter 函数来实现，同时如果想实现引用类型属性的深层侦听，需要显式地声明 `{deep: true}`，此时
`newValue` 和 `oldValue` 也是相等的，除非属性会被整个替换。
* `watch()` 是懒侦听的，仅在侦听员发生变化时才会触发，如果需要在创建侦听的时候就进行触发，可以使用 `watchEffect()` 来实现。
* `watchEffect()` 函数的参数为一个回调函数，`watchEffect()` 会立即执行一遍回调函数，如果这时函数产生了副作用，Vue 会自动追踪副作用的依赖关系，分析出响应式源数据，每当响应式源数据发生变化时，回调函数会再次执行。
* 当响应式数据发生改变的时候，可能会同时触发 Vue 组件的更新和侦听器函数，默认情况下，侦听器函数的执行会先于 Vue 组件更新，也就是说，此时的侦听器访问的 DOM 是旧的状态。
* 如果想先进行 Vue 组件更新，后触发侦听器函数，可以通过在 `watch()` 函数或 `watchEffect()` 函数声明 `{flush: 'post'}` 来实现。
* 后置刷新的 `watchEffect()` 可以简写为 `watchPostEffect()`。
* 在 `setup()` 或 `<script setup>` 中用同步语句创建的侦听器，会自动绑定到宿主组件实例上，并且会在宿主组件卸载时自动停止。因此，在大多数情况下，你无需关心怎么停止一个侦听器。
* 如果用异步回调创建一个侦听器，那么它不会绑定到当前组件上，只能手动通过调用侦听器所返回的函数来停止它。
* 需要异步创建侦听器的情况很少，请尽可能选择同步创建。如果需要等待一些异步数据，你可以使用条件式的侦听逻辑。

#### 模板 ref

* 如果需要直接访问底层的 DOM 元素，可以在模板标签上使用 ref 属性。
* 选项式 API 中，DOM 元素或子组件实例被挂载后，ref 会暴露在 this.$refs 之上。
* 组合式 API 中，DOM 元素或子组件实例被挂载后，要声明一个同名的 ref 来存放该 DOM 元素的引用。
* 模板 ref 在初次渲染时回事 null，因为初次渲染前这个元素还不存在。
* 当 ref 在 `v-for` 中使用时，相应的 ref 中包含的值是一个数组，且 ref 数组不能保证与源数组相同的顺序。
* ref 的属性值还可以是函数，每次组件更新时这个函数都会被调用，函数接受该元素的引用作为第一个参数。
* ref 也可以被用在一个子组件上。此时 ref 中引用的是组件实例。
* 使用选项式 API 时，此时父组件可以访问子组件所有的属性和方法，子组件可以通过 `expose` 选项限制对子组件实例的访问。
* 使用了 `<script setup>` 的组件是默认私有的：父组件无法访问这种子组件的任何属性和方法，子组件可以通过 defineExpose 显式暴露包含属性和方法的对象。
* 在组合式 API 中，父组件通过模板 ref 获取到子组件实例时，得到的实例会自动解包。

#### 组件基础

* 在选项式 API 中，组件需要进行显式的声明，才能进行使用，而在组合式 API 中，可以直接使用引用的组件。
* 在选项式 API 中，组件传参可以通过 `props` 选项进行定义参数，同时可以使用 `this` 在子组件调用传入参数。
* 在使用 `<script setup>` 的单文件组件中，通过调用 `defineProps()` 函数来注册传递的参数，函数的参数为自定义参数名的字符串组成的数组，返回值为一个包含传递给组件所有参数的对象。
* 在没用使用 `<script setup>` 且使用 `setup()` 函数时，传递给组件的参数对象会作为 `setup()` 函数的第一个参数。
* 组件参数注册后可以在使用组件的时候传递所注册的参数。
* 在选项式 API 中，可以通过在子组件 emits 选项中声明监听事件字符串名称数组，配合子组件 `$emit()` 和使用组件时的 `@` 指令，实现子组件触发父组件事件。
* 在使用 `<script setup>` 的单文件组件中，通过调用 `defineEmits()` 函数来注册传递的事件，函数的参数为自定义事件名的字符串组成的数组，可以通过子组件模板中的 `$emit()`
或显式的在 `<script setup>` 中调用 `defineEmits()` 返回的函数来配合使用组件时的 `@` 指令，实现子组件触发父组件事件。
* 如果你没有在使用 `<script setup>`，可以从 setup() 函数的第二个参数，即 setup 上下文对象上访问到 emit 函数，实现子组件触发父组件事件。
* 可以在定义组件的时候使用 `<slot />` 实现插槽功能。
* 通过 Vue 的 `<component>` 元素和特殊的 `is` 属性实现动态组件，`is` 的值可以为被注册的组件名或导入的组件对象。
* 当使用 `<component :is="...">` 来在多个组件间作切换时，组件会在被切换掉后卸载。我们可以通过 `<KeepAlive>` 组件强制不活跃的组件仍然保持“存活”的状态。

### 深入组件