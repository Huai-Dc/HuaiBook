## 遍历器
js中原本表示集合的数据结构主要有 `数组`、 `对象` ES6 又添加了 Map 与 Set，遍历器就是用来为不同的数据
结构提供统一的访问机制。

#### 遍历过程
- 1.创建一个指针对象，指向当前数据结构的起始位置，所以遍历器本质上就是一个指针对象。
- 2.第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员
- 3.不断调用指针对象，直到指向最后的结束位置
- 4.每次调用 next 都会返回当前成员的信息 `value` 与是否结束遍历 `done`

#### 原生具备遍历器接口的对象
- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

#### 使用场景
- 解构赋值

```js
let set = new Set().add(1).add(2).add(3)

let [a, b] = set;
// a = 1, b = 2
let [first, ...rest] = set;
// first = 1, rest = [2,3]

```

- 拓展运算符

```js
var str = 'hello';
[...str] //  ['h','e','l','l','o']
```

- yield*

    `yield*`后面跟的是一个可遍历结构
    
```js
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};
```

#### for...of 循环
- 遍历数据的统一方法

