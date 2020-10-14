> 原文地址 \[juejin.im\](https://juejin.im/post/6866914148387651592)

前言
--

最近在项目中，遇到布局问题，有时候，需要堆叠很多的样式，去排版，一定程度上增加了代码量，那么有没有更加方便的布局方式呢？👇

48 张图带你从 0 到 1 掌握 flex 布局方式。

`flex布局`在某种程度上，简便我们布局的一个难题，接下来的篇幅将介绍它的使用👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cad4dc9d88834a8e831c03de2b9e624c~tplv-k3u1fbpfcp-zoom-1.image)

需要本文的 Xmind 导图的话，移步公众号 -- **前端 UpUp**, 回复`flex`关键字即可。

flex 基本概念
---------

要想熟练掌握 flex 布局的话，你需要理解两个概念：`轴`和`容器`👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/30ae5302cb1c4488af8e011bcdb9a64b~tplv-k3u1fbpfcp-zoom-1.image)

从上面图来看，我们将 flex 布局分为两部分讲，`轴`和`容器`。

**轴**

*   主轴（mian axis）
*   交叉轴（cross axis）

**容器**

*   父容器（container)
*   子容器（item）

打个预防针，`flex布局`涉及到 12 个 CSS 属性，父容器, 子容器各 6 个。

接下来先梳理常见的属性，不常见的放在进阶部分来梳理。

### 轴

我们知道，`轴`包括`主轴`和`交叉轴`, 那么它们的方向是如何决定呢？我们直接从一张图看懂它👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8563c81d90394784b32c023b725f454c~tplv-k3u1fbpfcp-zoom-1.image)

默认情况下，**主轴**的方向是从左向右的，**交叉轴**垂直于主轴，逆时针方向 90 度，那么接下来我们看 **flex-direction** 是如何决定主轴的。讲这个之前，我们需要明白👇

*   交叉轴是由主轴决定的，主轴又是由 flex-direction 决定的。

*   flex-direction 属性设置在父容器上，这样子才可以生效。

```
flex-direction: row | row-reverse | column | column-reverse
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/47eeb569967e4c2eb64c2b539f8b35ee~tplv-k3u1fbpfcp-zoom-1.image)

首先布局如下👇

```
<div>
        <div>子盒子#flex1: 1 </div>
        <div>子盒子#flex2: 1 </div>
</div>
复制代码
```

接下来，我们看看他们的效果吧👇

* * *

#### flex-direction: row

```
当你给父盒子(wrapper)设置属性
flex-direction: row
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/63c8d0fdfb00435c8c25c0eb0c4b8d8e~tplv-k3u1fbpfcp-zoom-1.image)

**结论**

*   flex 容器的主轴被定义为与文本方向相同。 主轴起点和主轴终点与内容方向相同。
*   简单理解就是**主轴沿着水平方向向右**

*   * *

#### flex-direction: row-reverse

```
当你给父盒子(wrapper)设置属性
flex-direction: row-reverse
复制代码
```

效果👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f20b904b939419aafd9060969262108~tplv-k3u1fbpfcp-zoom-1.image)

我们可以看到这两个盒子的位置发生了变化，这个就是主轴起点和主轴终点位置**置换**的原因。

**结论**

*   表现和 row 相同，但是置换了主轴起点和主轴终点。
*   简单理解就是**主轴沿着水平方向向左**，与文本方向相反。

*   * *

#### flex-direction: column

```
当你给父盒子(wrapper)设置属性
flex-direction: column
复制代码
```

效果👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/263bf603893b49da987fbfdda7e61e5d~tplv-k3u1fbpfcp-zoom-1.image)

可以看到，子盒子的布局发生了变化，形成了在 Y 轴上的布局方式, 并且书写方式跟布局一样。

**结论**

*   flex 容器的主轴和块轴相同。主轴起点与主轴终点和书写模式的前后点相同
*   简单的理解，就是主轴变成 Y 轴方向，方向从上到下布局。

*   * *

#### flex-direction: column-reverse

```
当你给父盒子(wrapper)设置属性
flex-direction: column-reverse
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76432fefc1b449fb81dc3f257788b1cd~tplv-k3u1fbpfcp-zoom-1.image)

可以看到，子盒子的布局跟 column 差不多，唯一不同的是，方向上发生了变化。

**结论**

*   表现和`column`相同，但是置换了主轴起点和主轴终点
*   简单的理解，就是主轴变成 Y 轴方向，方向从下到上，与书写的方向相反。

*   * *

### 容器

这里就分为**父容器**和**子容器**，我们先来看看父容器👇

#### 父容器

*   justify-content: **设置子元素在主轴方向上的对齐方式**
*   align-items： **设置子元素在交叉轴方向上的对齐方式**

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4b1bdef0b27d4256a91c3e72bbc83fce~tplv-k3u1fbpfcp-zoom-1.image)

* * *

#### justify-content

这个属性设置在父容器上，**决定子元素在主轴方向上的对齐方式**，我们看看它们具体表现吧👇

* * *

#### justify-content: flex-start

```
当你给父盒子(wrapper)设置属性
justify-content: flex-start
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ec9455f3adf4e82b9db1afb15d23a02~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素沿着主轴方向开始对齐。

* * *

#### justify-content: flex-end

```
当你给父盒子(wrapper)设置属性
justify-content: flex-end
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9a466a6f5a64a77ac6478ae6c804d73~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素沿着主轴方向终点对齐。

* * *

#### justify-content: center

```
当你给父盒子(wrapper)设置属性
justify-content: center
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35b82988c18c49ebbf5cbde7f1d86f60~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在主轴方向上水平居中。

* * *

#### justify-content: space-between

```
当你给父盒子(wrapper)设置属性
justify-content: space-between
复制代码
```

效果👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d75e64a2ddfb43b5b5728f65a81f34e2~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在主轴方向上**两端对齐，项目之间间隔相等**。

* * *

#### justify-content: space-around

```
当你给父盒子(wrapper)设置属性
justify-content: space-around
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c605040c45343a78b6596051d61e228~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在主轴方向上**均匀排列每个元素，每个元素周围分配相同的空间**。

* * *

#### align-items

这个属性设置在父容器上，**决定子元素在交叉轴方向上的对齐方式**，我们看看它们具体表现吧👇

* * *

#### align-items: flex-start

```
当你给父盒子(wrapper)设置属性
align-items: flex-start
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcc92ecd68c34b0fb3ea75e2ff0b912b~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在交叉轴方向上起点对齐。

* * *

#### align-items: flex-end

```
当你给父盒子(wrapper)设置属性
align-items: flex-end
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a50fe990f88341d0a85b823b9ff3fb44~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在交叉轴方向上终点对齐。

* * *

#### align-items: center

```
当你给父盒子(wrapper)设置属性
align-items: center
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00f3b616efd946a5bffa5ffda877a78b~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在交叉轴方向上居中对齐。

* * *

#### align-items: baseline

```
当你给父盒子(wrapper)设置属性
align-items: baseline
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bfe338236564e179134b7e961505951~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，子元素在交叉轴方向上以文字基线对齐，具体不清楚的，可以自行百度。

* * *

#### align-items: stretch

```
当你给父盒子(wrapper)设置属性
align-items: stretch
复制代码
```

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e641e25027ad41ec806f37756c0468d3~tplv-k3u1fbpfcp-zoom-1.image)

**结论**，这个属性是默认的，如果项目未设置高度或者设为 auto，将占满整个容器的高度。

* * *

#### 子容器

先看张图片

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d534c220aa642b7a60e67c9b8f19acd~tplv-k3u1fbpfcp-zoom-1.image)

子容器的话，这里就介绍两个属性👇

*   `flex`属性 定义在主轴是如何伸缩的
    *   子容器是有弹性的，它们会自动填充剩余空间，子容器的伸缩比由`flex`属性决定。
    *   flex 是多个属性的缩写，允许 1-3 个值的连写，具体参考上面的图。
*   `align-self`属性 **单独设置子容器如何沿交叉轴排列**
    *   每个子容器都可以单独定义沿交叉轴排列方式。
    *   该属性的取值跟父容器中的 align-items 属性一致，如果两者相同的话，则以子容器`align-self`属性为主。

#### flex 作用规则

*   三个属性的简写，是 flex-grow flex-shrink flex-basis 的简写
*   常用简化写法👇
    *   flex:1 —> flex:1 1 0%;
    *   flex:3 —> flex:3 1 0%;
    *   注意: flexbox 布局和原来的布局是两个概念，部分 css 属性在 flexbox 盒子里面不起作用，eg：float， clear， column,vertical-align 等等

```
注意👉flex-grow  flex-shrink flex-basis 这三个属性会在后续介绍
复制代码
```

具体的 flex 取值问题，可以参照下面的图👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d9db21dde9249ecbaa9204fd2397e4e~tplv-k3u1fbpfcp-zoom-1.image)

* * *

#### **align-self 作用规则**

```
// 起始端对齐
align-self : flex-start;
复制代码
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa6a46b24f2b45d3ac93f9a9fffad31e~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 末尾段对齐
align-self : flex-end;
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e986cd6721f0486aaeb54ffe2dc93591~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
基线对齐// 末尾段对齐
align-self : baseline;
复制代码
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5442ea2af1804d82a9cb9c977008ef0c~tplv-k3u1fbpfcp-zoom-1.image)

可以看到的话，它们对齐的方式是第一行文字的基线。

* * *

```
拉伸对齐
align-self : stretch;
复制代码
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc1e80a04f504faa9b066974baea3972~tplv-k3u1fbpfcp-zoom-1.image)

* * *

flex 更深入了解
----------

上面介绍的常见几个属性掌握的话，基本上可以满足日常的开发布局需求，剩下的一些属性，接下来将梳理一遍，这样子的话，早日成为`flex布局进阶者`。

### 父容器

*   **flex-wrap** 设置换行方式

    *   绝对子容器是否可以选择换行，一般而言有三种状态，支持换行的话，也支持逆序换行。
*   **flex-flow** 设置轴向与换行组合

    *   是 flex-direction 和 flex-wrap 的简写。
    *   所以只要掌握，`flex-direction` 和 `flex-wrap`即可。
*   **align-content** 多行沿交叉轴对齐方式

    *   当子容器多行排列时，设置行与行之间的对齐方式。

*   * *

#### flex-wrap

设置子容器的换行方式，通常有三个取值👇

```
flex-wrap: wrap | nowrap | wrap-reverse
复制代码
```

三种情况👇

* * *

```
// 允许换行
flex-wrap : wrap
复制代码
```

效果👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06dd94f540de40faaaa41b5fea90a989~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 不允许换行
flex-wrap : nowrap
复制代码
```

效果👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d07888feba24c06a1b9c6cec1ab364b~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 允许逆向换行
flex-wrap : wrap-reverse
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/93dcac99b7a742309d55b831f1e8ecfd~tplv-k3u1fbpfcp-zoom-1.image)

* * *

#### flex-flow

先来一张图👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bee4c1a1e2aa4939aaec1168df9df15d~tplv-k3u1fbpfcp-zoom-1.image)

`更多取值信息请查看` [`flex-direction`](https://developer.mozilla.org/zh-CN/docs/CSS/flex-direction) 和 [`flex-wrap`](https://developer.mozilla.org/zh-CN/docs/CSS/flex-wrap)

可以查看 MDN 上，或者把之前的`flex-direction` 和 `flex-wrap` 两者取值看过一遍，那么使用这个属性就没有问题啦，这里也就不过多的举例子了，取值有三种情况👇

* 单独设置 flex-direction 取值，比如

  * ```
    flex-flow: row | column
    复制代码
    ```

* 单独设置 flex-wrap 取值

  * ```
    flex-flow: wrap | nowrap | wrap-reverse
    复制代码
    ```

* 同时设置两者取值

  * ```
    flex-flow: row wrap
    flex-flow: column nowrap
    复制代码
    ```

* * *

#### align-content

这个属性是定义子容器在交叉轴的排列方式，也就是对齐方式。

首先上一张图👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e94951ca10cd4dc8bf74069ac13d6966~tplv-k3u1fbpfcp-zoom-1.image)

根据这些取值，我们来看看布局效果吧👇

* * *

```
// 起始端对齐
align-content: flex-start
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b00aaeb582d3474e838db709ec66c3a8~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 末尾段对齐
align-content: flex-end
复制代码
```

效果👇

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab3d9265288840fdb83e79f544a15d97~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 居中对齐
align-content: center
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/023e04770fc54d7a93a5473fc2dbbd1e~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 等间距均匀分布
align-content: space-between
复制代码
```

效果👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/145a1df4a0ca4a13b7320b762bf01a33~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 等边距均匀分布
align-content: space-around
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/666d82d1ba6142599a534f47c45e99ba~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 拉伸对齐
align-content: stretch
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fded204fd3e42babecb11f16eb51480~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
// 基线对齐
align-content: baseline
复制代码
```

效果👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a8fda3bbdd140cdad6ee1cd6977bf76~tplv-k3u1fbpfcp-zoom-1.image)

* * *

### 子容器

*   **flex-grow** 设置扩展比例

*   **flex-shrink** 设置收缩比例

*   **flex-basis** 设置基准大小

*   **order** 设置排列顺序

*   * *

#### flex-grow

子容器弹性伸展的比例，简单理解，就是把剩余的空间按比例分配给子容器。

我们看个例子

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a4012640173402682c321785435b058~tplv-k3u1fbpfcp-zoom-1.image)

* * *

#### flex-shrink

子容器弹性收缩的比例。简单理解，就是当你子容器超出的部分，会按照对应的比例给子容器减去对应的值。

我们来看下效果👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25a4c764286f4d41b9d7610764379c4b~tplv-k3u1fbpfcp-zoom-1.image)

当取值为 0 时，就会溢出，那么我们给它们设置一个值👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8f15ec185cdb4910a882c2fd6863aee4~tplv-k3u1fbpfcp-zoom-1.image)

这样子的超出的部分就会按照比列减去。

* * *

#### flex-basis

有几个点需要注意的是👇

* 在不伸缩的情况下，`flex-basis`给子容器设置大小才有作用。

* 当主轴为横向时，即👇

  * ```
    flex-direction：row | row-reverse
    复制代码
    ```

  * `flex-basis`设置的大小为宽度，并且会覆盖 witdh 值

* 当主轴为纵向时，即👇

  * ```
    flex-direction：column | column-reverse
    复制代码
    ```

  * `flex-basis`设置的大小为高度，并且会覆盖 height 值

我们来看看两种情况👇

```
当主轴为横向时
flex-direction：row | row-reverse
复制代码
```

效果👇

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef58a221bff243f48efdf0df099bd95b~tplv-k3u1fbpfcp-zoom-1.image)

* * *

```
当主轴为纵向时
flex-direction：column | column-reverse
复制代码
```

效果👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04b4154eee254445a2b8b32d90983d80~tplv-k3u1fbpfcp-zoom-1.image)

* * *

#### order

*   每个子容器的`order`属性默认为 0
*   通过设置`order`属性值，改变子容器的排列顺序。
*   可以是负值，数值越小的话，排的越靠前。

直接看效果图👇

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4c34d31c8fe4273b0905933fd3f3291~tplv-k3u1fbpfcp-zoom-1.image)

* * *

总结
--

这个时候，是不是需要总结一下呢👇

先看看子容器属性👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c5e529f03d345dab4534bf84b1cfd1d~tplv-k3u1fbpfcp-zoom-1.image)

* * *

再看看父容器属性👇

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2e9733b84e040f296f827ff4148f235~tplv-k3u1fbpfcp-zoom-1.image)

梳理的是常见的属性值，对于一些不常见的属性值，请[移步 MDN](https://developer.mozilla.org/zh-CN/docs/Glossary/Flex)
