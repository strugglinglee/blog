---
title: css基础
date: 2020-06-07 16:22:16
tags: css
categories:
- 基础巩固
- css
banner_img: https://pic4.zhimg.com/80/v2-6d2996424c3bacd7f1d0646b66a1add7_1440w.jpg
index_img: https://pic4.zhimg.com/80/v2-6d2996424c3bacd7f1d0646b66a1add7_1440w.jpg
---

#### 垂直居中的方案
- **弹性盒**
    - display:flex
    - align-items:center
    - justify-content:center
- **translate**
    - position: absolute
    - top: 50%
    - left: 50%
    - **transform: translate(-50%,-50%)**
- **相对定位**
    - position: absolute
    - top/right/bottom/left:0
    - margin:auto
- **table+vertical-align**
    - display: table-cell
    - vertical-align: middle
#### 清除浮动和BFC
- **清除浮动原理**
    - 清除浮动代码
    ```
    .clearfix:after{
        content:'';
        diaplay:block;
        clear:both
    }
    //兼容ie低版本
    .clearfix{
        *zoom:1
    }
    ```
    - 主要属性=>**clear:both  规定在左右两侧不允许浮动元素**
    - **display:block**  **clear只能在块级元素上起作用**
    - visibility:hidden/height:0  只要content的值为空，写不写都无所谓
- **BFC定义**
    - 块级格式化上下文，是一个独立的渲染区域
    - 让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。
- **触发BFC条件**
    - 根元素
    - position: absolute/fixed
    - display: inline-block / table
    - float 元素
    - ovevflow !== visible
- **BFC规则**
    - 属于同一个 BFC 的两个相邻 Box 垂直排列
    - 属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠
    - BFC 中子元素的 margin box 的左边， 与包含块 (BFC) border box的左边相接触 (子元素 absolute 除外)
    - BFC 的区域不会与 float 的元素区域重叠
    - 计算 BFC 的高度时，浮动子元素也参与计算
    - 文字层不会被浮动层覆盖，环绕于周围
- **BFC应用**
    - 阻止margin重叠
    - 可以包含浮动元素 —— 清除内部浮动 (清除浮动的原理是两个div都位于同一个 BFC 区域之中)
    - 自适应两栏布局
    - 可以阻止元素被浮动元素覆盖
    - 其子元素的float/clear/vertical属性将失效
#### css常用布局
- 盒模型包括怪异盒模型
- 定位
- 流式布局
- 浮动布局
- flex
- grid布局

#### css权重
- **CSS权重**
    - 是指样式的优先级，有两条或多条样式作用于一个元素
    - 权重高的那条样式对元素起作用
    - 权重相同的，后写的样式会覆盖前面写的样式。
- 权重的等级
    - **!important**,加在样式属性值后，权重值为**10000**
    - **内联样式**，如：style=""，权重值为1000
    - **ID选择器，如：#content，权重值为100**
    - **类，伪类和属性选择器**，如：content、:hover权重值为10
    - **标签选择器和伪元素选择器**，如：div/p/:before权重值为1
    - 通用选择器（ * ) 、子选择器（>）、相邻选择器（+）、同胞选择器（~）、权重值为0

#### px/em/rem的区别
- **px** (pixel，像素):相对长度单位。像素px是相对于显示器屏幕分辨率而言的
- **em**(相对长度单位，相对于当前对象内文本的字体尺寸):em是相对长度单位。相对于当前对象内文本的字体尺寸（参考物是父元素的font-size）如当前父元素的字体尺寸未设置，则相对于浏览器的默认字体尺寸
    - 特点: em的值并不是固定的/em会继承父级元素的字体大小
- **rem**（root em，根em）
    - vue中使用postcss-px2rem
    - 自动将vue项目中的px转换为rem
    - 因为 postcss-plugin-px2rem 这个插件  配置选项上有  exclude 属性，它可以配置 是否对 某个文件夹下的所有css文件不进行从px到rem的转换。
    - rem是CSS3新增的一个相对单位，**rem是相对于HTML根元素的字体大小**（font-size）来计算的长度单位
    - 优点：只需要设置**根目录的大小**就可以把整个页面的成比例的调好
    - rem兼容性：除了IE8及更早版本外，所有浏览器均已支持rem
- em与rem的区别：
    - rem是相对于根元素（html）的字体大小，而em是相对于其父元素的字体大小
    - 如何使 1rem=10px？在设置HTML{font-size：62.5%；}即可
- 补充内容：
    - vw是相对视口（viewport）的宽度而定的，长度等于视口宽度的1/100
    - vh是相对视口（viewport）的高度而定的，长度等于视口高度的1/100
    - vm其中最小的那个被均分为100单位的vm

#### 网站变灰--滤镜
- filter:grayscale(100%)
#### 文本溢出
- **单行文本溢出省略**
    - overflow:hidden(文字超出限定宽度，则隐藏超出的内容)
    - white-space:nowrap(设置文字在一行显示，不能换行)
    - text-overflow:ellipsis(规定当文本溢出时，显示省略符号来代表被修剪得文本)
- **多行文本溢出省略**(只适用于webkit内核)
    - -webkit-line-clamp:2(用来限制在一个块元素现实的文本行数)
    - display:-webkit-box(和1结合使用，将对象作为弹性伸缩盒子显示)
    - -webkit-box-orient:vertical(和1结合使用，设置或检索伸缩对象的子元素的排列方式)
    - overflow:hidden
    - text-overflow:ellipsis

#### 伪类/伪元素
- **伪元素**
    - ::fist-line为某个元素的第一行文字添加使用样式
    - ::first-letter为某个元素的文字的首字母或第一个字添加使用样式
    - ::before在某个元素之前插入一些内容
    - ::after在某个元素之后插入一些内容
    - ::selection对光标选中的元素添加样式
- 伪类
    - :link
        - 选取未访问过的超链接元素，只要点过的就会变色，从而标记哪个链接是访问过的,这个属性就是用来表示没访问过的链接
    - :visited
        - 与前者相反，用来标记哪个链接是已经访问过的，防止重复点击
    - :hover
        - 选取鼠标悬停的元素，经常用在PC端，当鼠标放在一个元素上面时的样式,移动端基本用不到
    - :active
        - 选取点中的元素，希望按钮有反馈的时候 可以用它来标记操作反馈的样式
        - 并不只可以用在按钮上
    - :focus
    - :empty
        - 选取没有子元素的元素，比如选择空的span
        - 要注意，就算有空格的化也不能算空，不会被这个伪类选中
    - :checked
        - 选取勾选状态的input元素，只对radio/checkbox生效
    - :disabled
        - 选取禁用的表单元素
    - :first/last/nth-child/:nth-last-child(an+b)
    - :only-child(选取**唯一**子元素)
    - :not
        - 该伪类本身没有优先级
        - 优先级由括号里面的内容决定
#### 重绘和回流
- **重绘**:指当前页面的元素不脱离文档流，简单的进行样式的变化，如修改颜色背景，浏览器绘制样式等
- **回流**:指处于文档流中DOM的尺寸大小，位置或者某些属性发生变化时，导致浏览器重新渲染部分或者全部文档
- 回流比重绘消耗性能开支更大
#### html5新特性
- 用于绘画的**canvas**元素
- 用于媒介回放的 **video** 和**audio**元素
- **svg**,地理定位
- 对本地离线存储的更好的支持**localStorage**(5M的存储空间>>cookies的4K)
- 语义化标签 article、footer、header、nav、section
- **WebSocket**长链接
#### css3
- **伪元素**
    - ::first-line（首行）
    - ::first-letter （首字）
    - ::before 在某个元素之前插入一些内容
    - ::after 在某个元素之后插入一些内容
- **盒模型**（box-sizing: border-box怪异盒模型的运用）
- **弹性盒子**
    - 定义
        - 一种当页面需要适应不同的屏幕大小以及设备类型时确保元素拥有恰当的行为的布局方式
        - 弹性盒子由**弹性容器**(Flex container)和**弹性子元素**(Flex item)组成。
        - 弹性容器通过设置 display 属性的值为 flex 或 inline-flex将其定义为弹性容器。
    - **flex-direction**  顺序指定了弹性子元素在父容器中的位置。
        - row：横向从左到右排列（左对齐），默认的排列方式。
        - row-reverse：反转横向排列（右对齐，从后往前排，最后一项排在最前面
        - column：纵向排列。
        - column-reverse：反转纵向排列，从后往前排，最后一项排在最上面。
    - **justify-content** 属性(控制主轴)
        - justify-content: flex-start | flex-end | center | space-between | space-around
        - flex-start 弹性项目向行头紧挨着填充。默认值
        - flex-end 弹性项目向行尾紧挨着填充。
        - center 弹性项目居中紧挨着填充。
        - space-between 弹性项目平均分布在该行上，两端对齐。
        - space-around 弹性项目平均分布在该行上，两边留有一半的间隔空间。
    - **align-items** 设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式
        - flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
        - flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
        - center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
        - baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
        - stretch：默认值，自动将子元素拉伸成容器的高度
    - **flex-wrap**属性用于指定弹性盒子的子元素换行方式
        - nowrap- 默认，弹性容器为单行。该情况下弹性子项可能会溢出容器。
        - wrap- 弹性容器为多行。该情况下弹性子项溢出的部分会被放置到新行，子项内部会发生断行
        - wrap-reverse-反转 wrap 排列。
    - **align-content** 属性用于修改flex-wrap属性的行为。类似于align-items, 但它不是设置弹性子元素的对齐，而是设置各个行的对齐。
        - stretch- 默认。各行将会伸展以占用剩余的空间。
        - flex-start- 各行向弹性盒容器的起始位置堆叠。
        - flex-end- 各行向弹性盒容器的结束位置堆叠。
        - center-各行向弹性盒容器的中间位置堆叠。
        - space-between-各行在弹性盒容器中平均分布。
        - space-around- 各行在弹性盒容器中平均分布，两端保留子元素与子元素之间间距大小的一半。
    - **align-self**属性用于设置弹性元素自身在侧轴（纵轴）方向上的对齐方式
        - auto：如果'align-self'的值为'auto'，则其计算值为元素的父元素的'align-items'值，如果其没有父元素，则计算值为'stretch'。
        - flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
        - flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
        - center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。
        - baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
        - stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。
    - **flex** 属性用于指定弹性子元素如何分配空间
    - **flex-basis** 用来设置子元素主轴方向的尺寸，eg:flex-basis:200px;
- 背景和边框
    - **border-radius**
    - **box-shadow**
    - border-image
    - background-size
    - background-origin
    - content-box
    - padding-box
    - border-box
    - 多重背景background-image:url(bg_flower.gif),url(bg_flower_2.gif);
    - 文本效果text-shadow（能够规定水平阴影、垂直阴影、模糊距离，以及阴影的颜色）text-shadow: 5px 5px 5px #FF0000;
    - **word-wrap**允许您允许文本强制文本进行换行 即使这意味着会对单词进行拆分
- 字体
    - 将该字体文件存放到 web 服务器上，它会在需要时被自动下载到用户的计算机上（之前是需要下载到本地的）
    - @font-face 规则中定义
        - 首先定义字体的名称（比如 myFirstFont）然后指向该字体文件
- **2D/3D 转换**
    - CSS3 转换，我们能够对元素进行移动、缩放、转动、拉长或拉伸
    - 2D转换
        - **translate()**--translateX/Y
            - transform: translate(50px,100px);把元素从左侧移动 50 像素，从顶端移动 100 像素
        - **rotate()**:transform: rotate(30deg);元素顺时针旋转给定的角度。允许负值，元素将逆时针旋转
        - **scale()**--scaleX/Y transform: scale(2,4)把宽度转换为原始尺寸的 2 倍，把高度转换为原始高度的 4 倍。
        - **skew()**--skewX/Y元素翻转给定的角度，根据给定的水平线（X 轴）和垂直线（Y 轴）参数
            - transform: skew(30deg,20deg);围绕 X 轴把元素翻转 30 度，围绕 Y 轴翻转 20 度
        - **matrix()**
            - 把所有 2D 转换方法组合在一起
            - matrix() 方法需要六个参数，包含数学函数，允许您：旋转、缩放、移动以及倾斜元素
    - **3D转换**
        - translateX(x) /translateY(y)/translateZ(z)
        - scale3d(x,y,z)/scaleX(x)/scaleY(y)/scaleZ(z)
        - rotate3d(x,y,z,angle) /rotateX(angle)/rotateY(angle)/rotateZ(angle)
- **transition 过渡效果**
    - transition: property duration timing-function delay;
    - transition: 过渡动画
    - transition-property: 属性
    - transition-duration: 间隔
    - transition-timing-function: 曲线
    - transition-delay: 延迟
- **动画**
    -  @keyframes 创建动画规则（from-to/0%~100%）
        -  @keyframes myfirst{from {background: red;}to {background: yellow;}}
    -  animation: **myfirst 5s linear 2s infinite alternate**;
    - animation-timing-function：规定动画的速度曲线。默认是ease
    - animation-delay：规定动画何时开始。默认是 0
    - animation-iteration-count：规定动画被播放的次数。默认是 1
    - animation-direction：规定动画是否在下一周期逆向地播放。默认是normal
    - animation-play-state ：规定动画是否正在运行或暂停。默认是running
    - animation-fill-mode：规定动画执行之前和之后如何给动画的目标应用，默认是none，保留在最后一帧可以用forwards