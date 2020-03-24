### template
- 一个替换挂载的元素模板, html的方式做渲染，template 最终还是通过render的方式编译的


### render
- 字符串模板的替代方案，允许发挥js的最大编程能力
- 参数 h 就是 createElement
- createElement有三个参数
    - 第一个（必要参数）主要提供 dom标签名称类型可以是字符串、对象、数组
    - 第二个（可选）用于设置dom的样式、属性、传递的组件参数、绑定事件等
    - 第三个（可选）主要是指该结点下还有其他结点，用于设置分发的内容，包括新增的其他组件。注意，组件树中的所有VNode必须是唯一的
    
### 区别
https://www.cnblogs.com/liqiang666/p/12175465.html
- 都是创建html模板的
- template 逻辑相对简单render 适合复杂逻辑
- 使用者template理解相对容易但是灵活性不足， 自定义render函数灵活性较高
- render 性能较高 template 性能较低
- render 渲染函数没有编译过程
- render 函数的优先级高于template 但是要注意 双括号语法不能再次使用
