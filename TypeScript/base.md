### 原始数据类型
- 布尔值、数值、字符串、null、undefined、Symbol

#### Boolean
- 声明

```typescript
let flag:boolean = false;

let flag2: boolean = new Boolean(1) // 此处会报错，
// 因为 new Boolean返回的不是布尔值,但是此处依然会返回 true
```

#### Number 数值

- 声明

```typescript
let num: number = 1
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

#### String 字符串

- 声明

```typescript
let str: string = 'hello'
```

#### 空值

- js中没有空值的概念，在ts中可以用`void`表示没有返回值的函数

```typescript
function alertFunction():void{
    alert("no return val");
}
```

- 声明一个 `void` 类型的变量没有任何用处，只能为其赋值 `undefined`、`null`


#### Undefined Null

- null、undefined 与void的区别为 null、undefined是所有类型的子类型

```typescript
let num:number = undefined;

let u: undefined;  // 此处若声明为 void类型则会报错
let num2: number = u;
```

### 任意值
- 是什么
    - 普通类型在赋值过程中改变类型是不允许的
    
    ```typescript
       let str: string = 'world'
       str = 7  // 报错
    ```
  
    - 若声明为 `any` 类型则上面的操作时允许的
 
- 任意值得属性与方法
    - 在任意值上访问任意属性都是允许的
    
    ```typescript
    let anyThing: any = 'hello';
    console.log(anyThing.myName);
    console.log(anyThing.myName.firstName);
    ```
    
    - 也允许调用任何方法。可以认为声明一个变量为任意值后，对他任何操作，返回的内容都是任意值
    
- 未声明类型的变量
    - 变量如果在声明的时候，未指定其类型，则会被识别为任意值类型
    
    ```typescript
    let something;  // 此处若赋值，则会根据类型推论，推断一个具体类型
    something = 'seven';
    something = 7;
    
    something.setName('Tom');
    ```
    
### 类型推论
- 如果没有明确指定类型，则ts会按照类型推论的规则，推断出一个类型

#### 什么是类型推论

```typescript
let myFavoriteNumber = 'seven';  // 等价于 let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;  // 会报错
```

- 如果定义的时候没有赋值，则不管之后有没有赋值，都会被推断为`any`类型完全不被类型检测

### 联合类型
- 取值可以是多种类型中的一种

#### demo

```typescript
let myFavoriteNumber: string | number;  // 使用 | 分隔类型
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

#### 访问联合类型的属性与方法
- 当ts不确定一个联合类型的变量属于哪种类型时，只能访问联合类型中所有类型的共有属性或方法

```typescript
function getLength(something: string | number): number {
    return something.length;  // number 没有 length 方法
}
```

### 对象的类型--接口
- 在ts中使用接口来定义对象的类型

#### 什么是接口
- 接口是对行为 抽象，具体的实现由类去完成。ts中的接口除了可以对一部分行为进行抽象，也可以对 对象的形状进行描述

```typescript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

- 定义的变量比接口少一些属性是不允许的

```typescript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom', // 由于缺少age属性所以会报错
};
```

- 多一些属性当然也是不允许的，赋值时，变量的形状必须与接口保持一致

#### 可选属性
- 有时，我们希望不完全匹配一个形状，那么就能用可选属性

```typescript
interface Person {
    name: string;
    age?: number;  // 可以用
}

let tom: Person = {
    name: 'Tom'
};
```

#### 任意属性
- 有时希望一个接口允许有任意的属性，可以使用以下方式

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

- 一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它类型的子集

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,  // number 不是 string类型的子集 所以会报错
    gender: 'male'
};
```

- 一个接口中只能定义一个任意属性。如果接口中有多个类型的属性，则可以在任意属性中使用联合类型

#### 只读属性
- 有时候希望对象中的一些字段只能在创建的时候被赋值，那么就能使用 `readonly` 定义

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527;  // id为只读属性
```

- 只读属性的约束，存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候，即就算开始没赋值，后面再赋值也会报错

### 数组的类型
#### 「类型+方括号」表示

```typescript
let fibonacci: number[] = [1, 1, 2, 3, 5]; // 此时数组中不允许出现其他类型
fibonacci.push("6")  // 此处也会报错。数组部分方法也会对其类型进行限制
```

#### 数组泛型 表示

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

#### 接口 表示

```typescript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```
- `NumberArray`表示：只要索引的类型是数字时，那么值的类型必须是数字
- 常用来表示类数组，正常不用来描述数组（比较复杂）

#### 类数组
- 类数组不是数组 如 arguments

```typescript
function sum() {
    let args: number[] = arguments; // argument 不是数组 所以会报错
}
```

```typescript
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
```

#### any 在数组中的应用
- `any`表示在数组中可以出现任意类型元素


### 函数类型
#### 函数声明
- js中有两种常见的定义函数方式，函数声明与函数表达式
- 一个函数有输入与输出，在ts中可以对其进行约束

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
```

- 输入多余或少于函数规定的参数是不被允许的

#### 函数表达式

```typescript
let mySum = function (x: number, y: number): number {
    return x + y;
};
```
- 上面的例子只对等号右边的匿名函数进行类型定义，左边的`mySum`是通过类型推论推断的，若手动给`mySum`添加类型则为

```typescript
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
```
- 在ts的类型定义中 `=>` 用来表示函数定义，左边是输入类型，需要用括号括起

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
- 前面提到输入多余或缺少参数，是不允许的，我们用`?`表示可选参数

```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```
- 可选参数必须接在必选参数后，即可选参数后不允许出现必选参数

#### 参数默认值
- ts 会将添加了默认值的参数识别为可选参数

```typescript
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

- 此时就不受「可选参数必须接在必需参数后面」的限制了：

#### 剩余参数
- 在es6中可以使用`...rest`的方式获取函数中的剩余参数

```typescript
function push(array, ...items) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a: any[] = [];
push(a, 1, 2, 3);
```

- 事实上 `item` 是一个数组，可以用数组类型来定义

```typescript
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```

- 注意，rest 参数只能是最后一个参数。

#### 重载
- 重载允许一个函数接受不同数量或类型的参数时，作出不同处理

```typescript
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```
- 这样有个缺点，不能精确的表达，输入为数字时，输出应该也为数字，字符串同理
- 这时可以用重载，定义多个 `reverse`

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

- ts会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面

### 类型断言
- 可以用来手动指定一个值得类型

```$xslt
值 as 类型
```

或

```$xslt
<类型>值
```

- 推荐使用第一种，因为react中必须使用前者

#### 用途
##### 将一个联合类型断言为其中一个类型
- 前面提到当ts不确定一个联合类型变量为哪种类型时，只能访问联合类型共有的属性或方法
- 有时候需要在不确定类型时就访问其中一个类型的属性或方法，此时可以用类型断言

```typescript
interface Cat {
    name: string;
    run(): void;
}
interface Fish {
    name: string;
    swim(): void;
}

function isFish(animal: Cat | Fish) {
    // 此处将animal 断言为 Fish 类型
    if (typeof (animal as Fish).swim === 'function') {
        return true;
    }
    return false;
}
```

- 不能滥用类型断言，类型断言只能欺骗 ts 编译器，无法避免运行时错误。尽量避免断言后调用方法或引用深层属性

```typescript
interface Cat {
    name: string;
    run(): void;
}
interface Fish {
    name: string;
    swim(): void;
}

function swim(animal: Cat | Fish) {
    (animal as Fish).swim();
}

const tom: Cat = {
    name: 'Tom',
    run() { console.log('run') }
};
swim(tom);  // 此处tom 并没有 swim 方法所以运行时会报错，但是编译时不会报错
// Uncaught TypeError: animal.swim is not a function`
```

##### 将一个父类断言为更加具体的子类
- 当类之间有继承时，类型断言也是很常见的
```typescript
class ApiError extends Error {
    code: number = 0;
}
class HttpError extends Error {
    statusCode: number = 200;
}

function isApiError(error: Error) {
    // 此处更适合用 instanceof 但是有时候 ApiError 与 HttpError 只是接口就会报错
    if (typeof (error as ApiError).code === 'number') {
        return true;
    }
    return false;
}
```

```typescript
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (error instanceof ApiError) { // 接口是一个类型，而不是真正的值，在编译结果中会被删除，就无法使用 instanceof 
        return true;
    }
    return false;
}
```

- 此时只能使用类型断言，判断是否存在 code 属性

```typescript
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (typeof (error as ApiError).code === 'number' ) { // 接口是一个类型，而不是真正的值，在编译结果中会被删除，就无法使用 instanceof 
        return true;
    }
    return false;
}
```

##### 将任意类型断言为 any
- 在理想的情况下，ts类型系统运行良好，当我们引用一个此类型上不存在的方法或属性时会报错。
- 有时候，我们非常确定代码不会出错如：

```typescript
window.foo = 1;
```

- 此时我们可以用 `as any` 临时将 `window` 断言为 `any`

```typescript
(window as any).foo = 1
```

##### 将any 断言为一个具体类型
- 目的提高代码的可维护性

#### 类型断言的限制
- 联合类型可以被断言为其中一个类型
- 父类可以被断言为子类
- 任何类型都可以被断言为any
- any 可以被断言为任意类型
- 要使得 A 被断言为 B 只需要 A 兼容 B 或 B 兼容 A

#### 双重断言

- 既然 任何类型都能被断言为 any， any 可以被断言为任意类型

```typescript
interface Cat {
    run(): void;
}
interface Fish {
    swim(): void;
}

function testCat(cat: Cat) {
    return (cat as any as Fish);
}
```

- 若使用上面的双重断言，很可能出现运行时错误，除非迫不得已，否则不要用双重断言

#### 类型断言 VS 类型转换
- 类型断言只会影响编译时的类型，类型断言语句会在编译后被删除

```typescript
function toBoolean(something: any): boolean {
    return something as boolean;
}

toBoolean(1);
// 返回值为 1
```

- 等价于下面代码，所以类型断言不是类型转换，不会影响到变量的类型

```js
function toBoolean(something) {
    return something;
}

toBoolean(1);
```

```typescript
function toBoolean(something: any): boolean {
    return Boolean(something);  // 直接使用转换方法
}

toBoolean(1);
```

#### 类型断言 VS 类型声明

- 类型断言

```typescript
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
```

- 类型声明

```typescript
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

// 因为此处 tom 声明时确定了类型为 Cat getCacheData 返回值为 any
const tom: Cat = getCacheData('tom');
tom.run();
```

```typescript
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

const animal: Animal = {
    name: 'tom'
};
let tom: Cat = animal;  
// 此处由于左边定位为 Cat 类型 右边的值为 Animal Animal 为Cat的父类
// 不能将父类的实例赋值给子类的变量
// 区别在于
//    animal 断言为 Cat 需要满足 Animal 兼容 Cat 或 Cat 兼容 Animal
//    animal 赋值给 Cat 需要满足 Cat 兼容 Animal （此处子类不兼容父类）
```

- 可以看出 类型声明比类型断言更加严格，所以优先使用 类型声明，这也比类型断言的as更加优雅

#### 类型断言 VS 泛型

```typescript
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
```

```typescript
function getCacheData<T>(key: string): T {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData<Cat>('tom');
tom.run();
```

- 泛型是解决这类问题最优的方式

### 声明文件
- 当使用第三方库时，需要引用它的声明文件，才能获得对应的代码补全、接口提示的功能

#### 什么是声明语句
- 加入我们要用jquery库，常见的方式是通过 `<script>` 引入然后就可以使用全局变量 $
- ts 并不知道 `$` 或 `jQuery` 是什么，这时需要 `declare var` 来定义它的类型

```typescript
declare var jQuery: (selector: string) => any;

jQuery('#foo');
``` 

- 上述例子中 `declare var` 并不会真正定义一个变量， 只是定义了全局变量 `jQuery` 的类型，用于编译检测，在结果中会被删除

```typescript
jQuery('#foo');  // 编译结果
```

#### 什么是声明文件
- 通常我们会将声明语句放到一个单独的文件中（jQuery.d.ts）,这就是声明文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他`*.ts`的文件就可以获得 `jQuery` 的类型定义
- 若还是无法解析，可以检查下 `tsconfig.json` 中的 `files`、`include`、`exclude` 配置，确保其包含了`jQuery.d.ts`文件

##### 第三方声明文件
- 推荐使用 `@types` 统一管理第三方库的声明文件，直接使用npm 安装对应的声明模块即可

```bash
npm install @types/jquery --save-dev
```

#### 书写声明文件
- 在不同场景下，声明文件的内容和使用方式会有所区别
- 库的使用场景
    - 全局变量 通过 `<script>` 引入的三方库，注入全局变量
    - npm 包 通过 `import a from 'a'` 导入
    - UMD 库 即可以通过 `<script>` 引入，也可以通过 `import` 导入
    - 直接拓展全局变量 通过 `<script>` 引入后，改变一个全局变量的结构
    
##### 全局变量
- 使用全局变量的声明文件时，如果是以 `npm install @types/xxx --save-dev` 安装的，则不需要任何配置。
- 如果是将声明文件直接存放于当前目录中，则建议和其他源码一起放到 src 目录下
- 全局变量的声明文件主要有以下几种语法
    - declare var 声明全局变量
    - declare function 声明全局方法
    - declare class 声明全局类
    - declare enum 声明全局枚举类型
    - declare namespace 声明（含有子属性的）全局对象
    - interface 和 type 声明全局类型
 
###### declare var
- 与其相似的还有 `declare let`  `declare const`
- 一般来说全局变量都是不允许修改的，所以大多用 const

```typescript
declare const jQuery: (selector: string) => any;

jQuery('#foo');
```

- 注意声明语句中只能定义类型，切勿在声明语句中定义具体实现

```typescript
// 报错
declare const jQuery = function(selector) {
    return document.querySelector(selector); 
};
```

###### declare function
- declare function 用来定义全局函数的类型

```typescript
declare function jQuery(selector: string): any;  // jQuery 就是一个函数
```

- 在函数类型的声明语句中，函数重载也是支持的

```typescript
declare function jQuery(selector: string): any;
declare function jQuery(domReadyCallback: () => any): any;
```

###### declare class
- 当全局变量是一个类的时候，我们用 declare class 来定义它的类型

```typescript
// src/Animal.d.ts

declare class Animal {
    name: string;
    constructor(name: string);
    sayHi(): string;
}
```

```typescript
// src/index.ts

let cat = new Animal('Tom');
```

- declare class 语句也只能用来定义类型，不能用来定义具体的实现 否则会报错

###### declare enum
- 使用 declare enum 定义的枚举类型也称作外部枚举

```typescript
// src/Directions.d.ts

declare enum Directions {
    Up,
    Down,
    Left,
    Right
}
```

```typescript
// src/index.ts

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
- declare enum 仅用来定义类型，而不是具体的值， Directions.d.ts 仅仅会用于编译时的检查
- 其中 Directions 是由第三方库定义好的全局变量

###### declare namespace
- namespace 是 ts 早期时为了解决模块化而创造的关键字，中文称为命名空间。
- namespace 被淘汰了，但是在声明文件中，declare namespace 还是比较常用的，它用来表示全局变量是一个对象，包含很多子属性。
- 比如 jQuery 是一个全局变量，它是一个对象，提供了一个 jQuery.ajax 方法可以调用，那么我们就应该使用 declare namespace jQuery 来声明这个拥有多个子属性的全局变量。

```typescript
// src/jQuery.d.ts

declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
}
```

###### 嵌套的命名空间
- 如果对象拥有深层的层级，则需要用嵌套的 `namespace` 来声明深层的属性类型

```typescript
// src/jQuery.d.ts

declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
    namespace fn {
        function extend(object: any): void;
    }
}
```

```typescript
// src/index.ts

jQuery.ajax('/api/get_something');
jQuery.fn.extend({
    check: function() {
        return this.each(function() {
            this.checked = true;
        });
    }
});
```

- 假如 jQuery 下仅有 fn 这一个属性（没有 ajax 等其他属性或方法），则可以不需要嵌套 namespace

```typescript
// src/jQuery.d.ts

declare namespace jQuery.fn {
    function extend(object: any): void;
}
```

###### interface 和 type
- 除了全局变量之外，可能我们也希望一些类型可以暴露出来，在声明文件中，我们可以直接使用 `interface` 或 `type` 来声明一个全局的接口或类型

```typescript
// src/jQuery.d.ts

interface AjaxSettings {
    method?: 'GET' | 'POST'
    data?: any;
}
declare namespace jQuery {
    function ajax(url: string, settings?: AjaxSettings): void;
}
```

```typescript
// src/index.ts

let settings: AjaxSettings = {
    method: 'POST',
    data: {
        name: 'foo'
    }
};
jQuery.ajax('/api/post_something', settings);
```

###### 防止命名冲突
- 暴露在外层的 `interface` 或 `type` 会作为全局类型作用于整个项目，我们应该尽可能减少全局变量或全局类型的数量

```typescript
// src/jQuery.d.ts

declare namespace jQuery {
    interface AjaxSettings {
        method?: 'GET' | 'POST'
        data?: any;
    }
    function ajax(url: string, settings?: AjaxSettings): void;
}
```

- 注意在使用 这个接口时也要加上 jQuery 前缀

```typescript
// src/index.ts

let settings: jQuery.AjaxSettings = {
    method: 'POST',
    data: {
        name: 'foo'
    }
};
jQuery.ajax('/api/post_something', settings);
```

###### 声明合并
- 假如 jQuery 既是一个函数，可以直接被调用 jQuery('#foo')，又是一个对象，拥有子属性 jQuery.ajax()（事实确实如此），那么我们可以组合多个声明语句，它们会不冲突的合并起来
```typescript
// src/jQuery.d.ts

declare function jQuery(selector: string): any;
declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
}
```
