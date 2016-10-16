---
title: Class 与 Style 绑定
type: guide
order: 6
---

数据绑定的一个常见需求是操作元素的 class 列表和其内联样式。由于他们都是 attribute ，我们可以使用 `v-bind` 来处理它们：我们只需要计算出表达式最终的字符串结果就要好了。然而，拼接字符串是一件繁琐而又容易出错的事情。因此，Vue 特地强化了 `v-bind` 在 `class` 和 `style` 上的应用。除了字符串之外，表达式的结果类型还可以是对象或者数组。s.

## 绑定 HTML Class

### 对象语法

我们可以传递给 `v-bind:class` 一个对象，以动态的切换 class：

``` html
<div v-bind:class="{ active: isActive }"></div>
```

以上的语法的意味着 class `active` 的存在与否取决于数据对象 `isActive` 的[真值性](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)。

在对象中，你可以有多个属性以切换的多个 class 。除此之外，`v-bind:class` 指令也可以和原生的 `class` 属性共存，所以有下面的例子：

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

与其依赖的数据:

``` js
data: {
  isActive: true,
  hasError: false
}
```

会渲染为:

``` html
<div class="static active"></div>
```

每当 `isActive` 或者 `hasError` 的值改变了，class 列表就会跟着更新。举个例子，如果 `hasError` 的值变为 `true` ,class 列表就会变成 `"static active text-danger"`。

绑定的对象不一定要是内嵌的:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

这会被渲染为同样的结果。我们也可以绑定返回一个对象的[计算属性](computed.html)。 这是一个常用且强大的模式:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal',
    }
  }
}
```

### 数组语法

我们可以把一个数组传给 v-bind:class，以添加一个 class 列表 :

``` html
<div v-bind:class="[activeClass, errorClass]">
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

会渲染为:

``` html
<div class="active text-danger"></div>
```

如果你也想要在这个数组列表中条件切换 class，你可以使用三元表达式：

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]">
```

这个例子中将总是添加 `errorClass` ，但是只有当 `isAcitve` 为真时才会添加 `activeClass`。

然而，当你有多个带条件的 class 这个写法可能会稍微有些啰嗦了，这就是为什么你也可以在数组语法中使用对象语法：

``` html
<div v-bind:class="[{ active: isActive }, errorClass]">
```

## 绑定行内样式

### 对象语法

`v-bind:style` 的对象语法非常直观—它看起来就像是 css ，除了它是一个 javaScript 对象以外。你可以以用驼峰式（camelCase）或短横分隔命名（kebab-case）来命名CSS 属性名:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

直接绑定一个样式对象通常来说是个好主意，因为它能让模板变得清晰：

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

同样的，对象语法通常会结合计算属性返回样式对象来使用。

### 数组语法

`v-bind:style` 的数组语法允许你添加多个样式对象到同一个元素上：

``` html
<div v-bind:style="[baseStyles, overridingStyles]">
```
### 自动补齐前缀

当你在 `v-bind:style` 中使用的 css 属性需要浏览器前缀时，例如 `transform` 属性，Vue 将会自动的检测并添加合适的前缀到实际的样式上。
