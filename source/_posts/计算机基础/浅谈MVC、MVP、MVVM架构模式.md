---
title: 浅谈MVC、MVP、MVVM架构模式
date: 2020-06-07 16:46:16
tags: 计算机基础
categories:
- 计算机基础
---

> 参考阮一峰网络日志，廖雪峰官方网站，以及部分网络资料总结

## mvc(model-view-controller)
### 概念解释

MVC是三个单词的首字母缩写，它们是Model（模型）、View（视图）和Controller（控制）

- 最上面的一层，是直接面向最终用户的"视图层"（View）。它是提供给用户的操作界面，是程序的外壳。
- 最底下的一层，是**核心的"数据层"（Model）**，也就是程序需要操作的数据或信息。
- 中间的一层，就是"控制层"（Controller），它负责根据用户从"视图层"输入的指令，选取"数据层"中的数据，然后对其进行相应的操作，产生最终结果。

![](https://user-gold-cdn.xitu.io/2020/4/5/1714950836ab2b65?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 简单理解
用户操作->View（负责接收用户的输入操作）->Controller（业务逻辑处理）->Model（数据持久化）->View（将结果反馈给View）

### 联系与作用
这三层是紧密联系在一起的，但又是互相独立的，每一层内部的变化不影响其他层。每一层都对外提供接口（Interface），供上面一层调用。这样一来，软件就可以实现模块化，修改外观或者变更数据都不用修改其他层，大大方便了维护和升级。

> JavaEE中的SSH框架（Struts/Spring/Hibernate），Struts（View, STL）-Spring（Controller, Ioc、Spring MVC）-Hibernate（Model, ORM）以及ASP.NET中的ASP.NET MVC框架，xxx.cshtml-xxxcontroller-xxxmodel。

##  mvp（model-view-presenter）
### 概念
MVP是把MVC中的Controller换成了Presenter（呈现），目的就是为了完全切断View跟Model之间的联系，**由Presenter充当桥梁**，做到View-Model之间通信的完全隔离。

![](https://user-gold-cdn.xitu.io/2020/4/5/171495f433d4ed1f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> .NET程序员熟知的ASP.NET webform、winform基于事件驱动的开发技术就是使用的MVP模式。控件组成的页面充当View，实体数据库操作充当Model，而View和Model之间的控件数据绑定操作则属于Presenter。控件事件的处理可以通过自定义的IView接口实现，而View和IView都将对Presenter负责。

## mvvm(model-view-viewModel)
如果说MVP是对MVC的进一步改进，那么MVVM则是思想的完全变革。它是将“**数据模型数据双向绑定**”的思想作为核心，因此在View和Model之间没有联系，**通过ViewModel进行交互，而且Model和ViewModel之间的交互是双向的**，因此视图的数据的变化会同时修改数据源，而数据源数据的变化也会立即反应到View上。

![](https://user-gold-cdn.xitu.io/2020/4/5/17149616b6cb9aef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> MVVM最早由微软提出来，它借鉴了桌面应用程序的MVC思想，在前端页面中，把Model用纯JavaScript对象表示，View负责显示，两者做到了最大限度的分离。

### 核心
把Model和View关联起来的就是ViewModel。ViewModel负责把Model的数据同步到View显示出来，还负责把View的修改同步回Model。

### MVVM的设计思想
关注Model的变化，让MVVM框架去自动更新DOM的状态，从而把开发者从操作DOM的繁琐步骤中解脱出来！

> 这里以前端框架VUE举例说明MVVM，当然还有许多有名的框架都用的是MVVM模式；MVVM的好处就是数据驱动，数据变，则页面变，这样就能用简单的代码，实现比较复杂的逻辑操作；因此MVVM框架比较适合逻辑复杂的前端项目，比如一些管理系统等。

### 结论
从mvc一直发展到mvvm，使view/model能够完全分离，是一个解耦的过程，使每个部分都能更专注于自身的作用，代码逻辑更清晰，也使开发者的工作更轻松。

