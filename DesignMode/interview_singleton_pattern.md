## 单例模式
### 定义
- 保证一个类只有一个实例，并且提供一个全局访问的访问点

### 需求
- 一些对象我们往往只需要一个如，线程池，全局缓存，浏览器的window对象，登录浮窗等

### 实现
- 用一个变量标识当前是否为某个类创建过实例，如果有则直接返回已经创建的实例

- 优点：
    - 用来划分命名空间，减少全局变量
```js
let single = (function(){
    let unique;
    function getInstance(){
        if(!unique){
            unique = new Construct();
        }
        return unique
    }
    function Construct(){
        // 构造
    }
    
    return {getInstance: getInstance}
})()
```

