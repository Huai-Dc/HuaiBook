## 事件修饰符
- 处理事件冒泡等问题，vue为v-on添加了事件修饰符
```$xslt
.stop       // 阻止事件冒泡
.prevent    // 阻止默认事件
.capture    // 使用捕获模式
.self       // 只有e.target 是目标本身才会执行
.once       // 只执行一次后就解绑， 适用于自定义组件事件
.passive    // addEventListener 中的 passive 提高移动端滚动事件等性能
```

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->
<div v-on:scroll.passive="onScroll">...</div>
```

- 使用修饰符事件顺序非常重要
```$xslt
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。
因此，用 v-on:click.prevent.self 会阻止所有的点击，
而 v-on:click.self.prevent 只会阻止对元素自身的点击。
```

## 按键修饰符
```vue
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```
```vue
<!-- 处理函数只会在 $event.key 等于 PageDown 时被调用。 -->
<input v-on:keyup.page-down="onPageDown">
```

## 按键码
- keyCode的事件用法已经被废弃
- 使用keyCode attribute也是允许的
```vue
<input type="text" v-on:keyup.13="submit">
```

```$xslt
.enter      // 回车
.tab        // Tab
.delete     // 退格键与delete
.esc        // Esc
.space      // 空格键
.up         // 箭头上
.down
.left
.right
```

