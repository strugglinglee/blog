---
title: 关于Flex布局的深度学习
date: 2021-07-21 23:25:27
tags:
- css
- Flex
categories:
- 基础巩固
- css
---


## 什么是Flex布局

Flex 是 `Flexible Box` 的缩写，意为`"弹性布局"`，用来为盒状模型提供最大的灵活性。换句话说，你想要的任何布局灵活的flex基本都能帮你实现。

> 任何一个父元素都可以指定为 Flex 布局。

采用 Flex 布局的元素，称为 Flex 父元素（flex container），简称"父元素"。
它的所有子元素自动成为父元素成员，称为 Flex 子元素（flex item），简称"子元素"。
![](/img/flex/0.png)

- 父元素默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。
- 子元素默认沿主轴排列。单个子元素占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

> 这里的“父元素(container)”很多地方会直译为“容器”，个人认为叫“父元素”更通俗易懂，本文统称“父元素”，同理，其他地方“子元素（item）”会被直译为“项目”，本文统称“子元素”。

### 重点

与“主轴”垂直的轴称为“交叉轴”，通过对flex-direction的设定，主轴和交叉轴可以改变。

## 父元素的属性(Flex-container)

以下6个属性设置在父元素上。

### 一、flex-direction 方向

```
flex-direction: row(default) | row-reverse | column | column-reverse
```
决定主轴的方向（即子元素的排列方向）
- row（默认值）：主轴为水平方向，起点在左端。
- row-reverse：主轴为水平方向，起点在右端。
- column：主轴为垂直方向，起点在上沿。
- column-reverse：主轴为垂直方向，起点在下沿。
![](/img/flex/1.png)

### 二、flex-wrap 换行

默认情况下，子元素都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。

```
flex-wrap: nowrap(default) | wrap | wrap-reverse
```

- nowrap（默认）：不换行
- wrap：换行，第一行在上方
- wrap-reverse：换行，第一行在下方

![](/img/flex/2.png)

### 三、flex-flow 流向
flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
> 简单理解 方向+换行一起决定了流向

```
flex-flow: <flex-direction> || <flex-wrap>;
```

### 四、justify-content 主轴对齐方式

属性定义了子元素在主轴上的对齐方式

```
justify-content: flex-start | flex-end | center | space-between | space-around;
```
- flex-start（默认值）：左对齐
- flex-end：右对齐
- center： 居中
- space-between：两端对齐，子元素之间的间隔都相等。
- space-around：每个子元素两侧的间隔相等。所以，子元素之间的间隔比子元素与边框的间隔大一倍。

### 五、align-items 交叉轴对齐方式

属性定义子元素在交叉轴上如何对齐

```
align-items: flex-start | flex-end | center | baseline | stretch(default)
```

- flex-start：交叉轴的起点对齐。
- flex-end：交叉轴的终点对齐。
- center：交叉轴的中点对齐。
- baseline: 子元素的第一行文字的基线对齐。
- stretch（默认值）：如果子元素未设置高度或设为auto，将占满整个父元素的高度。

### 六、align-content 多行交叉轴对齐方式

align-content属性定义了多根轴线的对齐方式。如果子元素只有一根轴线，该属性不起作用。

```
align-content: flex-start | flex-end | center | space-between | space-around | stretch(default);
```

- flex-start：与交叉轴的起点对齐。
- flex-end：与交叉轴的终点对齐。
- center：与交叉轴的中点对齐。
- space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
- space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
- stretch（默认值）：轴线占满整个交叉轴。

### align-content vs align-items

#### 相同点：

都被用来设置对齐行为
#### 不同点：

align-items 的设置对象是行内成员;
![](/img/flex/3.png)
align-content 的设置对象是所有行，且只有在多行弹性盒子容器中才生效。
![](/img/flex/4.png)

## 子元素的属性

以下6个属性设置在子元素上

### 一、order 顺序

```
order: <integer>;
```

属性定义子元素的排列顺序。数值越小，排列越靠前，默认为0。

### 二、flex-grow 放大

grow译翻译过来是生长，所以这个属性一定是和子元素的放大有关的

```
.item {
  flex-grow: <number>; /* default 0 */
}
```

#### 父元素空间充足，子元素默认不变

flex-grow属性定义子元素的放大比例，默认为`0`，即如果存在剩余空间，也不放大。
**简单理解：子元素flex-grow的值越大所占比例越大**

#### 使用场景
子元素默认是不会放大的，所以使用的场景是父元素空间充足时，子元素需要放大/子元素放大比例不一

> 如果所有子元素的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个子元素的flex-grow属性为2，其他子元素都为1，则前者占据的剩余空间将比其他项多一倍。


### 三、flex-shrink 缩小
shrink翻译过来是缩小，故这个属性一定是和子元素缩小有关的
#### 父元素空间不足，子元素默认等比缩小
flex-shrink属性定义了子元素的缩小比例，默认为1，即如果空间不足，该子元素将缩小。

**简单理解：和flex-grow刚好相反，子元素flex-shrink的值越大所占比例越小**

```
.item {
  flex-shrink: <number>; /* default 1 */
}
```

#### 使用场景
默认是会等比缩小的，所以使用的场景是空间不足时，子元素缩小比例不同/不需要缩小


> 如果所有子元素的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个子元素的flex-shrink属性为0，其他子元素都为1，则空间不足时，前者不缩小。
> 负值对该属性无效。


### 四、flex-basis
flex-basis属性定义了在分配多余空间之前，子元素占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即子元素的本来大小。

```
.item {
  flex-basis: <length> | auto; /* default auto */
}
```
它可以设为跟width或height属性一样的值（比如350px），则子元素将占据固定空间。


### 五、flex

flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。

```
//缩写记忆顺序为gsb
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

####  该属性的快捷值

- auto (1 1 auto)
- none (0 0 auto)
- 1 (1 1 0%)
- 0 (0 1 0%)

#### flex:auto和flex:1的区别

实际上就是flex-basis为0%和auto的区别,auto为默认值即为子元素的大小

- flex-basis为0%，可以覆盖width
- flex-basis为auto，width权限更高

> flex:auto=>flex-basis:auto 表示flex-basis:$width 是相当于设置了Flex-basis为宽度的
> ![](/img/flex/5.png)
> flex:1=>flex-basis:0 相当于未设置flex-basis
> ![](/img/flex/6.png)

##### 结论：
存在多余空间时，flex为1，width将会被忽略；flex为auto时，width的设置将是有效的

### 六、align-self

align-self属性允许单个子元素有与其他子元素不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。

#### 使用场景

单个子元素有与其他子元素有不一样的对齐方式

```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

> ![](/img/flex/7.png)

## 附：属性查询表（自制）

### 父元素

![](/img/flex/table1.png)

### 子元素

![](/img/flex/table2.png)