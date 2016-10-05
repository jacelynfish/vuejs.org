---
title: Components
type: guide
order: 11
---

## What are Components?
## 组件是什么？

Components are one of the most powerful features of Vue. They help you extend basic HTML elements to encapsulate reusable code. At a high level, components are custom elements that Vue's compiler attaches behavior to. In some cases, they may also appear as a native HTML element extended with the special `is` attribute.
组件是 Vue 最强大的特性之一。组件可以扩展 HTML 元素封装成可复用的代码。从高一点的层面上讲，组件是自定义元素，Vue 编译器为它添加了特殊功能。在某些情况下，组件也可以是原生 HTML 元素形式，以 `is` 特性扩展。

## Using Components
## 使用组件

### Registration
### 注册

We've learned in the previous sections that we can create a new Vue instances with:
在先前章节我们知道了可以这样创建一个 Vue 实例：

``` js
new Vue({
  el: '#some-element',
  // options
})
```
``` js
new Vue({
  el: '#some-element',
  // 选项
})
```

To register a global component, you can use `Vue.component(tagName, options)`. For example:
可以用 `Vue.component(tagName, options)` 注册一个全局的组件：

``` js
Vue.component('my-component', {
  // options
})
```
``` js
Vue.component('my-component', {
  // 选项
})
```

<p class="tip">Note that Vue does not enforce the [W3C rules](http://www.w3.org/TR/custom-elements/#concepts) for custom tag names (all-lowercase, must contain a hyphen) though following this convention is considered good practice.</p>
<p class="tip">注意，Vue 并不强制要求遵循 [W3C 规则](http://www.w3.org/TR/custom-elements/#concepts) (全小写，必须包含一个短横杠) 来命名标签名，尽管遵循这个规则比较好。</p>

Once registered, a component can be used in an instance's template as a custom element, `<my-component></my-component>`. Make sure the component is registered **before** you instantiate the root Vue instance. Here's the full example:
一旦组件注册完毕，就可以在模板中以自定义元素 `<my-component></my-component>` 的形式使用了。要确保在初始化根实例 **之前** 注册好组件：

``` html
<div id="example">
  <my-component></my-component>
</div>
```
``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// register
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// create a root instance
new Vue({
  el: '#example'
})
```

``` js
// 注册组件
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// 创建根实例
new Vue({
  el: '#example'
})
```

Which will render:
渲染为：

``` html
<div id="example">
  <div>A custom component!</div>
</div>
```
``` html
<div id="example">
  <div>A custom component!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}
{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

### Local Registration
### 局部注册

You don't have to register every component globally. You can make a component available only in the scope of another instance/component by registering it with the `components` instance option:
并不需要全局注册每一个组件，可以让组件只能用在其它组件或实例内，通过实例选项  `components` 注册：

``` js
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> will only be available in parent's template
    'my-component': Child
  }
})
```
``` js
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> 只能用在父组件模板内
    'my-component': Child
  }
})
```

The same encapsulation applies for other registerable Vue features, such as directives.
这种封装也可以用在其它 Vue 特性中，比如说指令（directive）。

### DOM Template Parsing Caveats
### 模板解析

When using the DOM as your template (e.g. using the `el` option to mount an element with existing content), you will be subject to some restrictions that are inherent to how HTML works, because Vue can only retrieve the template content **after** the browser has parsed and normalized it. Most notably, some elements such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<option>` can only appear inside certain other elements.
由于 Vue 只能在浏览器解析完成 **之后** 取到模板内容，所以必须要遵从 HTML 元素的限制。其中最显著的是，`<ul>` ， `<ol>` ， `<table>` 和 `<select>` 这些元素限制了其内部可以包含的元素，一些像 `<option>` 这样的元素只能放在特定的一些元素内。

This will lead to issues when using custom components with elements that have such restrictions, for example:
这些限制会导致一些问题：

``` html
<table>
  <my-row>...</my-row>
</table>
```
``` html
<table>
  <my-row>...</my-row>
</table>
```

The custom component `<my-row>` will be hoisted out as invalid content, thus causing errors in the eventual rendered output. A workaround is to use the `is` special attribute:
`<my-row>` 组件会被作为空白内容前置，从而导致渲染错误。一个变通方法是加上 `is` 特性：

``` html
<table>
  <tr is="my-row"></tr>
</table>
```
``` html
<table>
  <tr is="my-row"></tr>
</table>
```

**It should be noted that these limitations do not apply if you are using string templates from one of the following sources**:
**注意，在使用以下来源的模板时上述限制不会生效**：

- `<script type="text/x-template">`
- JavaScript inline template strings
- `.vue` components
- `<script type="text/x-template">`
- 行内JavaScript模板
- `<script type="text/x-template">`

Therefore, prefer using string templates whenever possible.
因此，无论何时都尽可能地使用字符串模板是更好的选择。

### `data` Must Be a Function
### `data` 必须是一个函数

Most of the options that can be passed into the Vue constructor can be used in a component, with one special case: `data` must be function. In fact, if you try this:
大多数可以传进 Vue 构造器的选项也都可以使用在组件中，只有一个特例： `data` 必须是一个函数：

``` js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```
``` js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```

Then Vue will halt and emit warnings in the console, telling you that `data` must be a function for component instances. It's good to understand why the rules exist though, so let's cheat.
Vue 会停止运行并在控制台中发出警告，告诉你 `data` 应用在组件实例中时必须是一个函数。为了理解为什么会存在这样的规定：

``` html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```
``` html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

``` js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // data is technically a function, so Vue won't
  // complain, but we return the same object
  // reference for each component instance
  data: function () {
    return data
  }
})

new Vue({
  el: '#example-2'
})
```
``` js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // data 严格意义上是一个函数，所以 Vue 不会报错
  // 但其实每个组件实例返回的都是同一个对象的引用
  data: function () {
    return data
  }
})

new Vue({
  el: '#example-2'
})
```

{% raw %}
<div id="example-2" class="demo">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
<script>
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
</script>
{% endraw %}
{% raw %}
<div id="example-2" class="demo">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
<script>
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
</script>
{% endraw %}

Since all three component instances share the same `data` object, incrementing one counter increments them all! Ouch. Let's fix this by instead returning a fresh data object:
因为这三个组件共享同一个 `data` 对象，一个计数器上加一就等于所有计数器都加一！由函数返回一个全新的 `data` 对象可以解决这个问题：

``` js
data: function () {
  return {
    counter: 0
  }
}
```
``` js
data: function () {
  return {
    counter: 0
  }
}
```

Now all our counters each have their own internal state:
现在所有的计数器都有了自己的内部计数状态：

{% raw %}
<div id="example-2-5" class="demo">
  <my-component></my-component>
  <my-component></my-component>
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
new Vue({
  el: '#example-2-5'
})
</script>
{% endraw %}
{% raw %}
<div id="example-2-5" class="demo">
  <my-component></my-component>
  <my-component></my-component>
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
new Vue({
  el: '#example-2-5'
})
</script>
{% endraw %}

The `el` option also requires a function value when used in a component instance, for exactly the same reason.
同理，`el` 选项用在组件实例中也要求必须是一个函数。

### Composing Components
### 构成组件

Components are meant to be used together, most commonly in parent-child relationships: component A may use component B in its own template. They inevitably need to communicate to one another: the parent may need to pass data down to the child, and the child may need to inform the parent of something that happened in the child. However, it is also very important to keep the parent and the child as decoupled as possible via a clearly-defined interface. This ensures each component's code can be written and reasoned about in relative isolation, thus making them more maintainable and potentially easier to reuse.
像是组件A把组件B用在自己的模板中这样的多组件使用十分常见，这种情况下不可避免地会进行父子组件间通信，父组件可能需要向下给子组件传递数据，子组件可能需要向父组件确认子组件里发生了什么事。然而，非常重要的一点是，要尽可能地使父子组件解耦，这样可以确保每个组件的代码都是在独立的作用域内编写的，也使得组件更容易维护和复用。

In Vue.js, the parent-child component relationship can be summarized as **props down, events up**. The parent passes data down to the child via **props**, and the child sends messages to the parent via **events**. Let's see how they work next.
在 Vue.js 中，父子组件间的通信可以简单概括为 **通过 props 向下通信，通过 events 向上通信** 。父组件通过 **props** 向子组件传递数据，子组件通过 **events** 向父组件传递消息。来看一下它们是如何工作的：

<p style="text-align: center">
  <img style="width:300px" src="/images/props-events.png" alt="props down, events up">
</p>
<p style="text-align: center">
  <img style="width:300px" src="/images/props-events.png" alt="props down, events up">
</p>

## Props
## Props

### Passing Data with Props
### 使用 Props 传递数据

Every component instance has its own **isolated scope**. This means you cannot (and should not) directly reference parent data in a child component's template. Data can be passed down to child components using **props**.
每个组件实例都有自己 **独立的作用域** ，这意味着不能并且不该在子组件的模板里直接引用父组件的数据。由父组件向子组件传递数据可以通过 **prop** 来完成。

A prop is a custom attribute for passing information from parent components. A child component needs to explicitly declare the props it expects to receive using the [`props` option](/api/#props):
prop 是组件数据的一个字段。子组件需要显式地用 [`props` 选项](/api/#props) 来明确声明 prop:

``` js
Vue.component('child', {
  // declare the props
  props: ['message'],
  // just like data, the prop can be used inside templates
  // and is also made available in the vm as this.message
  template: '<span>{{ message }}</span>'
})
```
``` js
Vue.component('child', {
  // 声明 props
  props: ['message'],
  // prop 可以像 data 一样在模板里使用
  // prop 也可以用 this.message 来声明
  template: '<span>{{ message }}</span>'
})
```

Then we can pass a plain string to it like so:
然后传入一个普通字符串：

``` html
<child message="hello!"></child>
```
``` html
<child message="hello!"></child>
```

Result:
结果：

{% raw %}
<div id="prop-example-1" class="demo">
  <child message="hello!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['message'],
      template: '<span>{{ message }}</span>'
    }
  }
})
</script>
{% endraw %}
{% raw %}
<div id="prop-example-1" class="demo">
  <child message="hello!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['message'],
      template: '<span>{{ message }}</span>'
    }
  }
})
</script>
{% endraw %}

### camelCase vs. kebab-case
### camelCase vs. kebab-case

HTML attributes are case-insensitive, so when using non-string templates, camelCased prop names need to use their kebab-case (hyphen-delimited) equivalents:
HTML 特性不区分大小写，名字形式为 camelCase 的 prop 用作特性时，需要转为 kebab-case（短横线隔开）：

``` js
Vue.component('child', {
  // camelCase in JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case in HTML -->
<child my-message="hello!"></child>
```
``` js
Vue.component('child', {
  // camelCase in JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case in HTML -->
<child my-message="hello!"></child>
```

Again, if you're using string templates, then this limitation does not apply.
再强调一次，使用字符串模板的时候那些 html 元素的限制不会生效。

### Dynamic Props
### 动态 Props

Similar to binding a normal attribute to an expression, we can also use `v-bind` for dynamically binding props to data on the parent. Whenever the data is updated in the parent, it will also flow down to the child:
类似于用 `v-bind` 绑定属性到表达式，也可以这样用 `v-bind` 绑定动态的 prop 到父组件的数据上。无论何时父组件的数据发生变化，都会传递给子组件：

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```
``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

It's often simpler to use the shorthand syntax for `v-bind`:
使用 `v-bind` 的缩写语法会更简单：

``` html
<child :my-message="parentMsg"></child>
```
``` html
<child :my-message="parentMsg"></child>
```

Result:
结果：

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}
{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}

### Literal vs Dynamic
### 字面量语法 vs 动态语法

A common mistake beginners tend to make is attempting to pass down a number using the literal syntax:
初学者容易犯的一个常见错误就是使用字面量传递数值：

``` html
<!-- this passes down a plain string "1" -->
<comp some-prop="1"></comp>
```
``` html
<!-- 传递了字符串 "1" -->
<comp some-prop="1"></comp>
```

However, since this is a literal prop, its value is passed down as a plain string `"1"` instead of an actual number. If we want to pass down an actual JavaScript number, we need to use `v-bind` so that its value is evaluated as a JavaScript expression:
然而，既然传递的是一个字面 prop ，它的值以字符串 `"1"` 的形式传递，而不是一个数值。如果想要传递一个实际的 JavaScript 数字的话，需要使用 `v-bind` 这种动态语法，从而使它被当做 JavaScript 表达式计算。

``` html
<!-- this passes down an actual number -->
<comp v-bind:some-prop="1"></comp>
```
``` html
<!-- 传递了实际的数字 -->
<comp v-bind:some-prop="1"></comp>
```

### One-Way Data Flow
### 单向数据流

All props form a **one-way-down** binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around. This prevents child components from accidentally mutating the parent's state, which can make your app's data flow harder to reason about.
所有的 props 都是 **单向** 绑定的：父组件的属性变化时，会传递到子组件，但返过来并不会。这就避免子组件突然改变了父组件的状态，这种改变会让应用的数据流难以追溯。

In addition, every time the parent component is updated, all props in the child component will be refreshed with the latest value. This means you should **not** attempt to mutate a prop inside a child component. If you do, Vue will warn you in the console.
每次更新父组件，子组件的所有 prop 值都会被更新成最新值，因此 **不** 应该在子组件内改变 prop 的值，否则 Vue 会在控制台抛出警告。

There are usually two cases where it's tempting to mutate a prop:
通常有这两种场景可能会需要修改 prop 的值：


1. The prop is used to only pass in an initial value, the child component simply wants to use it as a local data property afterwards;
1. prop 只是用来传递初始值，子组件想用这个 prop 字段来作为自己组件内的本地数据；

2. The prop is passed in as a raw value that needs to be transformed.
2. prop 以需要被转换的初始值来传递。

The proper answer to these use cases are:
对这些场景的处理方式是：

1. Define a local data property that uses the prop's initial value as its initial value;
1. 定义一个本地数据属性，它的初始值就是 prop 的初始值；

2. Define a computed property that is computed from the prop's value.
2. 定义一个用 prop 值计算的计算属性。

<p class="tip">Note that objects and arrays in JavaScript are passed by reference, so if the prop is an array or object, mutating the object or array itself inside the child **will** affect parent state.</p>
<p class="tip">如果 prop 是数组或对象的话，要注意它们是按引用传递的，在子组件内修改 **会** 影响父组件的状态。</p>

### Prop Validation
### Prop 验证

It is possible for a component to specify requirements for the props it is receiving. If a requirement is not met, Vue will emit warnings. This is especially useful when you are authoring a component that is intended to be used by others.
组件可以为 prop 指定验证要求，如果没有满足要求，Vue 会抛出警告。当组件给其他人使用时， prop 验证非常有用。

Instead of defining the props as an array of strings, you can use an object with validation requirements:
prop 除了可以定义为字符串数组，也可以为包含验证条件的对象：

``` js
Vue.component('example', {
  props: {
    // basic type check (`null` means accept any type)
    propA: Number,
    // multiple possible types
    propB: [String, Number],
    // a required string
    propC: {
      type: String,
      required: true
    },
    // a number with default value
    propD: {
      type: Number,
      default: 100
    },
    // object/array defaults should be returned from a
    // factory function
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // custom validator function
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```
``` js
Vue.component('example', {
  props: {
    // 基本类型验证（`null` 意思是任何类型都可以）
    propA: Number,
    // 多种类型
    propB: [String, Number],
    // 必需项且必须是字符串
    propC: {
      type: String,
      required: true
    },
    // 数字，有默认值
    propD: {
      type: Number,
      default: 100
    },
    // 对象/数组的默认值应由函数返回
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

The `type` can be one of the following native constructors:
`type` 可以是以下原生构造器：

- String
- Number
- Boolean
- Function
- Object
- Array
- String
- Number
- Boolean
- Function
- Object
- Array

In addition, `type` can also be a custom constructor function and the assertion will be made with an `instanceof` check.
`type` 也可以是自定义函数，使用 `instanceof` 校验。

When a prop validation fails, Vue will refuse to set the value on the child component and throw a warning if using the development build.
当 prop 验证失败，Vue 会拒绝在子组件上设置此值，使用开发版本的话还会抛出警告。

## Custom Events
## 自定义事件

We have learned that the parent can pass data down to the child using props, but how do we communicate back to the parent when something happens? This is where custom events come in.
在之前章节已经知道，父组件可以使用 prop 传递数据到子组件，但是反过来子组件向父组件通信该怎么做呢？是时候让自定义事件上场了。

### Using `v-on` with Custom Events
### 使用 `v-on` 绑定自定义事件

Every Vue instance implements the [Events interface](/api/#Instance-Methods-Events), which means it can:
Vue 实例实现了 [自定义事件接口](/api/#Instance-Methods-Events)：

- Listen to an event using `$on(eventName)`
- 通过 `$on(eventName)` 监听事件
- Trigger an event using `$emit(eventName)`
- 通过 `$emit(eventName)` 触发事件

In addition, a parent component can listen to the events emitted from a child component using `v-on` directly in the template where the child component is used.
除此之外，父组件也可以监听模板中直接使用 `v-on` 的子组件发出的事件。


Here's an example:
如下例：

``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

``` js
Vue.component('button-counter', {
  template: '<button v-on:click="increment">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    increment: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```
``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

``` js
Vue.component('button-counter', {
  template: '<button v-on:click="increment">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    increment: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```

{% raw %}
<div id="counter-event-example" class="demo">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
<script>
Vue.component('button-counter', {
  template: '<button v-on:click="increment">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    increment: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>
{% endraw %}
{% raw %}
<div id="counter-event-example" class="demo">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
<script>
Vue.component('button-counter', {
  template: '<button v-on:click="increment">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    increment: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>
{% endraw %}

In this example, it's important to note that the child component is still completely decoupled from what happens outside of it. All it does is report information about its own activity, just in case a parent component might care.
在上面这个例子中值得注意的一点是，子组件仍然与外部发生的事情完全解耦的，子组件做的事仅仅是向父组件报告自己组件内的活动。

#### Binding Native Events to Components
#### 在组件上绑定原生事件

There may be times when you want to listen for a native event on the root element of a component. In these cases, you can use the `.native` modifier for `v-on`. For example:
可能经常需要在组件的根元素上监听原生事件，这种情况下可以在 `v-on` 上添加一个 `.native` 修饰器，比如：

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```
``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

### Form Input Components using Custom Events
### 使用自定义事件的表单输入组件

This strategy can also be used to create custom form inputs that work with `v-model`. Remember:
可以创建一个与 `v-model` 搭配使用的自定义表单输入组件：

``` html
<input v-model="something">
```
``` html
<input v-model="something">
```

is just syntactic sugar for:
这只是下面语句的语法糖：

``` html
<input v-bind:value="something" v-on:input="something = $event.target.value">
```
``` html
<input v-bind:value="something" v-on:input="something = $event.target.value">
```

When used with a component, this simplifies to:
与组件一起使用时就会简化为：

``` html
<input v-bind:value="something" v-on:input="something = arguments[0]">
```
``` html
<input v-bind:value="something" v-on:input="something = arguments[0]">
```

So for a component to work with `v-model`, it must:
因此，对于一个与 `v-model` 一起使用的组件，它必须满足如下条件：

- accept a `value` prop
- 接收一个 prop `value`
- emit an `input` event with the new value
- 有新的 value 值时发出一个 `input` 事件

Let's see it in action:

``` html
<div id="v-model-example">
  <p>{{ message }}</p>
  <my-input
    label="Message"
    v-model="message"
  ></my-input>
</div>
```
``` html
<div id="v-model-example">
  <p>{{ message }}</p>
  <my-input
    label="Message"
    v-model="message"
  ></my-input>
</div>
```

``` js
Vue.component('my-input', {
  template: '\
    <div class="form-group">\
      <label v-bind:for="randomId">{{ label }}:</label>\
      <input v-bind:id="randomId" v-bind:value="value" v-on:input="onInput">\
    </div>\
  ',
  props: ['value', 'label'],
  data: function () {
    return {
      randomId: 'input-' + Math.random()
    }
  },
  methods: {
    onInput: function (event) {
      this.$emit('input', event.target.value)
    }
  },
})

new Vue({
  el: '#v-model-example',
  data: {
    message: 'hello'
  }
})
```
``` js
Vue.component('my-input', {
  template: '\
    <div class="form-group">\
      <label v-bind:for="randomId">{{ label }}:</label>\
      <input v-bind:id="randomId" v-bind:value="value" v-on:input="onInput">\
    </div>\
  ',
  props: ['value', 'label'],
  data: function () {
    return {
      randomId: 'input-' + Math.random()
    }
  },
  methods: {
    onInput: function (event) {
      this.$emit('input', event.target.value)
    }
  },
})

new Vue({
  el: '#v-model-example',
  data: {
    message: 'hello'
  }
})
```

{% raw %}
<div id="v-model-example" class="demo">
  <p>{{ message }}</p>
  <my-input
    label="Message"
    v-model="message"
  ></my-input>
</div>
<script>
Vue.component('my-input', {
  template: '\
    <div class="form-group">\
      <label v-bind:for="randomId">{{ label }}:</label>\
      <input v-bind:id="randomId" v-bind:value="value" v-on:input="onInput">\
    </div>\
  ',
  props: ['value', 'label'],
  data: function () {
    return {
      randomId: 'input-' + Math.random()
    }
  },
  methods: {
    onInput: function (event) {
      this.$emit('input', event.target.value)
    }
  },
})
new Vue({
  el: '#v-model-example',
  data: {
    message: 'hello'
  }
})
</script>
{% endraw %}
{% raw %}
<div id="v-model-example" class="demo">
  <p>{{ message }}</p>
  <my-input
    label="Message"
    v-model="message"
  ></my-input>
</div>
<script>
Vue.component('my-input', {
  template: '\
    <div class="form-group">\
      <label v-bind:for="randomId">{{ label }}:</label>\
      <input v-bind:id="randomId" v-bind:value="value" v-on:input="onInput">\
    </div>\
  ',
  props: ['value', 'label'],
  data: function () {
    return {
      randomId: 'input-' + Math.random()
    }
  },
  methods: {
    onInput: function (event) {
      this.$emit('input', event.target.value)
    }
  },
})
new Vue({
  el: '#v-model-example',
  data: {
    message: 'hello'
  }
})
</script>
{% endraw %}

This interface can be used not only to connect with form inputs inside a component, but also to easily integrate input types that you invent yourself. Imagine these possibilities:
这一特性不只可用于联系组件内表单 input 项，也可以更容易地统一自己定义的 input 类型，想象一下这种情况：

``` html
<voice-recognizer v-model="question"></voice-recognizer>
<webcam-gesture-reader v-model="gesture"></webcam-gesture-reader>
<webcam-retinal-scanner v-model="retinalImage"></webcam-retinal-scanner>
```
``` html
<voice-recognizer v-model="question"></voice-recognizer>
<webcam-gesture-reader v-model="gesture"></webcam-gesture-reader>
<webcam-retinal-scanner v-model="retinalImage"></webcam-retinal-scanner>
```

### Non Parent-Child Communication
### 非父子组件通信

Sometimes two components may need to communicate with one-another but they are not parent/child to each other. In simple scenarios, you can use an empty Vue instance as a central event bus:
有时候可能两个非父子关系的组件也需要通信，情景简单的话可以把一个空的 Vue 实例作为中心事件 bus ：

``` js
var bus = new Vue()
```
``` js
var bus = new Vue()
```
``` js
// in component A's method
bus.$emit('id-selected', 1)
```
``` js
// 组件A中的方法
bus.$emit('id-selected', 1)
```
``` js
// in component B's created hook
bus.$on('id-selected', function (id) {
  // ...
})
```
``` js
// 组件B中创建的钩子
bus.$on('id-selected', function (id) {
  // ...
})
```

In more complex cases, you should consider employing a dedicated [state-management pattern](/guide/state-management.html).
如果是更复杂的场景，就要考虑引入专用的 [状态管理模式](/guide/state-management.html)了。

## Content Distribution with Slots
## 使用 Slot 分发内容

When using components, it is often desired to compose them like this:
使用组件时，常常要像这样组合它们：

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```
``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

There are two things to note here:
注意两点：

1. The `<app>` component does not know what content may be present inside its mount target. It is decided by whatever parent component that is using `<app>`.
1. `<app>` 组件并不知道它的挂载点会有什么内容，这取决于 `<app>` 的父组件。

2. The `<app>` component very likely has its own template.
2. `<app>` 组件很可能有它自己的模板。

To make the composition work, we need a way to interweave the parent "content" and the component's own template. This is a process called **content distribution** (or "transclusion" if you are familiar with Angular). Vue.js implements a content distribution API that is modeled after the current [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the special `<slot>` element to serve as distribution outlets for the original content.
为了让组件可以组合使用，我们需要一种方式混合父组件的内容和子组件自己的模板。 这个过程称之为 **内容分发** （或 “transclusion”，如果你熟悉 Angular）。 Vue.js 实现了一个内容分发API，参照了当前的 [Web 组件规范草案](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)，使用特殊的 `<slot>` 元素作为原始内容的插槽。

### Compilation Scope
### 编译作用域

Before we dig into the API, let's first clarify which scope the contents are compiled in. Imagine a template like this:
在深入研究API之前，需要先搞清楚内容的编译作用域。假定模板为：

``` html
<child-component>
  {{ message }}
</child-component>
```
``` html
<child-component>
  {{ message }}
</child-component>
```

Should the `message` be bound to the parent's data or the child data? The answer is the parent. A simple rule of thumb for component scope is:
`messgae` 应该绑定在父组件还是子组件的数据里？答案是父组件。组件作用域有一个简单的规则：

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in child scope.
> 父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译。

A common mistake is trying to bind a directive to a child property/method in the parent template:
一个常见错误是试图在父组件模板内把指令绑定到子组件的属性/方法：

``` html
<!-- does NOT work -->
<child-component v-show="someChildProperty"></child-component>
```
``` html
<!-- 无效 -->
<child-component v-show="someChildProperty"></child-component>
```

Assuming `someChildProperty` is a property on the child component, the example above would not work. The parent's template is not aware of the state of a child component.
假设 `someChildProperty` 是子组件的属性，上例不会如预期一样运行。父组件并不清楚子组件的状态。

If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:
如果要绑定子组件的指令到一个组件的根节点的话，应该在它的模板内这样做：

``` js
Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```
``` js
Vue.component('child-component', {
  // 有效，因为是在正确的作用域内
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Similarly, distributed content will be compiled in the parent scope.
同样，分发内容是在父组件作用域内。

### Single Slot
### 单个 Slot

Parent content will be **discarded** unless the child component template contains at least one `<slot>` outlet. When there is only one slot with no attributes, the entire content fragment will be inserted at its position in the DOM, replacing the slot itself.
除非子组件内包含 `<slot>` ，父组件的内容将被 **抛弃**。如果子组件内只有一个没有特性的 slot 的话，父组件的整个内容将插入到 slot 在的地方并替换它。

Anything originally inside the `<slot>` tags is considered **fallback content**. Fallback content is compiled in the child scope and will only be displayed if the hosting element is empty and has no content to be inserted.
`<slot>` 标签内的内容视为 **回退内容**，在子组件作用域内编译，并且只在宿主元素为空或没有内容供插入时才显示这个回退内容。

Suppose we have a component called `my-component` with the following template:
假定 `my-component` 组件有以下模板：

``` html
<div>
  <h2>I'm the child title</h2>
  <slot>
    This will only be displayed if there is no content
    to be distributed.
  </slot>
</div>
```
``` html
<div>
  <h2>I'm the child title</h2>
  <slot>
    如果没有分发内容则显示我。
  </slot>
</div>
```

And a parent that uses the component:
父组件模板

``` html
<div>
  <h1>I'm the parent title</h1>
  <my-component>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </my-component>
</div>
```
``` html
<div>
  <h1>I'm the parent title</h1>
  <my-component>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </my-component>
</div>
```

The rendered result will be:
渲染结果为：

``` html
<div>
  <h1>I'm the parent title</h1>
  <div>
    <h2>I'm the child title</h2>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </div>
</div>
```
``` html
<div>
  <h1>I'm the parent title</h1>
  <div>
    <h2>I'm the child title</h2>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </div>
</div>
```

### Named Slots
### 具名 Slot

`<slot>` elements have a special attribute, `name`, which can be used to further customize how content should be distributed. You can have multiple slots with different names. A named slot will match any element that has a corresponding `slot` attribute in the content fragment.
`<slot>` 元素可用 `name` 特性来自定义如何分发内容，多个 slot 可以有不同的名字，具名 slot 会匹配内容片断中有相应 `slot` 的元素。

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all outlet for any unmatched content. If there is no default slot, unmatched content will be discarded.
仍然会有一个匿名 slot 来作为 **默认 slot** 作为所有找不到匹配的内容片段的回退插槽。如果没有默认 slot 的话，那些找不到匹配的内容片段将被抛弃。

For example, suppose we have an `app-layout` component with the following template:
举例来说，假定 `app-layout` 有以下模板：

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```
``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Parent markup:
父组件模板：

``` html
<app-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</app-layout>
```
``` html
<app-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</app-layout>
```

The rendered result will be:
渲染结果为：

``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```
``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

The content distribution API is a very useful mechanism when designing components that are meant to be composed together.
组合组件时，内容分发 API 是非常有用的机制。

## Dynamic Components
## 动态组件

You can use the same mount point and dynamically switch between multiple components using the reserved `<component>` element and dynamically bind to its `is` attribute:
多个组件可以使用同一挂载点，绑定上 `is` 属性，然后动态地在它们之间切换：

``` js
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```
``` js
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component v-bind:is="currentView">
  <!-- component changes when vm.currentView changes! -->
</component>
```
``` html
<component v-bind:is="currentView">
  <!-- 组件在 vm.currentview 变化时改变 -->
</component>
```

If you prefer, you can also bind directly to component objects:
也可以直接在组件上绑定：

``` js
var Home = {
  template: '<p>Welcome home!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```
``` js
var Home = {
  template: '<p>Welcome home!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

### `keep-alive`
### `keep-alive`

If you want to keep the switched-out components in memory so that you can preserve their state or avoid re-rendering, you can wrap a dynamic component in a `<keep-alive>` element:
如果把切换出去的组件保留在内存中，以保留它的状态或避免重新渲染，可以用 `<keep-alive>` 元素包装动态组件：

``` html
<keep-alive>
  <component :is="currentView">
    <!-- inactive components will be cached! -->
  </component>
</keep-alive>
```
``` html
<keep-alive>
  <component :is="currentView">
    <!-- 非活动组件将被缓存 -->
  </component>
</keep-alive>
```

Check out more details on `<keep-alive>` in the [API reference](/api/#keep-alive).
在 [参考 api](/api/#keep-alive) 中可以了解更多 `<keep-alive>` 的内容。

## Misc
## 杂项

### Authoring Reusable Components
### 编写可复用组件

When authoring components, it's good to keep in mind whether you intend to reuse it somewhere else later. It's OK for one-off components to be tightly coupled, but reusable components should define a clean public interface and make no assumptions about the context it's used in.
编写组件时，记住是否要复用组件有好处，一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口，不要妄自假设组件所被使用的上下文环境。

The API for a Vue component comes in three parts - props, events, and slots:
Vue 组件 API 来自于 prop，event 和 slot 三部分：

- **Props** allow the external environment to pass data into the component
- **prop** 允许外部环境传递数据给组件；

- **Events** allow the component to trigger side effects in the external environment
- **event** 允许组件触发外部环境的活动；

- **Slots** allow the external environment to compose the component with extra content.
- **slot** 允许外部环境插入内容到组件内。

With the dedicated shorthand syntaxes for `v-bind` and `v-on`, the intents can be clearly and succinctly conveyed in the template:
使用 `v-bind` 和 `v-on` 的简写语法，模板的缩进清晰而简洁：

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```
``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### Child Component Refs
### 子组件索引

Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you have to assign a reference ID to the child component using `ref`. For example:
尽管有 props 和 events，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 `v-ref` 为子组件指定一个索引 ID ：

``` html
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```
``` html
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// access child component instance
var child = parent.$refs.profile
```
``` js
var parent = new Vue({ el: '#parent' })
// 访问子组件
var child = parent.$refs.profile
```

When `ref` is used together with `v-for`, the ref you get will be an array or an object containing the child components mirroring the data source.
`ref` 与 `v-for` 一起使用时 ref 是一个数组或对象，包含相应的子组件。

<p class="tip">`$refs` are only populated after the component has been rendered, and it is not reactive. It is only meant as an escape hatch for direct child manipulation - you should avoid using `$refs` in templates or computed properties.</p>
<p class="tip">`$refs` 在组件渲染完成后才会存在，也不是响应式的。它仅仅是一个直接操作子组件的应急手段，因此要避免在模板或计算属性中使用。</p>

### Async Components
### 异步组件

In large applications, we may need to divide the app into smaller chunks and only load a component from the server when it's actually needed. To make that easier, Vue allows you to define your component as a factory function that asynchronously resolves your component definition. Vue will only trigger the factory function when the component actually needs to be rendered and will cache the result for future re-renders. For example:
在大型应用中，我们可能需要将应用拆分为小块，只在需要时才从服务器下载。为了让事情更简单，Vue 允许把组件定义为一个工厂函数，异步地解析组件的定义。Vue 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。例如：

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```
``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

The factory function receives a `resolve` callback, which should be called when you have retrieved your component definition from the server. You can also call `reject(reason)` to indicate the load has failed. The `setTimeout` here is simply for demonstration; How to retrieve the component is entirely up to you. One recommended approach is to use async components together with [Webpack's code-splitting feature](http://webpack.github.io/docs/code-splitting.html):
工厂函数接收一个 `resolve` 回调，在收到从服务器下载的组件定义时调用。也可以调用 `reject(reason)` 指示加载失败。 `setTimeout` 在这里指示为了演示；怎么获取组件完全取决于你。推荐配合使用 [Webpack 的代码分割功能](http://webpack.github.io/docs/code-splitting.html)：

``` js
Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
```
``` js
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 require 语法告诉 webpack 自动将编译后的代码分割成不同的块，
  // 这些块将通过 ajax 请求自动下载。
  require(['./my-async-component'], resolve)
})
```

You can also return a `Promise` in the resolve function, so with Webpack 2 + ES2015 syntax you can do:
也可以在回调函数中返回 `Promise` ，在 Webpack 2 与 ES2015 语法配合使用下：

``` js
Vue.component(
  'async-webpack-example',
  () => System.import('./my-async-component')
)
```
``` js
Vue.component(
  'async-webpack-example',
  () => System.import('./my-async-component')
)
```

### Component Naming Conventions
### 组件命名规定

When registering components (or props), you can use kebab-case, camelCase, or TitleCase. Vue doesn't care.
Vue 并不关心组件命名是按照 kebab-case ， camelCase ，还是 TitleCase 。

``` js
// in a component definition
components: {
  // register using camelCase
  'kebab-cased-component': { /* ... */ },
  'camelCasedComponent': { /* ... */ },
  'TitleCasedComponent': { /* ... */ }
}
```
``` js
// 在组件定义中
components: {
  // 使用 camelCase 形式注册
  'kebab-cased-component': { /* ... */ },
  'camelCasedComponent': { /* ... */ },
  'TitleCasedComponent': { /* ... */ }
}
```

Within HTML templates though, you have to use the kebab-case equivalents:
在 HTML 模板中必须使用 kebab-case 命名：

``` html
<!-- 在 HTML 模板中永远都要用 kebab-case 形式命名 -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<title-cased-component></title-cased-component>
```

When using _string_ templates however, we're not bound by HTML's case-insensitive restrictions. That means even in the template, you reference your components and props using camelCase, PascalCase, or kebab-case:
使用 _字符串_ 模板时， 由于 HTML 不区分大小写，在模板中引用组件和 prop 时可以任意使用 camelCase， PascalCase， 或者 kebab-case 形式命名：

``` html
<!-- 可使用任意命名形式 -->
<my-component></my-component>
<myComponent></myComponent>
<MyComponent></MyComponent>
```

If your component isn't passed content via `slot` elements, you can even make it self-closing with a `/` after the name:
如果组件不是由 `slot` 元素传递内容的话，也可以把 `/` 添加组件名后面，使组件自闭合：

``` html
<my-component/>
```
``` html
<my-component/>
```

Again, this _only_ works within string templates, as self-closing custom elements are not valid HTML and your browser's native parser will not understand them.
这种模式 _只_ 适用于字符串模板，浏览器的原生解析器无法理解自闭和的元素，它们是无效的 HTML 。

### Recursive Component
### 递归组件

Components can recursively invoke themselves in their own template. However, they can only do so with the `name` option:
组件在它的模板内可以递归地调用自己，不过，只有当它有 `name` 选项时才可以：

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```
``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

A component like the above will result in a "max stack size exceeded" error, so make sure recursive invocation is conditional (i.e. uses a `v-if` that will eventually be false). When you register a component globally using `Vue.component`, the global ID is automatically set as the component's `name` option.
上面组件会导致一个错误 “max stack size exceeded”，所以要确保递归调用有终止条件。当使用 `Vue.component()` 全局注册一个组件时，组件 ID 自动设置为组件的 `name` 选项。

### Inline Templates
### 内联模板

When the `inline-template` special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.
如果子组件有 `inline-template` 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。这让模板更灵活。

``` html
<my-component inline-template>
  <p>These are compiled as the component's own template.</p>
  <p>Not parent's transclusion content.</p>
</my-component>
```
``` html
<my-component inline-template>
  <p>These are compiled as the component's own template.</p>
  <p>Not parent's transclusion content.</p>
</my-component>
```

However, `inline-template` makes the scope of your templates harder to reason about. As a best practice, prefer defining templates inside the component using the `template` option or in a `template` element in a `.vue` file.
但是 `inline-template` 让模板的作用域难以理解，最佳实践是通过 `template` 选项在组件内，或 `.vue` 文件中的 `template` 元素内定义模板。

### X-Templates
### X-Templates

Another way to define templates is inside of a script element with the type `text/x-template`, then referencing the template by an id. For example:
另一种定义模板的方法是，在 script 元素内定义为 `text/x-template` 类型，然后在模板中通过 id 引用：

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```
``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```
``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

These can be useful for demos with large templates or in extremely small applications, but should otherwise be avoided, because they separate templates from the rest of the component definition.
对于非常小型的应用或是使用了大量的模板的 demo ，这种方法都非常实用，但是在其它场景内都应该避免使用，因为它实际上是把模板从其余的组件定义中分离了出来。

### Cheap Static Components with `v-once`
### 使用 `v-once` 的低消耗静态组件

Rendering plain HTML elements is very fast in Vue, but sometimes you might have a component that contains **a lot** of static content. In these cases, you can ensure that it's only evaluated once and then cached by adding the `v-once` directive to the root element, like this:
在 Vue 中渲染简单的 HTML 元素是很快的，但有时组件内可能会包含 **大量的** 静态内容。这种情况下，如果可以确认这些静态内容只需计算一次的话，就可以通过在根元素上使用 `v-once` 指令缓存它们：

``` js
Vue.component('terms-of-service', {
  template: '\
    <div v-once>\
      <h1>Terms of Service</h1>\
      ... a lot of static content ...\
    </div>\
  '
})
```
``` js
Vue.component('terms-of-service', {
  template: '\
    <div v-once>\
      <h1>Terms of Service</h1>\
      ... a lot of static content ...\
    </div>\
  '
})
```
