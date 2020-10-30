#### 标准盒模型与怪异盒模型
- 标准盒模型 内容宽度 不包含 padding 和 border
- 怪异盒模型 内容宽度包含 padding 与 border

#### box-sizing
- 用于控制盒模型的解析模式，默认为 content-box
    - content-box 宽高指的是 content 部分
    - border-box 设置元素宽高指的是 content + padding + border 三部分

#### css 选择器
- id选择器
- class选择器
- 伪类选择器
- 元素选择器
- 属性选择器
- 子元素选择器（ul>li）
- 相邻元素选择器 （div+p）
- 可继承样式：font-size  color  font-family
- 不可继承样式：border padding margin height width

#### css 优先级算法
- ！important 优先级最高
- 行内样式权重为 1000
- ID选择器权重为 0100
- 类选择器，伪类选择器，属性选择器权重为 0010
- 元素选择器和伪元素选择器权重为 0001
- 优先级相同，选择最后一个出现的
- 继承得到的优先级最低
- 通配符 > 继承 > 浏览器默认

#### 伪类与伪元素区别
- 描述的都是不存在于文本流的东西
- 伪类用单冒号，为元素处于某种状态时添加样式，如:hover
- 伪元素用双冒号，为了兼容老浏览器也会用单冒号，创建不存在文本流中的元素，为其添加样式 如 ::before

#### 为什么会出现浮动，怎么清除
- 浮动元素碰到包含其的边框或浮动元素的边框停留，由于脱离文档流，所以文档流的块框表现得就像浮动框不存在，浮动框会飘在块框的上面
- 带来的问题
    - 父元素没办法被撑开，影响父元素的兄弟元素
    - 与浮动元素同级的元素会跟随其后
    - 影响页面显示结构
- 处理方式
    - 父级div定义height
    - 添加样式 clear：both
    - 设置父元素 overflow hidden 或者auto
    - 父级div 定义zoom: 1

#### 全屏滚动原理
- 类似于轮播 比如有五个页面 设置高度为 500% 只展示100%，剩下的通过transform 进行y轴定位也可以通过 margin-top实现
- overflow: hidden; transform: all 1000ms ease;

#### 什么是响应式设计，原理是什么 怎么兼容低版本
- 一个站点能兼容多种终端（PC pad phone）
- 原理是通过 媒体查询检测设备尺寸，页面head必须有meta声明viewport

#### ::before 与 :after的区别
- 单冒号: 用于css3的伪类  双冒号:: 用于css3的伪元素
- ::before 是以一个子元素存在，定义在元素主体之前的一个伪元素，不存在于dom只存在于页面
- :before 与 :after 这两个伪元素 在css3中被规范为使用双冒号

#### 怎么让chrome中的字体小于 12px
- transform: scale(.8)

#### 定位
- 相对定位，relative 元素相对于自身content box 定位，任然占据原来的位子空间
- 绝对定位，absolute 元素相对于第一个position不为static的祖先元素的padding box定位，元素脱离文档流
- 固定定位，fixed 元素相对于浏览器窗口顶部定位，元素脱离文档流

### 问题处理

#### margin重叠怎么处理
margin重叠的几种情况

- 相邻元素`margin-bottom`与`margin-top`重叠，可以将其中一个设置为BFC
- 父子元素`margin-top`（不能设置border solid 否则不会出现）重叠可以给父元素加`border-top|padding-top`来分隔，也可以将父元素设置为 BFC
- 父元素高度`auto`父子元素`margin-bottom`重叠，在第二种处理方法上，还可以给父级设置 height、min-height、max-height

#### li与 li之间为什么有空隙

li 是行内元素，浏览器会将行内元素间的 回车|空格|制表符 渲染为空格，所以会出现无法选中的空隙

解决方式
- 写在同一行
- 左浮动，但是swiper不能使用
- margin设置为负值，最后一个元素会多出负 maigin
- 父元素font-size设置为0，但是需要将li的font-size重新设置
- 设置负的 word-spacing | letter-spacing，再将li内的 字符间距|文本间距 设置为默认

#### a标签点击后hover失效 
- hover 有四种状态， hover需要放在 visited后

#### 文本溢出 添加省略号
- 单行文本
```css
.ellipsis{
  overflow: hideen;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```
- 多行文本（设置行高与高度， 在最后面的伪元素上设置内容为 ...）

```css
p {
  position: relative;
  overflow: hidden;
  /* height/line-height 为行数 */
  height: 3em;
  line-height: 1.5rem;
}
p::after {
  content: '...';
  position: absolute;
  bottom: 0;
  right: 0;
  background-color: #fff;
}
```
