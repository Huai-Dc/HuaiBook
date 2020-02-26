### 拓展运算符
- 拓展运算符`...`三个点

```js
console.log(...[1,2,3]) // 1 2 3
console.log(1, ...[2, 3, 4], 5) // 1 2 3 4 5
```

- 需要注意的情况

```js
console.log((...[1, 2]))   // 会报错 只有函数调用时，拓展运算符才能放括号中，否则会报错

console.log(...[1, 2])  // 1 2
```

- 替代 apply 函数

```js
// ES5 的写法
Math.max.apply(null, [14, 3, 77])

// ES6
Math.max(...[14,3,77])

// 等同于
Math.max(14,3,77)
```

#### 拓展运算符运用

- 复制数组

```js
let a = [1,2];
let b = a;

// 以上代码并不是深度复制，a与b指向的是同一份数据修改b会导致a改变

// ES5 只能用方法复制
b = a.concat();
//ES6 
b = [...a]
[...b] = a;

```

- 合并数组

```js
var a = [1,2]
var b = [3,4]

[...a,...b] // 1 2 3 4
```

- 与解构赋值结合
- 字符串

```js
[..."hello"] // ['h', 'e', 'l', 'l' ,'o']
```

- 只要有迭代器的对象都能与拓展运算符结合

```js
let map = new Map([
    [1,'a'],
    [2,'b']
])

let arr = [...map.keys()]  // [1, 2]
```

```js
const go = function *(){
    yield 1;
    yield 2;
    yield 3;
}

[...go()]  // [1,2,3]
```

***

### Array.from()
- 将两类对象转成数组，类似数组的对象与可遍历对象

```js
let arrayLike = {
    '0': 1,
    '1': 2,
    '2': 3,
    'length': 3
}

// ES5
let arr = [].slice.call(arrayLike);

// ES6
let arr1 = Array.from(arrayLike);

Array.from("hello"); //['h', 'e', 'l', 'l' ,'o']

Array.from({ length: 2 }, () => 'jack') // ['jack','jack']
```

### Array.of()
- 将一组值，转化为数组
- 总是返回参数组成的数组，若没有参数则返回空数组

```js
Array.of(1,2,3) //[1,2,3]
```

### copyWithin()
- 在当前数组内部,将指定位置的成员复制到其他位置，然后返回数组，次方法会修改原数组

```js
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

- target 开始替换的位置，若为负数，表示倒数
- start 开始读取数据的位置 默认 0 若为负数，表示倒数
- end 到该位置停止读取数据 默认为数组长度  若为负数，表示倒数


### find() 与 findIndex()
- find() 用于找出第一个符合条件的数组成员，找到会返回符合条件的第一个元素 否则返回undefined
- findIndex() 与find 类似 返回值为数组成员位置，找不大则返回 -1
- 这两个方法可以发现  NaN 弥补了 indexOf 的不足

```js
[NaN].indexOf(NaN)  //-1
[NaN].findIndex((y)=> Object.is(NaN, y)); // 0
```


### fill()
- 使用给定值，填充数组
- 会替换掉数组原有的元素
- 可接受第二与第三个函数 用于指定填充的起始位置

```js
['a','b','c'].fill(7)// [7,7,7]
```

### entries(), keys(), values()
- 返回一个迭代器对象 可以用 `for... of` 进行遍历
- keys() 是对键的遍历 values() 是对值的遍历  entries() 是对键值对的遍历

```js
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

### includes()
- 返回一个 Boolean 值表示数组中是否含有给定的值与字符串的 includes类似

```js
[1,2,3].includes(2) // true
[1,2].includes(3)// false
[NaN].includes(NaN) // true
```

- 与indexOf 相比 indexOf 有两个缺点
    - 不够语义化
    - 不能判断是否含有 NaN （使用的是 === 进行比较的）
- Map 与 Set 有 `has` 方法，两者的区别为
    - Map的 has 方法是用来查找键名的 如 `Map.prototype.has(key)`
    - Set 的 `has` 是用来查找值的 `Set.prototype.has(value)`
    
#### flat flatMap
- 数组的成员有时候 还是数组， flat 用于将嵌套的数组拉平，变成一维数组，该方法返回新数组，不影响原数组

```js
[1,[2,3]].flat() /// [1,2,3]
```

- flat 只会拉平一层，若需要拉平多层可传入参数

```js
[1, 2, [3, [4, 5]]].flat() /// [1,2,3,[4,5]]

[1, 2, [3, [4, 5]]].flat() /// [1,2,3,4,5]

/// 如果不知道几层 可以传入参数 Infinity

/// 若原数组有空位， 则flat 会默认跳过

[1,2, ,3].flat() // [1,2,3]
```


- flatMap对原数组的每一个成员执行一个函数，然后对返回值组成的新数组 执行 flat() 方法，

```js
[1, 2, 3, 4].flatMap(x => [[x * 2]]) // [[2], [4], [6], [8]]
```

### 数组的空位
- 数组的空位，指数组的某一位置上什么值都没有

```js
Array(3) // [ , , ]
```

#### 对空位的处理
- forEach、filter、reduce、every、some 都会跳过空位
- map 会跳过空位，但是胡保留这个值
- join与toString 会空位视为 undefined 而 undefined 与 null 会被处理成空字符串

```js
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1

// filter方法
['a',,'b'].filter(x => true) // ['a','b']

// every方法
[,'a'].every(x => x==='a') // true

// reduce方法
[1,,2].reduce((x,y) => x+y) // 3

// some方法
[,'a'].some(x => x !== 'a') // false

// map方法
[,'a'].map(x => 1) // [,1]

// join方法
[,'a',undefined,null].join('#') // "#a##"

// toString方法
[,'a',undefined,null].toString() // ",a,,"
```

- ES6 明确将空位转成 undefined
