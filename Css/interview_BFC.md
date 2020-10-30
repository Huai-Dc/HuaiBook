### 是什么
- 浮动元素和绝对定位元素，非块级盒子的块级容器以及overflow不为visible的块级盒子

### 触发条件
- 根元素
- 浮动元素（元素float不为none）
- 绝对定位元素（absolute fixed）
- 行内块元素 （inline-block）
- 表格单元格 （display:table-cell）
- 表格标题
- 匿名表格单元格元素
- overflow 值不为 visible的块元素
- 弹性元素 display flex inline-flex
- 网格元素 display grid inline-grid

### 渲染规则
- BFC垂直方向边距重叠
- BFC区域不会与浮动元素的box重叠
- BFC是个独立容器，外面元素不会影响内部元素
- 计算浮动元素高度时，浮动元素也会参与计算（overflow:hidden 清除浮动）

### 应用场景
- 清除浮动 防止由于浮动导致的父元素高度塌陷
- 避免外边距折叠（两个相邻的div margin 会重叠到一起 不会一起计算， 设置后可避免

### 清除浮动
- 浮动元素会导致父元素高度坍塌，影响布局
    - 将父类设置为BFC， 一般使用 `overflow：hidden`，缺点是会裁剪溢出元素
    - 使用clear属性
    ```css
    .clearfix::after {
      content: '';
      display: table; // clear 属性在块级元素下才能起作用
      clear: both;
    }
    .clearfix {
      *zoom: 1; // IE6+ 兼容
    }
    ```

- 使用 `zoom` IE独有的属性，用来检索或设置对象的缩放比例。设置会触发`hasLayout` 属性，重新计算高度，解决高度坍塌问题。
