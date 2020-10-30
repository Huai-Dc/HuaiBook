
### 类型别名
```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```

- 类型别名常用于联合类型

### 字符串字面量类型
- 字符串字面量类型用来约束取值只能是某几个字符串中的一个。

```typescript
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dblclick'); // 报错，event 不能为 'dblclick'

// index.ts(7,47): error TS2345: Argument of type '"dblclick"' is not assignable to parameter of type 'EventNames'.
```

- 上例中，我们使用 `type` 定义了一个字符串字面量类型。
- 注意 类型别名与字符串字面量类型都是使用 `type` 定义

### 元组
- 数组合并了相同类型的对象，而元组合并了不同类型的对象
- 当赋值或访问一个已知索引的元素时，会得到正确的类型

```typescript
let tom: [string, number] = ['Tom', 25];
```

```typescript
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;

tom[0].slice(1);
tom[1].toFixed(2);
```

- 可以只赋值其中一项

```typescript
let tom: [string, number];
tom[0] = 'Tom';
```

- 但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项

```typescript
let tom: [string, number];
tom = ['Tom'];

// Property '1' is missing in type '[string]' but required in type '[string, number]'.
```

#### 越界元素
- 当添加越界元素时，它的类型会被限制为元祖中每个类型的联合类型

```typescript
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true); // true 不属于 联合类型 string | number 中的一项
```

### 枚举
- 枚举类型用于取值被限定在一定场景范围内的场景，比如一周只能有七天，颜色限定为红蓝绿等

```typescript
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
```

- 枚举成员被赋值为从 0 开始递增的数字

```typescript
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

#### 手动赋值
```typescript
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```
- 跟在手动赋值后未赋值的枚举项，会自动递增步长为1
- 若手动赋值的枚举项与未赋值的重复了，ts是不会察觉到的

```typescript
enum Days {Sun = 3, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 3); // true
console.log(Days["Wed"] === 3); // true
console.log(Days[3] === "Sun"); // false
console.log(Days[3] === "Wed"); // true
```

- 所以没有特殊情况最好别这样处理。
- 手动赋值的枚举项允许可以为非数字，此时需要使用类型断言来让 tsc 无视类型检查 
- 手动赋值的枚举项也可以为小数或负数，此时后续未手动赋值的项的递增步长仍为 1

#### 常数项与计算所得项
- 枚举项有两种类型： 常数项与计算所得项

```typescript
enum Color {Red, Green, Blue = "blue".length};  // "blue".length 就是一个计算所得项
```

- 上面的例子不会报错，但是紧接在后面的是未手动赋值的项，那么它就会因为无法获得初始值而报错
- 当满足以下条件时，枚举成员被当作常数
    - 不具有初始化函数并且之前的枚举成员是常数，该情况当前枚举成员的值为上一个枚举成员值 加 1，但是第一个例外，如果它没有初始化方法，则初始值为0
    - 枚举成员使用常数枚举表达式初始化，常识表达式是ts表达式的子集
    ```$xslt
        1. 数字字面量
        2.引用之前定义的常数枚举成员
        3.带括号的常数枚举表达式
        4.+, -, ~ 一元运算符应用于常数枚举表达式
        5.+, -, *, /, %, <<, >>, >>>, &, |, ^ 二元运算符，常数枚举表达式做为其一个操作对象。
            求值后若为 NaN 或 无穷值则会报错    
    ```

#### 常数枚举
- 常数枚举使用的是 const enum 定义枚举类型

```typescript
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

- 常数枚举与普通枚举的区别为，它会在编译阶段被删除，并且不能包含计算成员
- 上个例子的编译结果为

```typescript
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

#### 外部枚举
- 使用 `declare enum` 定义的枚举类型
- `declare` 定义的类型只会用于编译时的检查，结果中会被删除

```typescript
// 上例的编译结果为
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

- 同时使用 declare 与 const 也是可以的。 编译结果为

```typescript
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

### 类
- 传统方法中，js通过构造函数实现类，通过原型链实现继承，在ES6才迎来了类

#### 类的概念
- 类：定义了一件事物的抽象特点，包含其属性与方法
- 对象：类的实例，通过 `new` 生成
- 面向对象（OOP）三大特性 封装、继承、多态
- 封装：将对象的操作细节隐藏起来，对外只暴露接口。外界调用接口不需要知道实现的细节，就能通过接口访问该对象，同时也保证外部不能任意更改对象内部数据
- 继承：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多态：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应，如：Cat 与 Dog 都继承自Animal，但是分别实现了 cry 叫的方法。我们无需了解其是Cat或Dog，就可以直接调用 cry 方法，程序会自动判断
- 存取器：用于改变属性的读取与赋值行为
- 修饰符：用于限定成员或类型的性质 如 public
- 抽象类：抽象类是其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 接口：不同类之间共有的属性或方法，可以抽象成一个接口，接口可以被类实现。一个类只能继承自另一个类，但是可以实现多个接口

#### ES6中类的用法
##### 属性与方法
- 使用 `class` 定义类， 使用 `constructor` 定义构造
- 通过 `new` 关键字生成实例时，会自动调用构造函数

```typescript
class Animal {
    public name;
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        return `My name is ${this.name}`;
    }
}

let a = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

##### 类的继承
- 使用 `extends` 关键字实现继承 子类中使用 `super` 关键字调用父类的构造函数与方法

```typescript
class Cat extends Animal {
  constructor(name) {
    super(name); // 调用父类的 constructor(name)
    console.log(this.name);
  }
  sayHi() {
    return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
  }
}

let c = new Cat('Tom'); // Tom
console.log(c.sayHi()); // Meow, My name is Tom
```

##### 存取器
- 使用 `getter` 与 `setter` 改变属性的赋值与读取

```typescript
class Animal {
  constructor(name) {
    this.name = name;
  }
  get name() {
    return 'Jack';
  }
  set name(value) {
    console.log('setter: ' + value);
  }
}

let a = new Animal('Kitty'); // setter: Kitty
a.name = 'Tom'; // setter: Tom
console.log(a.name); // Jack
```

##### 静态方法
- 使用 `static` 修饰符修饰的方法称为静态方法，不需要实例化就可以直接通过类调用

#### ES7 中类的用法
##### 实例属性
- ES6中实例属性只能通过在构造函数中的 `this.xxx` 定义，ES7 提案中可以直接在类里定义

```typescript
class Animal {
  name = 'Jack';

  constructor() {
    // ...
  }
}

let a = new Animal();
console.log(a.name); // Jack
```

##### 静态属性
- ES7 提案中，可以使用 `static` 定义一个静态属性：

```typescript
class Animal {
  static num = 42;

  constructor() {
    // ...
  }
}

console.log(Animal.num); // 42
```

#### TypeScript 中类的用法
- public private 和 protected
    - `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
    - `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
    - `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的
    
```typescript
class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

```typescript
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

- `TypeScript` 编译之后的代码中，并没有限制 `private` 属性在外部的可访问性。
- `private` 修饰的属性与方法在子类中也是不允许访问的
- 而如果是用 `protected` 修饰，则允许在子类中访问：

```typescript
class Animal {
  protected name;
  public constructor(name) {
    this.name = name;
  }
}

class Cat extends Animal {
  constructor(name) {
    super(name);
    console.log(this.name);
  }
}
```

- 当构造函数修饰为 `private` 时，该类不允许被继承或者实例化：

```typescript
class Animal {
  public name;
  private constructor(name) {  // 构造修饰符为 private
    this.name = name;
  }
}
class Cat extends Animal {   // 不允许被继承
  constructor(name) {
    super(name);
  }
}

let a = new Animal('Jack');   // 不允许被实例化
```

- 当构造函数修饰为 `protected` 时，该类只允许被继承：

```typescript
class Animal {
  public name;
  protected constructor(name) {
    this.name = name;
  }
}
class Cat extends Animal {
  constructor(name) {
    super(name);
  }
}

let a = new Animal('Jack');

// index.ts(13,9): TS2674: Constructor of class 'Animal' is protected and only accessible within the class declaration.
```

#### 参数属性
- 修饰符和 `readonly` 还可以使用在构造函数参数中，等同于类中定义该属性同时给该属性赋值，使代码更简洁。
##### readonly

```typescript
class Animal {
  readonly name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';  // name 被修饰为 只读 所以此处不能赋值
```

- 注意如果 `readonly` 与其他修饰符同时存在，需要写在后面

```typescript
class Animal {
  // public readonly name;
  public constructor(public readonly name) {
    // this.name = name;
  }
}
```

#### 抽象类
- `abstract` 用于定义抽象类和其中的抽象方法。
- 抽象类不允许被实例化

```typescript
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi();
}

let a = new Animal('Jack');  // Animal 是抽象类 不允许实例化
```

- 抽象类中的抽象方法必须被子类实现

```typescript
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi(); // 抽象方法
}

class Cat extends Animal {  // Cat 中没实现抽象方法
  public eat() {
    console.log(`${this.name} is eating.`);
  }
  // public sayHi(){} // 实现该方法后就不会报错
}

let cat = new Cat('Tom');
```

#### 类的类型

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi(): string {
    return `My name is ${this.name}`;
  }
}

let a: Animal = new Animal('Jack'); // 类似基本数据类型的声明
console.log(a.sayHi()); // My name is Jack
```

### 类与接口
- 之前学过，接口可以用于 对象形状的描述，此处主要介绍接口对类的一部分行为进行抽象

#### 类实现接口
- 实现是面向对象中一个重要的概念一般来说一个类只能继承自另一个类
- 有时候不同类之间有一些共有的特性，这时候就能把特性提取为接口用 `implements` 实现
- 栗子：门是一个类 防盗门是子类，防盗门有个报警器功能，这时候另一个类也有类似报警器的功能，就可以考虑将报警器提取出来，作为一个接口

```typescript
interface Alarm {  // 接口
    alert(): void;
}

class Door {
}

class SecurityDoor extends Door implements Alarm {  // 防盗门实现报警器
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {  // 车实现报警器
    alert() {
        console.log('Car alert');
    }
}
```

- 一个类可以实现多个接口

#### 接口继承接口
- 接口与接口之间可以是继承关系

```typescript
interface Alarm {
    alert(): void;
}

interface LightableAlarm extends Alarm {
    lightOn(): void;
    lightOff(): void;
}
```

- LightableAlarm 继承了 Alarm 除了有 alert 方法外 还有灯开关的方法

#### 接口继承类
- 常见的面向对象语言中，接口是不能继承类的
- 实际上在声明 `class Point` 时，除了创建一个名为 `Point` 的类之外，还创建了一个名为 `Point` 的类型
- 所以即可以将 Point 当作类，也可以当作一个类型

```typescript
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    
    printPoint() {
        console.log(this.x, this.y);
    }
}

interface Point3d extends Point {  // 接口继承类
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3, printPoint() {}};
```

- 声明 Point 类时创建的 Point 类型只包含其中的实例属性和实例方法
- 接口继承类，实际上还是接口继承接口 上面例子等价于。

```typescript
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    
    printPoint() {
        console.log(this.x, this.y);
    }
}

interface PointInstanceType {
    x: number;
    y: number;
    printPoint(): void;
}

interface Point3d extends PointInstanceType {  // 接口继承接口
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3, printPoint() {}};
```
- 上栗中类型 Point 与 类型 PointInstanceType 是等价的
- 接口继承类的时候，也只能继承其实例属性与实例方法

### 泛型
- 泛型是指在定义函数、接口或类时，不预先指定具体的类型，而在使用时再指定类型的一种特性

```typescript
function createArray(length: number, value: any): Array<any> {
    let result = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```

- 上栗中，我们使用之前的数组泛型来定义返回值的类型。但是其没有准确定义返回值类型，这时候就可以用泛型

```typescript
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

- 上栗中，在函数名后添加 `<T>` 其中 `T` 用来指代任意输入类型

#### 多个类型参数
- 定义泛型的时候，可以一次定义多个类型参数：

```typescript
// 交换输入的元组
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
}

swap([7, 'seven']); // ['seven', 7]
```

#### 泛型的约束
- 在函数内部使用泛型时，由于事先不知道泛型属于哪种类型，所以不能随意操作它的属性或方法
- 这时候我们可以对泛型进行约束，只允许传入含有某些属性或方法的变量

```typescript
interface Lengthwise {
    length: number;
}
// extends 约束了泛型T必须符合接口 Lengthwise 的形状
function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

- 多个类型也是可以相互约束

```typescript
// 保证了 U 中有的 T 中都有
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });
```

#### 泛型接口

```typescript
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```

#### 泛型类
- 与泛型接口类似，泛型也可以用于类的类型定义

```typescript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

#### 泛型参数的默认类型
- TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。

```typescript
// 默认类型为 string
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```

### 声明合并
- 如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型

#### 函数的合并
- 之前学过可以使用重载定义多个函数类型

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

#### 接口的合并
- 接口中的属性在合并时，会简单的合并到一个接口中

```typescript
interface Alarm {
    price: number;
}
interface Alarm {
    weight: number;
}
```

- 相当于

```typescript
interface Alarm {
    price: number;
    weight: number;
}
```

- 合并的属性的类型必须是唯一的

```typescript
interface Alarm {
    price: number;
}
interface Alarm {
    // 虽然重复了，但是类型都是 `number`，所以不会报错
    // 如果类型不一致就会导致错误
    price: number;  
    weight: number;
}
```
