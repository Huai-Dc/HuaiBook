## 函数与对象
### this 指向问题
- this 永远指向一个对象
- this的指向完全取决于函数调用的位置

#### this使用频繁的几个场景
- 对象中的方法
- 事件绑定
- 构造函数
- 定时器
- 函数对象的call、apply

#### 事件绑定的方式
- 行内绑定

```html
<a onclick="clickFun()"></a>
// 由于当前运行环境（标签行内）并没有clickFun 的方法  会到全局去找 所以 this就会指向window

<a onclick="console.log(this)"></a>
// 指向节点本身

// 内联事件 在严格模式下的两种情况
<button onclick="alert((function(){'use strict'; return this})());">
  内联事件处理1
</button>
<!-- 警告窗口中的字符为undefined -->

<button onclick="'use strict'; alert(this.tagName.toLowerCase());">
  内联事件处理2
</button>
<!-- 警告窗口中的字符为button -->
```

- 动态绑定 与 事件监听

```html
<input type="button" value="按钮" id="btn">
<script>
   let btn = document.getElementById('btn');
   btn.onclick = function(){
       this ;  // this指向本节点对象
   }
</script>

// 动态绑定就是为节点对象的属性重新赋值为一个匿名函数，
// 所以函数执行时是在节点对象的环境下，this就指向节点本身
```

- new 一个实例的五个步骤
    - 创建一个空对象
    - 将创建的空对象的原型执行构造函数的prototype（继承）
    - 将构造函数的this 执行新的对象
    - 执行构造函数代码为对象赋值（添加属性）
    - 返回对象（引用地址）
    
#### 定时器中的this

```js
let obj = {
    fun:function(){
        this ;
    }
}
​
setInterval(obj.fun,1000);      // this指向window对象
setInterval('obj.fun()',1000);  // this指向obj对象

// 定时器是window对象内置的一个方法
```

#### 严格模式下以下方法中的this为undefined
```js
    "use strict";
    
    console.log("严格模式");
    console.log('在全局作用域中函数中的this');
    function f1(){
      console.log(this);
    }
    
    function f2(){
      function f3(){
        console.log(this);
      }
      f3();
    }
    f1();  // undefined
    f2();  // undefined
```

#### call、apply
- .call(obj,arg1,arg2...argN);
- .apply(obj,[arg1,arg2...,argN])

- 作用一致，都是用来改变 函数中的this指向

