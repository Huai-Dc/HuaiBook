## Vue 高频题目
### 答题要点
- 多给点答案内容
    - 阐述原理内容
    - 优缺点，如何规避
    - 不同版本的如何改进的（如vue2-vue3）
    - 特例的处理
- 结构化的回答思路
    - 总分总
    - 3w1h
- 升华回答
    - 相关知识点，在项目中的应用
    - 原理
    - 库的设计思想，自己的理解
    
### v-if与v-for哪个优先级高？常识
- 1.先给出结论  
    - `v-for`优先于`v-if`被解析
- 2.为什么是这样？
    - 做过实验，输出的渲染函数中可以看出会先执行循环再判断
- 3.能放一起吗？
    - 不应该放到一起，因为哪怕只渲染列表中的一小部分元素，也得在每次重新渲染的时候遍历整个列表
- 4.如果不能改怎么办
    - 通常为了过滤列表中的数据，此时我们可以先过滤（计算属性），再返回
    - 避免渲染本应该被隐藏的列表，此时可以直接将`v-if`移动到父级元素上
- 5.总结
    - 永远不要把`v-if`与`v-for`用到同一个元素上
- 6.源码 打印出 app.$options.render 可以看到
    - genElement方法中 genFor在genIf 前


### key的作用
