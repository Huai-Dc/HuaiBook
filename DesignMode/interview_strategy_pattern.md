## 策略模式
### 定义
- 定义一系列的算法，把他们一个个封装起来，目的就是将算法的实现与使用分离，避免多重判断条件，更具有拓展性

### demo
- 超市打折，vip5折，老客户7折，新客户9折
- 普通实现

```js
function discountPrice(personType, price){
    if(personType === "VIP"){
        return price*0.5
    }else if(personType === "old"){
        return price*0.7
    }
    return price*0.9
}
```

- 策略模式

```js
function vipPrice(){ this.discount = 0.5; }  

vipPrice.prototype.getPrice = function(price) { 
 return price * this.discount; 
} 

function oldPrice() { 
 this.discount = 0.7; 
} 
oldPrice.prototype.getPrice = function(price) { 
 return price * this.discount; 
} 
// 对于普通客户 
function Price() { 
 this.discount = 0.9; 
} 
 
Price.prototype.getPrice = function(price) { 
 return price ; 
}
// 上下文，对于客户端的使用 
function Context() { 
 this.name = ''; 
 this.strategy = null; 
 this.price = 0; 
} 
 
Context.prototype.set = function(name, strategy, price) { 
 this.name = name; 
 this.strategy = strategy; 
 this.price = price; 
} 
Context.prototype.getResult = function() { 
 console.log(this.name + ' 的结账价为: ' + this.strategy.getPrice(this.price)); 
} 
let context = new Context(); 
let vip = new vipPrice(); 
context.set ('vip客户', vip, 200); 
context.getResult(); // vip客户 的结账价为: 100 
let old = new oldPrice(); 
context.set ('老客户', old, 200); 
context.getResult(); // 老客户 的结账价为: 140
let Price = new Price(); 
context.set ('普通客户', Price, 200); 
context.getResult(); // 普通客户 的结账价为: 180
```

### 总结
- 当我们代码中有多个判断分支，每个条件分支都会引起改类的特定行为以不同的方式作出改变，这时候就可以使用策略模式，能更好的进行单元测试。
- 解耦代码，提高可拓展性
