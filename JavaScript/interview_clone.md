## 数据类型
数据分为基本数据类型(String, Number, Boolean, Null, Undefined，Symbol)和对象数据类型。 
- 基本数据类型的特点：直接存储在栈(stack)中的数据
-  引用数据类型的特点：存储的是该对象在栈中引用，真实的数据存放在堆内存里
-  
引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

## 深拷贝与浅拷贝
- 深拷贝和浅拷贝是只针对Object和Array这样的引用数据类型的。
- 浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。

## 赋值与浅拷贝的区别
- 当我们把一个对象赋值给一个新的变量时，赋的其实是该对象的在栈中的地址，而不是堆中的数据。也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此，两个对象是联动的。
- 浅拷贝是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。即默认拷贝构造函数只是对对象进行浅拷贝复制(逐个成员依次拷贝)，即只复制对象空间而不复制资源。

```js
// 对象赋值
let obj1 = {
    'name' : 'zhangsan',
    'age' :  '18',
    'language' : [1,[2,3],[4,5]],
};
let obj2 = obj1;
obj2.name = "lisi";
obj2.language[1] = ["二","三"];
console.log('obj1',obj1)
console.log('obj2',obj2)

// obj1 中的name 会因为被重新赋值而改变， language 也会因为被重新赋值而改变
```
***
```js
let obj1 = {
    'name' : 'zhangsan',
    'age' :  '18',
    'language' : [1,[2,3],[4,5]],
};
 let obj3 = shallowCopy(obj1);
 obj3.name = "lisi";
 obj3.language[1] = ["二","三"];
 function shallowCopy(src) {
    let dst = {};
    for (let prop in src) {
        if (src.hasOwnProperty(prop)) {
            dst[prop] = src[prop];
        }
    }
    return dst;
}
console.log('obj1',obj1)
console.log('obj3',obj3)

// 深拷贝与浅拷贝 只针对引用数据类型 name 属于基本数据类型 拷贝后与原数据并没有联动
// 但是数组属于引用数据类型，浅拷贝拷贝的是其引用地址，所以重新赋值后会影响到原始的数据
```
***

| 类型 | 和原始数据指向同一对象 | 第一层数据为基本数据类型|原数据中包含子对象 |
| :-------- | :--------: | --------: | :--------: |
| 赋值 | 是 | 改变会使原始数据一同改变 | 改变会使原始数据一同改变 |
| 浅拷贝 | 否 | 改变==不会==使原数据一同改变 | 改变会使原数据一同改变 |
| 深拷贝 | 否 | 改变==不会==使原数据一同改变 | 改变==不会==使原数据一同改变 |

## 浅拷贝的实现方式
1.Object.assign()

Object.assign() 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。但是 Object.assign()进行的是浅拷贝，拷贝的是对象的属性的引用，而不是对象本身。

- object只有一层的时候，是深拷贝
```js
let obj = { a: {a: "kobe", b: 39} };
let initalObj = Object.assign({}, obj);
initalObj.a.a = "wade";
console.log(obj.a.a); //wade
```

2.Array.prototype.concat()
```js
let arr = [1, 3, {
    username: 'kobe'
    }];
let arr2=arr.concat();    
arr2[2].username = 'wade';
console.log(arr);
```

3.Array.prototype.slice()
```js
let arr = [1, 3, {
    username: ' kobe'
    }];
let arr3 = arr.slice();
arr3[2].username = 'wade'
console.log(arr);
```

## 深拷贝的实现方式
### JSON.parse(JSON.stringify())

```js
let arr = [1, 3, {
    username: ' kobe'
}];
let arr4 = JSON.parse(JSON.stringify(arr));
arr4[2].username = 'duncan'; 
console.log(arr, arr4)
```

- 这种方法虽然可以实现数组或对象深拷贝,但不能处理函数

```js
let arr = [1, 3, {
    username: ' kobe'
},function(){}];
let arr4 = JSON.parse(JSON.stringify(arr));
arr4[2].username = 'duncan'; 
console.log(arr, arr4)

// 此处拷贝的函数 为 null
// 这是因为JSON.stringify() 方法是将一个JavaScript值
// (对象或者数组)转换为一个 JSON字符串，不能接受函数
```
### 手写递归方法
- 遍历对象、数组直到里边都是基本数据类型，然后再去复制，就是深度拷贝

### 函数库lodash

```js
const _ = require('lodash');
let obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
let obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```

- PS: Object.assign 拷貝的对象属性 也属于浅拷贝

```js
let obj1 = {a: 0, b: {c: 0}};
let obj2 = Object.assign({},obj1);

obj1.b.c = 1;

console.log(obj2) // {a: 0, b: {c: 1}}
```
