## 经典题目
- [1,2,3].map(parseInt)
```js
[1,2,3].map(parseInt)
// [1, NaN, NaN]
// parseInt 有两个参数 parseInt(s,radix)
// radix 值为0 或省略 则以10 来解析 以0x或0X开头则以16进制解析 不在范围内则返回NaN
parseInt("1", 0) // 1
parseInt("2", 1)// 1 不在 2~36的范围内所以返回NaN
parseInt("3", 2)// 二进制没有3 所以返回NaN
```
