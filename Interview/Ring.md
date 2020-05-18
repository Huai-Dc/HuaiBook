https://zhuanlan.zhihu.com/p/92590306 基础题目

## 闭包相关
### 是什么
- 此处提问者想了解的是
    - 为什么其他非闭包的函数没有权限访问另一个函数的内部作用域
    - 为什么闭包有这个权限
    - 什么是函数作用域
- 有权访问其他函数作用域内的变量的一个函数（js高程）
- 由于在js中，变量的作用域属于函数作用域，在函数执行后作用域会被清理，内存也被回收，但是闭包是建立在一个函数内部的子函数，由于其可以访问上级作用域，即使上级执行完毕，作用域也不会被销毁，这时候的子函数就是闭包。

### 闭包解决了什么
- 由于闭包可以缓存上级作用域，那么就能使函数外部打破“函数作用域的束缚”，可以访问函数内部变量。平时写的Ajax回调就是典型的闭包，由于闭包的特性，回调就有访问与操作整个上级作用域的能力，开发者就不用写钩子操作上级作用域内部变量了


### 闭包应用场景
- 之前的数字序列生成器就是闭包的应用场景之一
- ajax的请求回调，事件绑定的回调，setTimeout的延时回调等都是闭包

### 优缺点
- 优点
    - 减少全局变量
    - 减少传递函数的参数量
    - 封装
    - 模拟块级作用域
    
```js
for(var i=0; i<10; i++){
    console.info(i)
}
alert(i)  // 变量提升，弹出10

//为了避免i的提升可以这样做
(function () {
    for(var i=0; i<10; i++){
         console.info(i)
    }
})()
alert(i)   // undefined // underfined   因为i随着闭包函数的退出，执行环境销毁，变量回收
```

- 缺点
    - 闭包会占有内存资源，过多使用会导致内存溢出（所以不需要用到的变量，要置为null或undefined）

## 原型与原型链
### 什么是原型
- js中所有东西都可以看作对象，但是对象还分普通对象与函数对象

### _proto_与prototype
- 每个对象都有一个 `__proto__`属性，并且指向 `prototype` 原型对象
- 每个构造函数都有一个`prototype`原型对象
    - `prototype`原型对象的的`constructor` 指向构造函数
    - 实例对象的`__proto__`指向构造函数的`prototype`从而实现继承
    - `prototype`对象相当于特定类型所有实例对象都可以访问的公共容器

```js
function Person(nick, age){
    this.nick = nick;
    this.age = age;
}
Person.prototype.sayName = function(){
    console.log(this.nick);
}

var p1 = new Person('Byron', 20);

var p2 = new Person('Casper', 25);

p1.sayName()  // Byron

p2.sayName()  // Casper

p1.__proto__ === Person.prototype       //true

p2.__proto__ === Person.prototype   //true

p1.__proto__ === p2.__proto__           //true

Person.prototype.constructor === Person  //true
```

- PS
    - 建议少用 `Object.prototype.__proto__`, 只使用 `Object.getPrototypeOf()`
    - Object.create(null)新建的对象时没有 `__proto__`属性的

### 原型链
- demo

```js
var arr = [1,2,3]

arr.valueOf()  //  [1, 2, 3]
```

- 通过浏览器输出 arr对象，在其 `__proto__` 中是找不到 `valueOf`方法的
- 但是我们能在 `Array.prototype.__proto__`中找到 `valueOf`
- 查找过程
    - 当前实例对象obj，查找obj属性或方法
    - 没有找到，通过`obj.__proto__` 查找，找到obj构造函数的prototype并查找对应的属性或方法，找到则返回
    - 没有找到把`Array.prototype`当做obj重复以上步骤 
    - 最后查到`Object.prototype`时， `Object.proptotype.__proto__ === null` 查找结束

```js
arr.__proto__ === Array.prototype
// true
Array.prototype.__proto__ === Object.prototype
// true
arr.__proto__.__proto__ === Object.prototype
// true

// 原型链的终点
Object.prototype.__proto__ === null
// true
```

- 原型链如下
```js
arr ---> Array.prototype ---> Object.prototype ---> null
```
- 这就是传说中的原型链，层层向上查找，最后还没有就返回undefined

## 继承
### 什么是继承
- 一个对象直接使用另一个对象的属性与方法

### 属性如何继承
- 先创建一个Person 类

```js
function Person (name, age) {
    this.name = name
    this.age = age
}

// 方法定义在构造函数的原型上
Person.prototype.getName = function () { console.log(this.name)}
```

- 此时我们想创建Teacher类并希望他可以继承Person 类

```js
function Teacher (name, age, subject) {
    Person.call(this, name, age)
    this.subject = subject
}
```
- 属性的继承就是通过在一个类内执行另一个构造函数，并通过call指定this为当前执行环境，这样就能得到另一个类的所有属性

### 继承方法
- 我们都知道方法定义在`prototype`中，那么我们只需要将Person的prototype的备份赋值给Teacher的prototype

```js
Teacher.prototype = Object.create(Person.prototype)
```
- 不能直接赋值是因为直接赋值就是引用关系，修改Teacher的prototype也会影响到Person的prototype
- 给Teacher添加方法应该放在修改prototype后否则会被覆盖，因为赋值前后对象不一样了
- 还有我们知道 prototype 的 constructor 指向构造函数本身，赋值后Teacher的构造变成了Person，指向错误需要更正

```js
Teacher.prototype.constructor = Teacher
```

```js
Teacher.prototype = Object.create(Person.prototype)

Teacher.prototype.constructor
// ƒ Person(name, age) {
//     this.name = name
//     this.age = age
// }

---

Teacher.prototype.constructor = Teacher
// ƒ Teacher(name, age, subject) {
//     Person.call(this, name, age)
//     this.subject = subject
// }
```

- 最终方案

```js
Teacher.prototype = Object.create(Person.prototype)
Teacher.prototype.constructor = Teacher
```

### hasOwnProperty
- 在原型链上查询属性比较耗时，对性能有影响，视图访问不存在的属性时会遍历整个原型链，需要检查是否具有自己定义的属性，而不是原型链上的属性，必须用 hasOwnProperty
- hasOwnProperty 是唯一处理属性不会遍历原型链的方法

### 总结
#### prototype 和 __proto__

- 每个对象都有一个__proto__属性，并且指向它的prototype原型对象
- 每个构造函数都有一个prototype原型对象
    - prototype原型对象里的constructor指向构造函数本身

#### 原型链
- 每个对象都有一个__proto__，它指向它的prototype原型对象，而prototype原型对象又具有一个自己的prototype原型对象，就这样层层往上直到一个对象的原型prototype为null

- 这个查询的路径就是原型链


## 基础面试题
### 基本数据类型
- undefined、null、Object、Boolean、Number、String、Symbol

### 引用数据类型
- Object、Array、Function

### 数据类型检测

- typeof 除了null 都可以显示正确的类型，typeof对象除了函数都会显示Object

- instanceof 通过原型链来判断数据类型

```js
let p1 = new Person();
p1 instanceof Person
```

- Object.prototype.toString.call(obj) 可以检测所有的类型，算是一个比较完美的方法。

```js
let obj = {};
let arr = [];

Object.prototype.toString.call(obj) // [object Object]

Object.prototype.toString.call(arr) // [object Array]
```

### 深浅拷贝
- 浅拷贝
```js
Object.assign()
```

- Object.assign 会合并对象生成一个新对象，如果对象的属性是普通类型，改变后新对象不会改变，如果是引用类型，改变后新对象也会跟着改变；

```js
let a = {b:{i:1}};

let k = Object.assign({}, a)

a.b.i = 2;

console.log(k.b.i) // 2
```

- 深拷贝
```js
JSON.parse(JSON.stringify(obj))
```

- 实现一个深拷贝，深拷贝可以拆分成两步， 浅拷贝+递归

```js
function deepClone(obj){
    if(typeof obj !== "object" && obj !== null){ // 非对象返回本身
        return obj
    }
    let target = Array.isArray(obj)?[]:{};
    
    for(let key in obj){
        if(typeof obj[key] === "object" && obj[key] !== null){
            target[key] = deepClone(obj[key])  // 递归
        }else{
            target[key] = obj[key]
        }
    }
    
    return target
}
```

### 作用域
#### 变量提升
- 在js 中函数声明与 var 变量声明会隐式的提升到当前作用域顶部
- 函数声明的优先级高于变量， 如果函数名与变量名相同且未赋值，函数声明会覆盖变量声明
- 声明部分，赋值不会被提升， 只有变量名会被提升

#### 作用域链
- 因为函数嵌套形成的层级关系，当函数执行时， 从当前作用域开始搜索，没有找到变量会向上级作用域搜索，直到全局，这就是作用域链
- 在js 中作用域为 function(){}内的区域就是函数作用域
- 全局函数没办法查看局部函数的细节，但是局部函数可以查看上层的函数细节，直至全局

#### 闭包
- 闭包的原理就是作用域链

### 继承与原型
#### js创建对象的几种方式
- 对象字面量

```js
var obj = {}
```

- new 一个构造函数

```js
let p1 = new Person()
```

- new 一个内置对象

```js
var obj = new Object()
```

- Object.create()

```js
var obj = Object.create({c:1});
```

- 三种方式的区别
    - Object.create(null),创建后是个空对象，没有继承Object.prototype的属性与方法
    - Object.create({x:1})  // 打印出来的依旧是 {} x:1 挂载在 `__proto__` 上
    - `new Object` 与 `var obj = {}` 默认都是继承`Object.prototype`
    
### js 怎么实现一个类

- ES5

```js
function Person(name){
    this.name = name;
}

Person.sayName = function(){
    console.log(this.name)
}
```

- ES6

```js
class Person{
    constructor(name){
        this.name = name;
    }
    
    sayName(){
        console.log(this.name)
    }
}
```

### 原型链
- 遍历一个实例属性时，先遍历实例对象上的属性，再遍历原型对象，一直到Object

- 任何一个类（函数）都有原型对象，原型对象至少有两个属性，constructor `__proto__`
- constructor 指向构造函数， `__proto__` 指向父类原型对象
- 实例没有prototype属性，类才有

### js实现继承
- 构造函数绑定， 使用call 或 apply
- 实例继承，将子对象prototype指向父对象的实例
```js
var Cat.prototype = new Animal()
Cat.prototype.constructor = Cat;
```
- 拷贝继承， 将父对象的属性与方法拷贝给子对象
- 原型继承， 将子对象prototype 指向父对象prototype
- ES6 extends


## new 与 this
### new 操作符具体做了什么
- 1.创建实例对象{}
- 2.this 变量引用该对象，并继承构造函数的原型
- 3.属性与方法加入到this引用的对象上
- 4.返回 this

```js
var obj = new Object()

Constructor = [].shift.call(arguments)

...
```

### this对象理解
#### 普通函数
- this 总是指向函数的直接调用者
- 如果有new 关键字， this 指向新生成的实例
- 在事件中，this指向触发该事件对象（全局调用就是 window）
- IE 下 attachEvent 总是指向 window
- 箭头函数，函数体内部的this 指向的是定义时所在作用域的对象，不是调用时的
- 函数内部的匿名函数，谁调用了谁就是 this
- 对箭头函数使用 bind 是无效的

#### call apply bind
- bind 返回对应函数， call、apply 是立即调用
- call 传入参数列表， apply 传入数组
- bind 实际上是创建一个新函数
- 作用都是改变函数内部作用域的指向

## 数据处理
### 数组去重
- indexOf

```js
var arr=['12','32','89','12','12','78','12','32'];
    // 最简单数组去重法
function unique1(array){
    var n = []; //一个新的临时数组
    for(var i = 0; i < array.length; i++){ //遍历当前数组
        if (n.indexOf(array[i]) == -1)
            n.push(array[i]);
    }
    return n;
}
arr=unique1(arr);


// ES6 
[...new Set(arr)]
```
### 排序
```js
var arr = [1,5,4,2];

arr.sort((a,b)=>{
    return a-b;  // ASC
})
```

### 递归求和

```js
var arr = [1,2,3,4,5]

arr.reduce((a,b)=>{
    return a+b
})
```

## Event Loop
### 宏任务与微任务
- 宏任务，script的全部代码，setTimeout, setInterval,I/O, UI rendering
- 微任务，下一个宏任务开始前执行的任务，Process.nextTick(Node独有)，Promise,MutationObserver(水印中用来监听DOM变化)

### 什么是even loop
- 主线程运行时生成堆与栈
- js 从上到下解析方法，将其中的同步任务按照执行顺序排列到执行栈中
- 当主程序调用外部api时，会将此类异步任务挂起，继续执行栈中的任务，等异步任务返回结果后，再按照顺序排列到事件队列中
- 主线程每次清空执行栈，就会去事件队列中检测是否有任务，如果有就每次取出一个推到执行栈中，这个循环往复的过程就是 eventloop


## 浏览器页面的渲染过程
- 浏览器解析 html 代码 形成 dom树，并行请求css / js / image, 每个html标签都有一个dom节点，每个文本也有对应的文本节点，dom树的根就是 document 对应的就是 html
- 浏览器解析 css 计算出最终的样式
- 构建渲染树（会忽略 head， display:none的元素）
- 根据渲染树绘制到屏幕


## 浏览器缓存



