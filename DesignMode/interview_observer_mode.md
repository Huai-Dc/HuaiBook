## 观察者模式（发布-订阅者模式）
- 定义对象间的一种一对多的依赖关系，当一个对象状态发生改变时，所有依赖他的对象都将得到通知与更新，观察者模式提供了一个订阅模型，
其中对象订阅事件并在发生时得到通知，这种模式是事件驱动的编程基石，有利于良好的面向对象设计

## 实现
- 最简单的实现就是事件监听
```js
// 订阅页面的点击事件
document.addEventListener("click", callback);

document.body.click()
```
