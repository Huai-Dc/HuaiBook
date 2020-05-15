### 数组方法
#### find与findIndex
- `find` 用于找出第一个符合条件的数组成员，他的参数是一个回调函数，所有数组成员依次执行该回调，直到找到返回值为true的值，然后返回该成员。若没找到则返回 undefined
```js
[1,2,3,4,5].find(n => n>4);
// 5
```
- `findIndex` 与 `find` 方法类似，返回第一个符合条件的数组成员下标，若没有符合项返回 `-1`
```js
[1,2,3,4,5].findIndex(n => n>4);
// 4
```

#### indexOf  includes
- indexOf 返回符合条件的下标
- includes 返回 true/false

#### filter
- filter 方法使用指定函数测试所有元素，并创建一个返回所有测试通过元素的数组，不改变原数组
```js
let arr = [1,2,3,4,5]
let newArr = arr.filter(item => item>3)
console.log(newArr)  // [4,5]
```

#### forEach
- 遍历数组全部元素，利用回调对数组进行操作，自动遍历整个数组，且无法break跳出循环，不支持return 操作输出，return 只能用于控制循环是否跳过当前循环
```js
let arr = [1,2,3,4,5]
arr.forEach(item => {
    if(item == 3) return false
    console.log(item)
})
// 1,2,4,5
// return false  只能跳过当前循环
```
- 改方法没有返回值，仅仅是遍历数组中的每一项，不会修改原数组，但是可以通过数组索引，修改原数组。

#### some 与 every
- some与every都是JS中数组的迭代方法，只返回boolean值
- every
    - 判断数组中是否每个元素都满足条件
    - 只有都满足才返回 true
    - 有不满足的就返回 false
    - 遇到return false 终止
- some
    - 判断数组中是否至少有一个元素满足条件
    - 只要有一个满足就返回 true
    - 只有都不满足才返回 false
    - 遇到return true后终止
    
```js
let arr = [1,2,3,4,5]
arr.every((item, index, array) => {
    return item%2 == 0
})
// false


arr.some((item, index, array) => {
    return item%2 == 0
})
// true
```

#### map
map 方法返回一个原始数组调用回调函数处理后的新数组，map不会对空数组进行检测，map不会改变原始数组。

```js
let arr = [1,2,3,4,5]
let newArr = arr.map((item, index, arr) => {
    return item*2
})

// [2,4,6,8,10]
```

#### reduce
- reduce 函数接收一个函数作为累加器，数组中的每个值（从左到右）开始合并，最终为一个值
- 不传递 initalValue 自动以第一个元素为初始值，然后从第二个元素开始累积

```js
array.reduce(callback, initalValue);

let nums = [1, 2, 3];
// 多个数的加和
let newNums = nums.reduce((preSum,curVal,array) =>{
  return preSum + curVal; 
}, 0);
console.log(newNums);//6
```

- callback 执行数组中每个值的函数，包含4个参数。
    - previousValue 上一次调用返回的值，或者是提供的初始值
    - currentValue 数组中当前被处理元素
    - index 当前元素在数组中的索引
    - array 调用 reduce 的数组（原始数组）
    
#### sort
- 用于比较的函数，有两个默认参数，分别代表比较的两个元素

```js
let nums = [2, 3, 1];
//两个比较的元素分别为a, b
nums.sort(function(a, b) {
  if(a > b) return 1;
  else if(a < b) return -1;
  else if(a == b) return 0;
})
```

- 排序算法 升序

```js
let nums = [2,1,3]

nums.sort((a, b) => {
    return a-b // 反过来就是降序
})

nums // [1, 2, 3]
```

### 函数的arguments 为什么不是数组
- argument 是个对象，属性从 0 开始最后还有 callee与length属性，我们将其称为类数组
- 类似的还有
    - 用querySelector获得的 nodelist
    - getElementByTagName。。这类的获得的节点

#### 由于其不是数组 会造成数组的方法不可用，以下是转换方法
- Array.prototype.slice.call()

```js
function sum(a, b){
    // 调用数组方法
    let args = Array.prototype.slice.call(arguments)
    args.reduce((sum, cur) => sum + cur);//args可以调用数组原生的方法啦
}
```

- Array.from()

```js
function sum(a, b) {
  let args = Array.from(arguments);
  args.reduce((sum, cur) => sum + cur);//args可以调用数组原生的方法啦
}
```

- ES6 展开运算符

```js
function sum(a, b) {
  let args = [...arguments];
  args.reduce((sum, cur) => sum + cur);
}
```

- concat+apply

```js
function sum(a, b) {
  let args = Array.prototype.concat.apply([], arguments);//apply方法会把第二个参数展开
  args.reduce((sum, cur) => sum + cur);
}
```

#### slice
- slice(start,end)第一个参数表示开始位置,第二个表示截取到的位置(不包含该位置)

#### splice
- splice(start,length)第一个参数开始位置,第二个参数截取长度

https://zhuanlan.zhihu.com/p/81741778


