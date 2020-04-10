## 闭包
### 定义
- 有权访问另一个作用域中的变量的函数

### 产生原因
- js中有两种作用域 全局作用域 函数作用域
- 当访问一个变量时，解释器会先在当前作用域中查找标识符，如果没有找到，就去父作用域中，直到找到该变量的标识符或者不在父作用域中，这就是作用域链
- 闭包产生的本质就是，当前环境中存在父级作用域的引用
```js
function f1() {
  var a = 2
  function f2() {
    console.log(a);//2
  }
  return f2;
}
var x = f1();
x();
```

```js
var f3;
function f1() {
  var a = 2
  f3 = function() {
    console.log(a);
  }
}
f1();
f3();
```

### 表现形式
- 返回函数
- 作为函数参数传递
- 定时器、事件监听、Ajax请求等，使用回调就是使用闭包

### 处理闭包导致的结果不符合预期
- 将变量传到定时器中

```js
for(var i = 1;i <= 5;i++){
  (function(j){
    setTimeout(function timer(){
      console.log(j)
    }, 0)
  })(i)
}
```
- 给定时器传第三个参数

```js
for(var i=1;i<=5;i++){
  setTimeout(function timer(j){
    console.log(j)
  }, 0, i)
}
```
- es6的let

```js
for(let i = 1; i <= 5; i++){
  setTimeout(function timer(){
    console.log(i)
  },0)
}
```
