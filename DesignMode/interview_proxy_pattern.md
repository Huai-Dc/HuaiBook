## 代理模式
- 代理模式主要为其他对象提供一种代理，以控制对这个对象的访问，主要解决在直接访问对象时带来的问题，如：访问的对象在远程机器上，在面向对象
系统中，有些对象由于某些原因（如对象创建开销大，某些操作需要安全控制，或需要进程外的访问），直接访问会给使用者或系统结构带来很多麻烦，
我们可以在访问该对象时，加上一个此对象的访问层
- 代理模式最基本的形式是对访问进行控制，代理对象与另一个对象实现的是同样的接口，实际上工作还是在本体处理

```js
(function(){
    // 目标对象，真正被代理的对象
    function Subject(){}
    Subject.prototype.request = function(){}
    
    // 代理对象
    function Proxy(realSubject){
        this.realSubject = realSubject
    }
    Proxy.prototype.request = function (){
        this.realSubject.request()
    }
}())
```

## 总结
- 代理模式中较为常见的为 虚拟代理，用于控制对那种创建开销很大的本体访问，它会把本体的实例化推迟到有方法被调用的时候
- 前端的图片懒加载就可以使用虚拟代理

