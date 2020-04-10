### 为什么 0.1+0.2 不等于 0.3
- 0.1和0.2在转换成二进制后会无限循环，由于标准位数的限制后面多余的位数会被截掉，此时就已经出现了精度的损失
- 处理方式 先放大计算后再缩小

### '1'.toString() 为什么可以调用

```js
// 可看作以下步骤
var s = new String('1');
s.toString();
s = null;
```

### null 是对象吗
- null 是个关键字 虽然typeof 会输出 object
- undefined 是个变量 可以重新赋值

### js原始数据类型
- Boolean
- Object (引用类型包含 普通对象 数组对象Array 正则对象RegExp 日期对象Date 数学函数Math 函数对象)
- Number
- String
- symbol
- null
- undefined

### typeof 是否能正确判断类型
- 对于原始数据类型 除了null 都能调用 typeof 显示正确类型
- 对于引用类型 除了函数之外都会显示 object
- 用于判断对象数据类型会不准确 无论对象数据是 数组还是对象都会返回 object
- 用instanceof 判断会更加合理 instanceof 是基于原型链的查询

### Object.is 与 === 的区别
- Object在严格等于的基础上修复了一些错误，具体为 +0 与 -0 NaN 与NaN
```js
+0 === -0 // true
Object.is(+0,-0) // false

NaN === NaN // false
Object.is(NaN, NaN) // true
```
// is的原理

```js
function is(x, y) {
  if (x === y) {
    //运行到1/x === 1/y的时候x和y都为0，但是1/+0 = +Infinity， 1/-0 = -Infinity, 是不一样的
    return x !== 0 || y !== 0 || 1 / x === 1 / y;
  } else {
    //NaN===NaN是false,这是不对的，我们在这里做一个拦截，x !== x，那么一定是 NaN, y 同理
    //两个都是NaN的时候返回true
    return x !== x && y !== y;
  }
}
```

