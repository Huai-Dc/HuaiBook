## 基础
### 原始数据类型
- js 的类型分为原始数据类型与对象类型
- 原始数据类型包括 ：Boolean、String、Number、Null、Undefined、Symbol

#### Boolean
```typescript
let isDone: boolean = false;
```

- PS: 使用构造函数Boolean创造的对象不是布尔值，实际上返回的是一个Boolean对象

```typescript
let createdByNewBoolean: boolean = new Boolean(1);
// 此处会报错
```

```typescript
let createdByBoolean: boolean = Boolean(1);
// 编译通过 返回的是boolean类型
```

#### Number 数值
```typescript
let num: number = 1;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;

// 二进制
let binaryLiteral: number = 0b1010;
// 八进制
let octalLiteral: number = 0o744;
```

#### String 字符串
```typescript
let str: string = 'hello'
```

#### 空值
- js中没有空值的概念，ts中可以用 void 表示没有任何返回值的函数

```typescript
function sayName():void{
    alert(111)
}
```

- 声明一个void类型的变量没有用处，只能赋值`undefined`、`null`

#### Null 与 undefined

```typescript
let u:undefined = undefined;
let n:null = null;
```

- 与`void`的区别为`undefined`与`null`是所有类型的子类型，就是说 `undefined` 的类型可以赋值给 `number`的类型

```typescript
// 这样不会报错
let num: number = undefined;

// 这样也不会报错
let u: undefined;
let num2: number = u;

// 报错
let u2: void;
let num3: number = u;
```

***

### 任意值
- 任意值，表示允许赋值为任意类型

#### 是什么
- 普通类型，在赋值过程中改变类型是不允许的

```typescript
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
// 此处会报错
```

- 但是如果是 any 则允许赋值为任意类型

```typescript
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

#### 属性与方法
- 在任意值上访问任何属性都是允许的

```typescript
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
```

- 也允许调用任何方法

```typescript
let anyThing: any = 'Tom';
anyThing.setName('Jerry');
anyThing.setName('Jerry').sayHello();
anyThing.myName.setFirstName('Cat');
```

#### 未声明类型的变量
- 如果声明时，没有指定类型，则会被识别为任意类型

***

### 类型推论
- ts 会在没有明确指定类型的时候，推测出一个类型，这就是类型推论

***

### 联合类型
- 联合类型表示取值可以是多种类型中的一种

```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
// 赋值为 string 或 number 之外的类型会报错
```

#### 属性与方法
- 当ts不确定一个联合类型的变量属于哪个类型时，我们只能访问此联合类型的所有类型里共有的方法与属性

```typescript
function getLength(something: string | number): number {
    return something.length;  // number 没有 length 属性所以会报错
}
```

- 联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：
- 推断出一个类型后就能使用其类型的属性与方法

***

### 对象类型---接口
#### 什么是接口
- 接口是对行为的抽象，如何行动需要class类去实现

```typescript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
// 上面的例子中，既不能额外添加属性，也不能减少属性
// 赋值的时候，变量的形状必须和接口的形状保持一致。
```

#### 可选属性

```typescript
interface Person {
    name: string;
    age?: number; // 可以不写age
}
let tom: Person = {
    name: 'Tom',
};
```

#### 任意属性

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

- 一旦定义了任意属性，那么确定属性与可选属性都必须是其类型的子集

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// 由于可选类型 age 为 number 不是 string的子集 所以此处会报错
```
- 一个接口中只能定义一个任意属性，如果接口中有多个类型的属性，则可以在任意属性中使用联合类型

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string | number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
```

#### 只读属性
```typescript
interface Person {
    readonly id: number;
}

let tom: Person = {
    id: 89757,
};

tom.id = 9527; // 此处会报错
```

- 只读的束缚存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候

### 数组的类型
#### 类型+[]

```typescript
let fibonacci: number[] = [1, 1, 2, 3, 5];
// 元素中不允许出现其他类型
// push 也只允许传入 number 类型元素
```

#### 数组泛型 Array<elemType>

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

#### 用接口表示数组
- 正常不会用此种方式，较为复杂
- 此种方式正常用来表示类数组

```typescript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

***

### 函数的类型
#### 函数声明
- 定义函数的方式有，函数声明，函数表达式（匿名函数）

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
// 输入多余或少于要求的参数是非法的
```

#### 函数表达式

```typescript
let mySum = function (x: number, y: number): number {
    return x + y;
};

// mySum 并没有做类型定义
```

```typescript
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};

// 此处 => 用来表示函数定义，左边是输入类型，右边是输出类型
```

#### 用接口定义函数形状
```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

#### 可选参数
- 用 `?` 表示可选参数

```typescript
function bindName(firstName: String, lastName?:String){
    return lastName ? firstName + lastName : firstName;
}

// 可选参数必须接在必须参数后，且可选参数后不允许出现必选参数

bindName('Tom')
```

#### 参数默认值
- ts 会将添加了默认值的参数识别为可选参数
- 这种情况下就不受 <font color=#FFA500 size=4>可选参数必须接在必须参数后</font> 的限制
```typescript
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

#### 剩余参数
- es6中可以使用 `...rest` 的方式获取剩余参数
- `...rest` 参数只能是最后一个参数

```typescript
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}
```

#### 重载
- 重载允许一个函数接受不同数量或类型的参数时，作出不同的处理
- ts会优先从最前面的函数定义开始匹配，所以多个函数定义有包含关系，需要把精确的写前面

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

***

### 类型断言
- 语法

```typescript
值 as 类型  // React 中必须使用此种方式
// OR
<类型>值
```

#### 类型断言用途

