## 工厂模式
- 用于创建对象的设计模式， 不暴露创建对象的具体逻辑，将逻辑封装于函数中
### 分类
- 简单工厂（静态工厂方法）
- 复杂工厂
- 抽象工厂
### 简单工厂
- 传入一个合法参数，就能获取想要的对象， 不需要new 关键字来创建新实例
- 函数内部包含所有对象的构造与逻辑判断
- 使用一个类来生成实例， 缺点就是当类型较多的时候不利于维护

```js
let factory = function (role) {
    function superman() {
        this.name ='超级管理员';
        this.role = ['修改密码', '发布消息', '查看主页']
    }
    
    function commonMan() {
        this.name = '普通游客';
        this.role = ['查看主页']
    }
    
    switch(role) {
        case 'superman':
            return new superman();
            break;
        case 'man':
            return new commonMan();
            break;
        default:
            throw new Error('参数错误')
    }
}

let superman = factory('superman');
let man = factory('man');
```

### 复杂工厂模式
```js
// 安全模式创建的工厂方法函数
let factory = function (role) {
    if(this instanceof factory) {
        var s = new this[role]();
        return s;
    } else {
        return new factory(role);
    }
}

factory.prototype = {
    admin: function() {
        this.name = '平台用户';
        this.role = ['登录页', '主页']

    },
    common: function() {
        this.name = '游客';
        this.role = ['登录页']
    },
    test: function() {
        this.name = '测试';
        this.role =  ['登录页', '主页', '测试页'];
        this.test = '我还有一个测试属性哦'
    }
}

let admin = new factory('admin');
let common = new factory('common');
let test = new factory('test');
```
- 相较于简单工厂， 此处新增构造只需要修改下面的部分
- 需要用new关键字实例化新的实例

### 抽象工厂
- [参考](https://www.cnblogs.com/jswang/p/7660669.html)
- 抽象工厂是实现子类继承父类的方法
- js 中没有实现 abstract， 可以在类方法中抛出错误来模拟
- 将实例的创建放到具体工厂的子类中，降低代码耦合度， 方便后期维护
```js
let WechatUser = function() {}
WechatUser.prototype = {
    getName: function() {
        return new Error('抽象方法不能调用');
    }
}
```
