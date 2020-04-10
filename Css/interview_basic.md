#### 标准盒模型与怪异盒模型
- 标准盒模型 内容宽度 不包含 （padding border）
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
- 优先级相同，选择最后一个出现的
- 继承得到的优先级最低

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
