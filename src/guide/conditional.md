---
title: Conditional Rendering
type: guide
order: 7
---

## v-if
## v-if

In string templates, for example Handlebars, we would write a conditional block like this:
在字符串模板中，比如 Handlebars，我们可以像这样写一个条件语句块：

``` html
<!-- Handlebars template -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```
``` html
<!-- Handlebars 模板 -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

In Vue, we use the `v-if` directive to achieve the same:
在 Vue 中，我们可以用 `v-if` 指令来实现同一功能：

``` html
<h1 v-if="ok">Yes</h1>
```
``` html
<h1 v-if="ok">Yes</h1>
```

It is also possible to add an "else" block with `v-else`:
也可以用 `v-else` 来添加一个"else"语句块：

``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```
``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

### Template v-if
### Template v-if

Because `v-if` is a directive, it has to be attached to a single element. But what if we want to toggle more than one element? In this case we can use `v-if` on a `<template>` element, which serves as an invisible wrapper. The final rendered result will not include the `<template>` element.
由于 `v-if` 是一个指令，它必须添加在单个元素上。但是如果我们想切换多个元素的话要怎么做呢？我们可以通过在 `<template>` 元素上使用 `v-if` 来实现，`<template>` 元素可以充当一个不可见的包装容器。最后的渲染结果不会包含`<template>` 元素。

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```
``` html
<template v-if="ok">
  <h1>标题</h1>
  <p>段落1</p>
  <p>段落2</p>
</template>
```

### v-else
### v-else

You can use the `v-else` directive to indicate an "else block" for `v-if`:
你可以用 `v-else` 指令作为 `v-if` 的“else语句块”：

``` html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```
``` html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

The `v-else` element must immediately follow the `v-if` element - otherwise it will not be recognized.
'v-else'元素必须要紧挨着 `v-if` 元素——否则它将无法被识别。

## v-show

Another option for conditionally displaying an element is the `v-show` directive. The usage is largely the same:
另一个可以用来根据条件显示元素的指令是 `v-show` 指令。它的使用方法与 `v-if` 大致相同。

``` html
<h1 v-show="ok">Hello!</h1>
```
``` html
<h1 v-show="ok">Hello!</h1>
```

The difference is that an element with `v-show` will always be rendered and remain in the DOM; `v-show` simply toggles the `display` CSS property of the element.
区别在于使用 'v-show' 指令的元素始终会被渲染，存在于DOM中；'v-show' 指令仅仅切换了元素的CSS属性 `display`。

<p class="tip">Note that `v-show` doesn't support the `<template>` syntax, nor does it work with `v-else`.</p>
<p class="tip">注意 `v-show` 不支持 `<template>` 语法, 在其后使用 `v-else` 指令也不会生效.</p>

## v-if vs. v-show

`v-if` is "real" conditional rendering because it ensures that event listeners and child components inside the conditional block are properly destroyed and re-created during toggles.
`v-if` 是“真实的”条件渲染，因为它确保在切换过程中，完全地销毁和重建事件监听和条件块里的子组件。

`v-if` is also **lazy**: if the condition is false on initial render, it will not do anything - the conditional block won't be rendered until the condition becomes true for the first time.
`v-if` 也是 **惰性的**: 如果在初始渲染时条件为 false，它就什么都不会做——条件块在第一次变成 true 之后才被渲染的。

In comparison, `v-show` is much simpler - the element is always rendered regardless of initial condition, with just simple CSS-based toggling.
相比之下， `v-show` 简单得多——无论初始条件是什么，元素始终会被渲染，指令只是简单地基于切换CSS来实现功能。

Generally speaking, `v-if` has higher toggle costs while `v-show` has higher initial render costs. So prefer `v-show` if you need to toggle something very often, and prefer `v-if` if the condition is unlikely to change at runtime.
一般来说，`v-if` 有更高的切换消耗，`v-show` 有更高的初始渲染消耗。因此，如果需要经常切换的话使用`v-show`更好，如果在运行中条件很少会改变的话，`v-if`是更好的选择。
