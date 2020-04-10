### 原型链
#### 原型对象与构造函数的关系
- js中每当定义一个函数数据类型，都会自带一个prototype属性，该属性指向函数的原型对象
- 当函数 调用new时 该函数就是构造函数，返回一个全新的实例，该实例上的_proto_属性指向构造函数的原型对象

#### 描述一下原型链
- js 通过prototype 指向父类对象，直到指向Object这样就形成一个原型指向的链条即原型链
- 对象的hasOwnProperty 用来检测对象本身是否含有该属性
- 使用 in 检测对象是否含有某属性时，若对象中没有，但是原型链上有也会返回true （循环中 不使用 for in 的原因）

#### js如何实现继承
- 使用 call， 父类中的方法子类无法继承

```js
  function Parent1(){
    this.name = 'parent1';
  }
  function Child1(){
    Parent1.call(this);
    this.type = 'child1'
  }
  console.log(new Child1);
```

- 借助原型链 不足 引用的同一个原型对象，对子类的操作可能影响父类中原本的属性

```js
  function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3]
  }
  function Child2() {
    this.type = 'child2';
  }
  Child2.prototype = new Parent2();

  console.log(new Child2());
```

- 以上两种方式的结合

```js
  function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3]
  }
  function Child2() {
    Parent2.call(this);
    this.type = 'child2';
  }
  Child2.prototype = new Parent2();

  console.log(new Child2());
```

- 组合的优化

```js
  function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3]
  }
  function Child2() {
    Parent2.call(this);
    this.type = 'child2';
  }
  Child2.prototype = Parent2.prototype;

  console.log(new Child2());
```

- 寄生组合继承

```js
  function Parent5 () {
    this.name = 'parent5';
    this.play = [1, 2, 3];
  }
  function Child5() {
    Parent5.call(this);
    this.type = 'child5';
  }
  Child5.prototype = Object.create(Parent5.prototype);
  Child5.prototype.constructor = Child5;
```

- Es6的extend
    - 使用babel 编译为ES5时 使用的也是 寄生组合继承 但是多了一个 Object.setPrototypeOf(subClass, superClass) 用来继承父类的静态方法
    
#### 继承的问题
- 创建一个汽车类 有 drive music addOil 三个方法
- 如果新建一个实例 电动车 电动车 不需要加油（addOil的方法是多余的）（香蕉-大猩猩问题）
- 继承最大的问题就是 无法决定继承哪些属性，所有的都必须继承
- 解决的方式，用组合，先设计一系列的零件，再将零件拼装起来，形成不同的实例或类 
