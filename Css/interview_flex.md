## flex (容器与轴)
- flex 是`flex-grow` `flex-shrink` `flex-basis`的缩写

#### flex-grow  （自身放大）
- 当父控件还有剩余空间时,是否进行放大，其中数值代表放大比例，值为0时代表不放大
- flex-grow 定义弹性盒子项（item）的拉伸因子
- 负值无效

#### flex-shrink （缩小比例）
- 当父控件空间不足时，是否进行缩小，其中数值表示与控件大小有关的收缩比
- 设置或检索弹性盒子的收缩比率
- 不允许负值

#### flex-basis
- 当子控件含有该属性时，表示子控件占主轴的大小
- 指定元素在主轴方向上的初始大小，若不使用`box-sizing` 改变盒模型，则该属性就决定了flex元素的
内容盒（content-box）的宽或高（取决于主轴方向）
- 取值可以是数字+单位、 百分比

##### PS: 设置为Flex元素后，子元素的`float`、`clear`、`vertical-align` 都将失效


### 基本概念
- flex容器默认存在两根轴，水平主轴与交叉轴

#### 容器属性
- flex-direction    (决定主轴方向)
- flex-wrap         (轴线上排不下如何换行)
- flex-flow         (flex-direction 与 flex-wrap的简写形式)
- justify-content   (主轴上的对齐方式)
- align-item        (交叉轴上的对齐方式)
- align-content     (定义多根轴线的对齐方式，若只有一根主轴则不起作用)


#### 子项属性
- order             (排列顺序，数值越小越靠前)
- flex-grow         (定义项目放大比例，默认为0不放大)
- flex-shrink       (定义项目的缩小比例 默认为1，空间不足将缩小。值为0表示不缩小)
- flex-basis        (分配多余空间前，项目占据的主轴空间，默认为 auto 本来的大小)
- flex              (flex-grow\flex-shrink\flex-basis的简写 默认值为 0 1 auto)
- flex-self         (允许单个项目与其他项目对齐方式不一样 默认为 auto)
- [example](./example/flex.html)
