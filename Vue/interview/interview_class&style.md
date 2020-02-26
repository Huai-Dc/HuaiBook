#### 动态绑定class
- 对象语法
```html
<div class="color" :class="{active: isActive}"></div>
```
```js
data(){
    return {
        isActive: true
    }
}
```

- 数组语法
```html
<div :class="[activeClass, dangerClass]"></div>
```
```js
data: {
    activeClass: 'active',
    dangerClass: 'text-error'
}
```
- 可以使用三元表达式配合
```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

- 与对象语法结合
```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

#### 绑定内联样式
- 对象语法
```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
```js
data: {
  activeColor: 'red',
  fontSize: 30
}
```
- 数组语法
```vue
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```
