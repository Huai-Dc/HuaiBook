## 盒模型
- 组成：margin,border,padding,content
### 分类
- 标准盒模型 :width 指的是 content的宽
- 怪异盒模型（IE盒模型）:width 指的是border+padding+content

### 切换
- `box-sizing: content-box` 标准盒模型
- `box-sizing: border-box` 怪异盒模型

### margin 与 margin 重叠部分的计算
- 水平边距不会重合
- 两个或以上的块级盒模型相邻的垂直margin会重叠，计算方法如下
    - 全部为正值，取最大的
    - 不全为负值，则都取绝对值，然后用正值减最大值
    - 全为负值，都取绝对值，然后用0减最大值
- 相邻的盒模型，如果其中一个为浮动（float）,则垂直margin不会重叠，并且浮动的盒模型与其子元素也是这样
- 设置设置了overflow属性的元素与其子元素的margin不会重叠，值为visible的除外
- 设置了绝对定位的盒模型垂直margin不会被重叠
- 设置了display：inline-block的元素，垂直margin不会重叠
- 根元素的垂直margin不会被重叠
