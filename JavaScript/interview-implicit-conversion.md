## 隐式转换
### 介绍
- 当运算符运算时，如果两边数据不统一，CPU就无法计算，这时编译器会自动将操作符两边的数据做转换后再计算
```js
console.log("2" > 1) // true
```

### 字符串连接符与算术运算符

```js
console.log(1+"true"); // 1true  一边为字符串 + 为字符串连接符
console.log(1+true); // 2  + 是算术运算符 1+Number(true) = 1+1 = 2
console.log(1+undefined); // NaN  1+ Number(undefined) = 1+NaN = NaN
console.log(1+null); // 1   1+ Number(null) = 1+0 = 1
```


### 关系运算符 将其他类型转换为number后比较

```js
console.log("2">10) // false   Number(2)> 10 
console.log("2">"10") // true  "2".chartCodeAt()>"10".chartCodeAt()  50>49
console.log("abc">"a") // false  // 一个个字母比较 "a".chartCodeAt()
console.log("abc">"aad") // true
console.log(NaN == NaN)// false NaN 不等于任何数包括自己本身
console.log(undefined == null) // true
console.log(undefined === null) // false
```


### 复杂类型 会先转成String再转成 number运算

```js
let a = "?"
if(a == 1&& a ==2 && a==3){
    console.log(1)
}

a = {
    i:0,
    valueOf : () => ++ a.i
}

// 完善a  使其 输出 1
```
- 复杂对象类型会先调用 numberOf 然后转成 number 运算 而且 对象的 valueOf 是可以重写的

### 逻辑非 隐式转换与关系运算符隐式转换混淆

```js

// 逻辑非 会将 undefined null NaN 空字符串 false 0 -0 document.all() 转换为 false  其他情况为 true
console.log( [] == 0) // true  Number("") = 0
console.log(![] == 0) //true  false == 0  [] 转布尔值得 true 取反 为false

console.log( [] == ![]) // true
console.log( [] == []) // false

console.log( {} == !{}) // false
console.log( {} == {}) // false
```





