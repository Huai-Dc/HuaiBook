## CSS 优化
### 加载性能
- css压缩
- 减少使用`@import`，他导入的样式需要页面加载完后才加载
- 使用简写(减少文件大小)
```css
p{
    margin-top: 1px;
    margin-right: 2px;
    margin-bottom: 3px;
    margin-left: 4px;
}
/* 替换为 */
p{margin: 1px 2px 3px 4px}
```
- `border: none;` 替代 `border: 0;`
    - border: 0; 等价于 border-width: 0;浏览器依然对width与color进行渲染

### 选择器性能
- 不使用通配符选择器，消耗性能
- 选好关键选择器，减少层级
- ID作为关键选择器，不需要在左侧添加额外层级
- 用类选择器代替标签选择器

### 渲染性能
- 减少使用浮动定位
- 使用雪碧，减少请求
- 继承属性不要重复指定
- 选择器嵌套不要超过三层

### 样式抽离
- 将相同属性的样式抽离，整合到同一个class中，提高维护性
- 样式与结构分离，采用外部引入
