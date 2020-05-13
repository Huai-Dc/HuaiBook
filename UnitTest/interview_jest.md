## 是什么
- Jest 是FB 开源的一套js测试框架，自动集成了断言，JSDom，覆盖率报告等前端所需工具

## 安装
### 安装依赖
```js
npm install -D jest babel-jest babel-core babel-preset-env regenerator-runtime
```

- `babel-jest`、 `babel-core`、 `regenerator-runtime`、`babel-preset-env`这几个依赖是为了让我们可以使用ES6的语法特性进行单元测试，ES6提供的 `import` 来导入模块的方式，jest 本身不支持

### 添加 `.babelrc`文件
- 写入如下内容
```js
{
    "presets": 'env'
}
```

### 修改`package.json`中的test值
```js
"scripts":{
    "test": "jest"
}
```

## 写测试文件
- sum.js
```js
function sum(a, b) {
    return a + b;
}
module.export = {sum}
```

- sum.test.js
```js
const {sum} = require("./sum.js")
test("测试1+2=3", ()=>{
    expect(sum(1,2)).toBe(3)
})
```

- 运行`npm run test`会打印出如下信息

```js
PASS  test/functions.test.js
  √ 测试1+2=3

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        4.8s
```

## 常用的jest断言

- `expect`函数用来包装被测试的方法，并返回一个对象，该对象中包含一系列的匹配器来帮助我们进行断言

### .not
- 允许你测试结果不等于某个值得情况
```js
expect(functions.sum(2, 2)).not.toBe(5);
```

### .toEqual()
- `.toEqual`会递归检查对象的所有属性值是否相等，做应用类型比较时请使用`.toEqual`而不是`.toBe`

```js
// functions.js
export default {
  getAuthor() {
    return {
      name: 'LITANGHUI',
      age: 24,
    }
  }
}
// functions.test.js
import functions  from '../src/functions';

test('getAuthor()返回的对象深度相等', () => {
  expect(functions.getAuthor()).toEqual(functions.getAuthor());
})

test('getAuthor()返回的对象内存地址不同', () => {
  expect(functions.getAuthor()).not.toBe(functions.getAuthor());
})
```

### .toHaveLength
- `.toHaveLength`可以方便的用来测试字符串与数组长度是否符合预期

```js
test('getIntArray(3)返回的数组长度应该为3', () => {
  expect(functions.getIntArray(3)).toHaveLength(3);
})
```

### .toThrow
- `.toThrow`可能够让我们测试被测试方法是否按照预期抛出异常，需要注意的是我们需要将被测试函数用一个函数包装

```js
// functions.js
export default {
  getIntArray(num) {
    if (!Number.isInteger(num)) {
      throw Error('"getIntArray"只接受整数类型的参数');
    }
  }
}

// functions.test.js
import functions  from '../src/functions';

test('getIntArray(3.3)应该抛出错误', () => {
  function getIntArrayWrapFn() {
    functions.getIntArray(3.3);
  }
  expect(getIntArrayWrapFn).toThrow('"getIntArray"只接受整数类型的参数');
})
```

### .toMatch
- `.toMatch`传入一个正则表达式，允许我们用字符串类型的正则匹配

```js
test('getAuthor().name应该包含"li"这个姓氏', () => {
  expect(functions.getAuthor().name).toMatch(/li/i);
})
```

## 测试异步函数
- 测试请求地址 http://jsonplaceholder.typicode.com/users/1

```js
// functions.js
import axios from 'axios';

export default {
  fetchUser() {
    return axios.get('http://jsonplaceholder.typicode.com/users/1')
      .then(res => res.data)
      .catch(error => console.log(error));
  }
}
// functions.test.js
import functions  from '../src/functions';

test('fetchUser() 可以请求到一个含有name属性值为Leanne Graham的对象', () => {
  expect.assertions(1);
  return functions.fetchUser()
    .then(data => {
      expect(data.name).toBe('Leanne Graham');
    });
})
```
- 上面调用到的`expect.assertions(1)`确保在异步的测试用例中，有一个断言会在回调函数中被执行，可以防止漏测，比如执行了`reject`但是只测试了`resolve`

### 使用 `await`与`async` 精简异步代码
```js
test('fetchUser() 可以请求到一个用户名字为Leanne Graham', async () => {
  expect.assertions(1);
  const data =  await functions.fetchUser();
  expect(data.name).toBe('Leanne Graham')
})
```
