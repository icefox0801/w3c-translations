# CSS弹性盒布局模型（Level 1）
W3C候选标准，2016年5月26日

## 摘要
这篇规范介绍了一种用于用户界面设计，优化后的CSS盒模型。在flex布局模型中，flex容器的子元素可以在任意方向进行布局，并且可以『伸缩』他们的尺寸，要么通过伸展来填补未利用的空间，要么通过收缩来避免溢出他们的父元素。  

[CSS][0-1]是一门用于描述在屏幕、在纸质、在语音等媒介中，结构化文档（比如HTML和XML）渲染的语言。 

## 这篇文档的状态
*本章节描述了该文档在出版时的状态，其他文档可能会取代该文档。在[http://www.w3.org/TR/的技术报告目录](http://www.w3.org/TR/)中你可以找到当前W3C的一系列出版物和这篇技术报告的最新版。*

这篇文档是作为候选规范由[CSS Working Group](http://www.w3.org/Style/CSS/members)（作为[Style Activity](http://www.w3.org/Style/)的一部分）产生。这篇文档旨在成为W3C规范。至少到2016年9月1日，该文档都仍然会是一篇候选推荐标准，以确保被广泛审阅。

## 目录
+ 1 简介
  + 1.1 概述
  + 1.2 模块交互
  
+ 2 flex布局盒模型以及术语

+ 3 flex容器：'flex'和'inline-flex' 'display'属性值

+ 4 flex项
  + 4.1 绝对定位flex子元素
  + 4.2 flex项的margin和padding
  + 4.3 flex项的z-ordering
  + 4.4 折叠项
  + 4.5 flex项默认最小尺寸

+ 5 排列和定位
  + 5.1 flex流方向：'flex-direction'属性
  + 5.2 flex折行：'flex-wrap'属性
  + 5.3 flex方向与折行：'flex-flow'缩写
  + 5.4 显示顺序：'order'属性
    + 5.4.1 重排和可访问性

+ 6 Flex行线

+ 7 伸缩性
  + 7.1 flex缩写
    + 7.1.1 flex基本值
  + 7.2 flex组成
    + 7.2.1 'flex-grow'属性
    + 7.2.2 'flex-shrink'属性
    + 7.2.3 'flex-basis'属性

+ 8 对齐
  + 8.1 带auto值margin的对齐
  + 8.2 轴线对齐：'justify-content'属性
  + 8.3 跨轴对齐：'align-items'和'align-self'属性
  + 8.4 填空Flex行：'align-content'属性
  + 8.5 flex容器基线

+ 9 flex布局算法
  + 9.1 初始化设置
  + 9.2 确定行的长度
  + 9.3 确定主要尺寸
  + 9.4 决定纵向尺寸
  + 9.5 主轴对齐
  + 9.6 纵轴对齐
  + 9.7 解决弹性长度
  + 9.8 确定和不定尺寸
  + 9.9 固有尺寸
    + 9.9.1 flex容器固有主要尺寸
    + 9.9.2 flex容器固有纵向尺寸
    + 9.9.3 flex项固有尺寸分配

+ 10 分割flex布局
  + 10.1 flex分割算法示例

+ 附录A：轴线映射

+ 11 隐私和安全考虑
。。。

## 1. 简介
*本章节还不是正式标准。*

CSS2.1定义了四种布局模式—基于盒子与其兄弟、祖先元素的关系来确定尺寸和位置的算法：
+ block布局，用来布局文档
+ inline布局，用来布局文本
+ table布局，用来以表格格式布局2D数据
+ 定位布局，用来明确地定位而不考虑文档中的其他元素

该模块介绍了一种新的布局模式，***flex布局***，用来布局更加复杂的应用和页面。

### 1.1 概述
*本章节还不是正式标准。*

flex布局表面上和block布局是相似的。它没有很多以文本或者文档为中心的用于block布局的复杂属性，譬如[浮动][1-1]和[多列][1-2]。作为回报，它增加了简单而不失强大的工具，用于web应用和复杂web页面常用的空间分配和内容对齐。flex容器中的内容：
+ 可以以任意[流向][1-3]（向左、向右、向下甚至向上！）来布局
+ 可以使它们的显示顺序在样式层上['相反'][1-4]或者[重新排列][1-5]（即显示的顺序独立于源码和语音顺序）
+ 可以沿着一条（[主][1-6]）轴直线布局或沿着第二条（[侧][1-7]）轴折成多行
+ 可以["伸缩"它们的尺寸][1-8]来响应可用空间
+ 可以在第二条（[侧][1-7]）轴上相对于它们的容器[对齐][1-9]或者相互对齐
+ 可以沿着[主轴][1-6]被动态[收起][1-10]或展开，同时保留容器的[侧轴尺寸][1-11]

> **例1**  
> 这里是一个目录的示例，每一项都有标题、图片、描述和购买按钮。设计者想要每一项尺寸相同，图片位于文字上方，并且购买按钮不考虑描述长度，在底部对齐。flex布局使这个设计的许多因素变得容易：
> + 目录使用flex布局来使目录项水平布局，并且确保一排中的每一项是等高的。同时每一项本身又是flex列容器，使它的内容垂直布局
> + 在每一项内，原文档内容是按照逻辑顺序排列的：首先是标题，接下来是描述和图片。在无CSS的浏览器中为语音提供了合理的顺序。然而为了更引人注目的视觉呈现，用['order'][1-12]将图片从后面的内容拖到顶部，用['align-self'][1-13]使它水平居中。
> + [margin的'auto'值][1-14]强制购买按钮忽略每一项描述的高度，位于每个目录项底部。

```css
#deals {
  display: flex;        /* flex布局来使目录项等高 */
  flex-flow: row wrap;  /* 允许目录项折成多行 */
}
.sale-item {
  display: flex;        /* 使用flex布局来布局目录项 */
  flex-flow: column;    /* 垂直布局目录项的内容  */
}
.sale-item > img {
  order: -1;            /* 把图片移到内容前面 (按照显示的顺序) */
  align-self: center;   /* 横向居中图片（水平） */
}
.sale-item > button {
  margin-top: auto;     /* margin-top的auto值将按钮推到底部 */
}
```
```html
<section id="deals">
  <section class="sale-item">
    <h1>Computer Starter Kit</h1>
    <p>This is the best computer money can buy, if you don’t have much money.
    <ul>
      <li>Computer
      <li>Monitor
      <li>Keyboard
      <li>Mouse
    </ul>
    <img src="images/computer.jpg"
         alt="You get: a white computer with matching peripherals.">
    <button>BUY NOW</button>
  </section>
  <section class="sale-item">
    …
  </section>
  …
</section>
```

### 1.2 模块交互
该模块扩展了['display'][1-15]属性的定义[[CSS21]][1-16]，添加了一个新的块级和行内级的display类型，并且定义了一种新的带有布局控制属性的格式上下文。该模块中定义的属性不能应用在['::first-line'][1-17]或者['::first-letter'][1-18]伪元素上。

## 2. flex布局盒模型以及术语
*flex容器*是由一个计算得到['flex'][2-1]或者['inline-flex'][2-2]的['display'][1-15]属性的元素生成的盒子。flex容器的流中子元素被称作*flex项*并且使用flex布局模型来布局。

不像block和inline布局，他们的布局计算方式基于[block和inline流方向][2-3]来位移的，flex布局是基于*flex方向*进行偏移的。为了更简单地阐述flex布局，本章节定义了一系列flex flow相关的术语。['flex-flow'][2-4]的值和['writing mode'][2-5]确定这些术语如何与物理方向（top/right/bottom/left），坐标轴（vertical/horizontal），和尺寸（width/height）相对应。

主轴  
主轴维度  
&emsp;&emsp;flex容器的*主轴*是一条[flex项][2-6]沿着它布局的主要的轴。主轴在*主轴维度*上扩展。

主轴起点  
主轴终点  
&emsp;&emsp;[flex项][2-6]在容器内从*主轴起点*侧向*主轴终点*侧摆放。

主轴尺寸  
主轴尺寸属性  
&emsp;&emsp;[flex项][2-6]任何一个在[主轴维度][2-7]中的宽度或高度，就是该项的*主轴尺寸*。[flex项][2-6]任何一个在[主轴维度][1-25]中的['width'][2-8]或['height'][2-9]属性，就是该项*主轴尺寸属性*。

侧轴  
侧轴维度  
&emsp;&emsp;垂直于[主轴][1-6]的轴被称之为侧轴。侧轴在*侧轴维度*上扩展。

侧轴起点  
侧轴终点  
&emsp;&emsp;[flex行][2-10]被flex项填充并且在容器内从*侧轴起点*侧向*侧轴终点*侧摆放。

侧轴尺寸  
侧轴尺寸属性  
&emsp;&emsp;[flex项][2-8]任何一个在[侧轴维度][2-12]中的宽度或高度，就是该项的*侧轴尺寸*。[flex项][2-8]任何一个在[侧轴维度][2-11]中的['width'][2-8]或['height'][2-9]属性，就是该项*侧轴尺寸属性*。

这这篇规范中使用的其他尺寸术语被定义在[CSS内在和外在尺寸][2-12]中。[[CSS3-SIZING]][2-13]

## 3. flex容器：['flex'][2-1]和['inline-flex'][2-2]的['display'][1-15]属性值
|key|value|
|:--|:--|
|Name:|['display'][1-15]|
|New values:|flex[ \| ][3-1]inline-flex|
+ *'flex'*  
该值会使一个元素生成一个块级[flex容器][3-2]。  
+ *'inline-flex'*  
该值会使一个元素生成一个行内[flex容器][3-2]。  

[flex容器][3-2]会为它的内容建立一个新的*弹性格式上下文（flex formatting context）*。这和建立一个块级格式上下文（block formatting context）是相同的，除了用flex布局替代block布局。例如，浮动不会干涉flex容器，并且flex容器的外边距不会它的内容的外边距进行合并。[flex容器][3-2]为它的内容形成了一个包含块，[正像块容器那样][3-3]。[CSS21][1-16]  ['overflow'][3-4]属性会应用在[flex容器][3-2]上。

flex容器并不是block容器，所以一些以block布局为假设的前提下进行设计的属性不能用于flex布局上下文。特别是：
+ 多列布局模型[[CSS3COL]][3-5]中的'column-*'不会影响flex容器。
+ ['float'][3-6]和['clear'][3-7]不会为[flex项][2-6]创建浮动和清除，也不会使它脱离flex流。
+ ['vertical-align'][3-8]不会影响flex项。
+ ['::first-line'][1-17]和['::first-letter'][1-18]伪元素对[flex容器][3-2]不起作用，并且[flex容器][3-2]不会为它的祖先元素生成[首格式行][1-17]或首字母

如果一个元素指定了['display'][1-15]为['inline-flex'][2-2]，那么它的['display'][1-15]属性在某些情况下会计算成为['flex'][2-1]：[CSS 2.1 9.7章节][3-9]中的表格增加了一行修改，"Specified Value"列中的['inline-flex'][2-2]和在"Computed Value"列中['flex'][2-1]。

## 4 flex项
通常来讲，[flex容器][3-2]中的[flex项][2-6]是表现它在flex流中内容的盒子。

[flex容器][3-2]中每一个在flex流中的的子元素会变成[flex项][2-6]，并且直接包含在[flex容器][3-2]的每一段连续的文本会被匿名的[flex项][2-6]包裹。然而，只包含[空格][4-1]（就是可以被['white-space'][4-2]属性影响的字符）的匿名flex项不会被渲染（就像'display:none'一样）。

> **例2**  
> flex项示例：

```html
<div style="display:flex">

    <!-- flex项：块级子元素 -->
    <div id="item1">block</div>

    <!-- flex项：浮动元素；浮动会被忽略 -->
    <div id="item2" style="float: left;">float</div>

    <!-- flex项：在行内内容周围的匿名块 -->
    anonymous item 3

    <!-- flex项：行内子元素 -->
    <span>
        item 4
        <!-- flex项不会在块元素周围被分割 -->
        <q style="display: block" id=not-an-item>item 4</q>
        item 4
    </span>
</div>
```
> 注意里面元素的空格消失了：即使里面元素的文本*确实*在一个匿名的flex项中折行了，它也确实没有成为它自己的flex项。
>
> 同时注意匿名项的盒子是无法用添加样式的，因为没有元素去分配样式规则。然后它的内容仍然会从flex容器继承样式（比如字体设置）。

[flex项][2-6]为它的内容建立新的格式上下文。格式上下文的类型通常由['display'][1-15]属性值决定。然而，flex项本身是*flex级*的盒子，并非块级的盒子：它们参与到它们容器的flex格式上下文中，而不是块状格式上下文。

---

> 注意：阅读这篇规范的作者可能会想要[跳过下面的盒子生成和静态定位说明][4-3]。

[flex项][2-6]的['display'][1-15]属性值被块状化：如果指定生成[flex容器][3-2]的流中的子元素的['display'][1-15]为行内级的值，它会被计算成等价于块级的值。（关于这一类的['display'][1-15]属性值转换的更多细节，请参考[CSS2.1§9.7][3-9] [[CSS21]][1-16]和[CSS Display][4-4] [[CSS3-DISPLAY]][4-5]）

> 注意：['display'][1-15]的某些值通常下会引发匿名盒子在原盒子外面被创建。如果这样的盒子是[flex项][2-6]，它首先会被块状化，那么不会发生匿名盒子的创建。例如，两个相邻的['display: table-cell'][1-15]的[flex项][2-6]会变成两个独立的['display: block'][1-15]的[flex项][2-6]，而不是被一个单独的匿名table所包裹。

### 4.1 绝对定位flex子元素
因为脱离了flex流，一个被绝对定位的[flex容器][3-2]的子元素不参与flex布局。

绝对定位的[flex容器][3-2]的子元素的[静态位置][4-6]会这样确定，导致该子元素就好像是[flex容器][3-2]中的仅有的[flex项][2-6]一样，假设子元素和flex容器都是以它们使用的尺寸为固定尺寸的盒子的话。出于这个目的，['align-self: auto'][1-13]的值会被等同于'start'来对待。

> 换句话说，flex容器绝对定位子元素的[静态位置矩形][4-7]就是[flex容器][3-2]的内容盒子，在这里*静态位置矩形*就是用来确定静态位置位移和绝对位置位移的[对齐容器][4-8]。
> 
> （在block布局中静态位置矩形和[CSS2.1§10.3.7][4-9]中描述的"假想盒子"一致，因为它没有对齐属性，CSS2.1总会用[block-start][4-10] [inline-start][4-11]来对齐[静态位置矩形][4-7]中的绝对定位盒子。注意该定义最终会移到CSS定位模块中。）

> **例3**  
> 设置了绝对定位的影响是，譬如，在flex容器的绝对定位子元素设置了['align-content: center;'][4-12]，子元素上自动的偏移会使它在[flex容器][3-2]的[侧轴][4-13]上居中。
> 
> 然而，因为绝对定位子盒子被认为是"固定尺寸"的，['stretch'][4-14]的值会被对待为['flex-start'][4-15]。

### 4.2 flex项的margin和padding
[flex项][2-6]的margin不会[合并][4-16]。

[flex项][2-6]的百分比margin和padding会相对于下面二者之一来决定：
+ 他们本身的坐标（left/right百分比相对于width决定，top/bottom百分比相对于高度），或者，
+ 行内坐标（left/right/top/bottom百分比都相对于宽度决定）

客户端必须选择两种行为之一。

> 注意：这种不一致太差劲了，但是它准确地捕捉了世界的当前状态（在实现上没有共识，在CSSWG内部没有共识）。这正是CSSWG的意图：浏览器会覆盖两种行为之一，到时候再根据需求修改。

> 作者应该避免在所有[flex项][2-6]的margin或者padding中使用百分比，因为它们在不同浏览器中表现不一致。

margin为auto在相应的维度上会扩展来吸收额外的空间。它们可以用于对齐，或者将相邻的flex项分离。参考[用'auto'的margin对齐][1-14]。

### 4.3 flex项的Z-Ordering
[flex项][2-6]就像和行内块一样绘制 [[CSS21]][1-16]，除了用['order'][1-12]修改的文档顺序来替代原始的文档顺序，而且['auto'][4-16]以外的['z-index'][4-17]值创建了层叠上下文，即便['position'][4-18]是['static'][4-19]。

> 注意：在flex项外面被定位的后代元素仍然参与flex项创建的任何层叠上下文。

### 4.4 收起项
在flex项上指定'visibility:collapse'会让它成为*收起的flex项*，产生类似于在table-row或者table-column上设置'visibility:collapse'的影响：合并的flex项被移除来防止整体渲染，但是留下"支柱"来保持flex行侧轴尺寸的稳定。所以，如果一个flex容器只有一个flex行，动态折叠或展开flex项可能改变[flex容器][3-2]的[主轴尺寸][4-20]，但是会确保不影响[侧轴尺寸][1-11]而且不会引起剩余页面"晃动"布局。在折叠后，flex折行*会*重来一遍，然而，有多行的flex容器的交叉可能会也可能不会发生变化。

尽管折叠的flex项不会被渲染，它们确实会出现在[格式结构][4-21]中。所以，不像'display:none'的项 [[CSS21]][1-16]，追加到出现在格式结构盒子上的效果会（像递增计数器或运行动画和过渡）仍然会作用于折叠的项。

> **例4**  
> 在下面的示例中，侧边栏被设置了尺寸来与它的内容相适应。['visibility:collapse'][4-22]用于不影响宽度的情况下动态隐藏导航侧边栏的一部分，即使最宽的项（"Architecture"）在一个收起的区域中。

```css
@media (min-width: 60em) {
  /* 仅当有足够空间时两列布局（相对于默认的文本尺寸） */
  div { display: flex; }
  #main {
    flex: 1;         /* main占据了所有的剩余空间 */
    order: 1;        /* 把它放在导航的后边（右边） */
    min-width: 12em; /* 优化内容区域尺寸 */
  }
}
/* 菜单项使用flex布局以便visibility:collapse起作用 */
nav > ul > li {
  display: flex;
  flex-flow: column;
}
/* 不是目标时动态地折叠子菜单 */
nav > ul > li:not(:target):not(:hover) > ul {
  visibility: collapse;
}
```
```html
<div>
  <article id="main">
    Interesting Stuff to Read
  </article>
  <nav>
    <ul>
      <li id="nav-about"><a href="#nav-about">About</a>
        …
      <li id="nav-projects"><a href="#nav-projects">Projects</a>
        <ul>
          <li><a href="…">Art</a>
          <li><a href="…">Architecture</a>
          <li><a href="…">Music</a>
        </ul>
      <li id="nav-interact"><a href="#nav-interact">Interact</a>
        …
    </ul>
  </nav>
</div>
<footer>
…
```

为了计算撑起来的尺寸，flex布局开始随着所有项被展开时完成，然后在每一个项收起时被一个保持该项最初行的最初侧轴尺寸的支柱替代时重新布局。参考[flex布局算法][4-23]来查看'visibility:collapse'与flex布局交互的正式定义。

> 注意：在任何flex项上面使用'visibility:collapse'会导致部分flex布局算法重复执行代价最高的步骤。推荐作者继续使用'display:none'来隐藏不会被动态收起和展开的项，因为这对渲染引擎是更高效的。（既然当['visibility'][4-22]改变时只有部分步骤需要重复，然而对于动态的情况仍然推荐'visibility:collapse'）

### 4.5 flex项默认最小尺寸
为了给[flex项][2-6]提供一个更合理的最小尺寸，规范引入了一个新的['auto'][4-23]值作为CSS2.1中定义的['min-width'][4-24]和['min-height'][4-25]的初始值。[[CSS21]][1-16]

|key|value|
|:--|:--|
|Name:|['min-width'][4-24], ['min-height'][4-25]|
|New values:|auto|
|New Initial value:|auto|
|New computed value:|规定的百分比或绝对长度或关键字|
+ 'auto'  
在一个[主轴][1-6]上['overflow'][3-4]为['visible'][3-4]的[flex项][2-6]上，当指定了[flex项][2-6]的主轴最小尺寸属性时，指定一个*自动最小尺寸*。否则会计算为'0'（除非在将来的规范中另有定义）。  

通常来讲，[自动最小尺寸][4-26]是[内容尺寸][4-27]和[指定尺寸][4-28]两者中小的那个。然而，如果盒子有高宽比而且没有[指定尺寸][4-28]，它的[自动最小尺寸][4-26]是[内容尺寸][4-27]和[转换尺寸][4-29]两者中小的那个。  

该计算中用到的[内容尺寸][4-27]、[指定尺寸][4-28]和[转换尺寸][4-29]占用了相对了最小/最大/优先尺寸属性所以[自动最小尺寸][4-26]不干扰作者提供的约束，并且定义如下：
+ *指定尺寸*  
如果一项的[主轴尺寸属性][4-30]是[明确的][4-31]，那么[指定尺寸][4-28]就是那个尺寸（被[主轴尺寸属性][4-30]限制如果它是[明确的][4-31]）。否是就是未定义的。  
+ *转换尺寸*  
如果一项有固定的高宽比并且计算后的[侧轴尺寸属性][4-32]是明确的，那么[转换尺寸][4-29]就是那个尺寸（被[主轴尺寸属性][4-30]限制如果它们是[明确的][4-31]）。通过高宽比转换。否则就是未定义的。  
+ *内容尺寸*  
[内容尺寸][4-26]是[主轴][1-6]上的[最小内容尺寸][4-33]，如果有高宽比，是通过任何由高宽比转换的[明确的][4-31][最小和最大侧轴尺寸][4-32]属性限制的，然后进一步由[最大主轴尺寸属性][4-30]限制如果它是[明确的][4-31]。  

出于计算一个元素初始尺寸（譬如元素的[最小内容尺寸][4-33]）的目的，这个值会使元素尺寸在对应轴上变为不明确（就算它的['width'][2-8]属性指定为[明确的][4-31]尺寸）。注意这意味着相对于这个尺寸百分比会被当作'auto'对待。

但是，尽管在某些情况下这需要传递额外的布局去解析百分比，该值（像[[CSS3-SIZING]][2-13]中定义的['min-content'][4-34]、['max-content'][4-35]和['fit-content'][4-36]）不会防止该项内部的百分比被解析。

> 注意虽然基于内容的最小尺寸通常是合适的，而且有助于防止内容重叠或者容器外分割，但是在某些情况却不是这样的：
>
> 特殊地，如果flex尺寸用于一个文档的主体内容区域，最好设置一个确切的相对字体的最小宽度如['min-width: 12em'][4-24]。基于内容的最小宽度会导致一个很大的表格或很大的图片在伸展尺寸到整个区域，从而也会使文本没有必要地长而且难以阅读。
>
> 同时注意，当基于内容的尺寸被用在带有大量内容的项上时，布局引擎在找到最小尺寸前必须得遍历所有的内容，反而如果我们设置了确切的最小值，又是没有必要的。（对于带有少量的内容的项，遍历不重要，也不是性能关注点了。）

## 5. 排列和定位
flex容器的内容可以在任何方向按照任何顺序布局。它使我们容易达到之前需要复杂或者脆弱的方法，譬如['float'][3-6]和['clear'][3-7]属性达到的效果。本功能通过['flex-direction'][5-1]、['flex-wrap'][5-2]和['order'][5-3]属性暴露出来。

> 注意：flex布局重新排列功能特意*只影响视觉渲染*，不去管语音顺序和基于原始顺序的浏览。这使我们可以在保留无CSS浏览器和像语音和顺序浏览等线性模型原始顺序的完整性的同时处理视觉呈现。参考[重新排列和可访问行][5-4]和[flex布局概述][5-5]来查看使用这种二分法来提高可访问性的例子。
>
> 我们*不能*用['order'][1-12]或['flex-flow'][2-4] / ['flex-direction'][5-1]'*-reverse'值作为纠正原始顺序的替代，因为这会毁了文档的可访问性。

### 5.1 flex流方向：['flex-direction'][5-1]属性
|key|value|
|:--|:--|
|Name:|*['flex-direction'][5-1]*|
|Value:|row[ \| ][3-1]row-reverse[ \| ][3-1]column[ \| ][3-1]column-reverse|
|Initial:|row|
|Applies to:|[flex容器][3-2]|
|Inherited:|no|
|Percentages:|n/a|
|Media|Visual|
|Computed Value:|指定值|
|Animation type:|不连续的|

['flex-direction'][5-1]属性指定了[flex项][2-6]如何在[flex容器][3-2]中摆放，通过设置flex容器[主轴][1-6]的方向。它确定了flex项布局的方向。

+ *'row'*  
flex容器的[主轴][1-6]和当前[书写方式][2-5]的[行内轴][5-6]有着相同的方向。[主轴起点][5-7]和[主轴终点][5-8]方向和当前[书写方式][2-5]的[行内轴起点][4-11]和[行内轴终点][5-9]分别相等。 
+ *'row-reverse'*  
和['row'][5-10]相同，只不过[主轴起点][5-7]和[主轴终点][5-8]方向调换。  
+ *'column'*  
flex容器的[主轴][1-6]和当前[书写方式][2-5]的[块状轴][5-11]有着相同的方向。[主轴起点][5-7]和[主轴终点][5-8]方向和当前[书写方式][2-5]的[块状轴起点][4-10]和[块状轴终点][5-12]分别相等。 
+ *'column-reverse'*
和['column'][5-13]相同，只不过[主轴起点][5-7]和[主轴终点][5-8]方向调换。

> 注意：相反的值不会颠倒盒子排列：就像['writing-mode'][2-5]和['direction'][5-14]一样[CSS3-WRITING-MODE][5-15]，他们仅改变流的方向。绘制顺序、语音顺序和逻辑浏览顺序不会受到影响。

### 5.2 flex折行：['flex-wrap'][5-2]属性
|key|value|
|:--|:--|
|Name:|*'flex-wrap'*|
|Value:|nowrap[ \| ][3-1]wrap[ \| ][3-1]wrap-reverse|
|Initial:|nowrap|
|Applies to:|flex容器|
|Inherited:|no|
|Percentage:|n/a|
|Media:|Visual|
|Computed value:|指定值|
|Animation type:|不连续的|

['flex-wrap'][5-2]控制flex容器是[单行][5-16]还是[多行][5-17]的，以及[侧轴][4-13]的方向来确定新的行层叠的方向。

+ *'nowrap'*  
flex容器为[单行][5-16]  
+ *'wrap'*  
flex容器为[多行][5-17]  
+ *'wrap-reverse'*  
同['wrap'][5-18]

对于非['wrap-reverse'][5-19]的值，[侧轴起点][5-20]等于当前[书写方式][2-5]的[行内轴起点][4-11]或[块状轴起点][4-10]（在[侧轴][4-13]中的那个）并且[侧轴终点][5-21]方向与[侧轴起点][5-20]方向相反。当['flex-wrap'][5-2]为['wrap-reverse'][5-19]时，[侧轴起点][5-20]和[侧轴终点][5-21]方向调换。

### 5.3 flex方向与折行：['flex-flow'][2-4]缩写
|key|value|
|:--|:--|
|Name:|*'flex-flow'*|
|Value:|<[flex-direction][5-1]>[ \|\| ][5-22]<[flex-wrap][5-2]>|
|Initial:|row nowrap|
|Applies to:|[flex容器][3-2]|
|Inherited:|no|
|Percentage:|n/a|
|Media:|Visual|
|Computed value:|指定值|
|Animation type:|不连续的|

['flex-flow'][2-4]属性是设置['flex-direction'][5-1]和['flex-wrap'][5-2]属性的缩写，同时定义了flex容器的主轴和侧轴。

> **例5**  
> 一些英文文档（按照左右，水平书写方式）中有效的流的例子：

```css
div { flex-flow: row; }
/* 初始值. 主轴是行内的，无折行。（项目会收缩来适应或者溢出） */
```
```css
div { flex-flow: column wrap; }
/* 主轴是块方向 （从上到下）并且在行内方向折行（向右）。 */
```
```css
div { flex-flow: row-reverse wrap-reverse; }
/* 主轴与行内方向相反（从右到左）。向上折行。 */
```

> 注意['flex-flow'][2-4]对[书写方式][2-5]敏感。在垂直的日文中，譬如，一个['row'][5-10]flex容器将它的容器从上向下布局，就像例子中所见到的那样：

### 5.4 显示顺序：['order'][1-12]属性
[flex项][2-6]默认会和原始文档中的出现的顺序一样显示和布局。['order'][1-12]属性被用于改变该顺序。
|key|value|
|:--|:--|
|Name:|*'order'*|
|Value:|<[integer][5-23]>|
|Initial:|0|
|Applies to:|[flex项][2-6]和[flex容器][3-2]中绝对定位的子元素|
|Inherited:|no|
|Percentage:|n/a|
|Media:|Visual|
|Computed value:|指定值|
|Animation type:|integer|

['order'][1-12]属性通过分配序号组来控制flex容器内子元素在flex容器中出现的顺序。它采用一个简单的['<integer>'][5-23]值，指定[flex项][2-6]属于哪个序号组。

flex容器在*可修改的文档顺序*中布局它的内容，从最小的序号组开始增长。同样序号组的项按照他们在原始文档出出现的顺序布局。它同时会影响[绘制顺序][5-24][CSS21][1-16]，就像flex项在原始文档中被重新排列一样。

> **例6**  
> 下面的表格展示了一个简单的标签切换界面，激活的标签始终在第一位：  

> 可以通过下面的CSS（只显示相关代码）实现：

```css
.tabs {
  display: flex;
}
.tabs > .current {
  order: -1; /* 比默认的0小 */
}
```

除非被将来的规范声明，否则该属性对不是[flex容器][3-2]子元素的盒子没有影响。

#### 5.4.1 重新排列和可访问性
['order'][1-12]属性*不*影响非显示媒介（譬如[语音][5-25]）的渲染。同样地，['order'][1-21]不影响默认的遍历顺序或者逻辑浏览模式（譬如在链接中循环，参考[tabindex][5-26][HTML5][5-27]）

> 我们*不要*仅把['order'][1-12]属性用于显示，不用于逻辑的重新排列的内容。样式表使用['order'][1-12]来完成逻辑重新排列是不确定的。

> 注意：这导致线性呈现内容的非显示媒介和无CSS的浏览器，会依赖逻辑的原始顺序，然后['order'][1-12]用于适应显示顺序。（因为视觉感知是二维的而且是非线性的，需要的显示顺序不总是逻辑的。）

> **例7**  
> 许多页面在标记上有相似的形状，顶部是header，底部是footer，然后是内容区域和附加的两列。通常需要内容在页面源码中最先出现，在附件列之前。然后，这使很多常用的设计，譬如简单地使两个附加列靠左内容靠左，都难以实现。过去的许多年这被以多种方式提出，当有两个附加列时通常叫"圣杯布局"。['order'][1-12]使之简单。例如，采取以下简略页面的代码和所需的布局：

```html
<!DOCTYPE html>
<header>...</header>
<main>
   <article>...</article>
   <nav>...</nav>
   <aside>...</aside>
</main>
<footer>...</footer>
```

> 该布局很容易被flex布局实现：

```css
main { display: flex; }
main > article { order: 2; min-width: 12em; flex:1; }
main > nav     { order: 1; width: 200px; }
main > aside   { order: 3; width: 200px; }
```

> 作为附带的好处，所有列都是默认['等高'][4-14]的，并且为了填满屏幕主要内容会尽可能宽。还有，可以结合媒体查询来在债屏幕上切换为全部垂直布局。

```css
@media all and (max-width: 600px) {
  /* 过窄，不支持三列 */
  main { flex-flow: column; }
  main > article, main > nav, main > aside {
    /* 重置为文档顺序 */
    order: 0; width: auto;
  }
}
```
（更多通过多行flex容器来实现甚至更智能的折行的用法留给读者当做练习。）

为了保留在所有呈现模式中作者想要的顺序，创作工具—包括WYSIWYG编辑器和基本web的工具 必须重新排列底层文档源码并且不能使用['order'][1-12]来完成渲染，除非作者明确表明底层文档顺序（它确定了语音和导航顺序）应该*不同步*于显示顺序。

> **例8**  
> 例如，工具可能提供flex项拖放重新排列同时为每个屏幕尺寸范围提供可选择布局处理媒体查询。
>
> 既然多数时间，重新排列不但会影响所有屏幕范围，而且会影响导航和语音，工具应该在DOM层完成拖放重新排列。然后在某些情况下，我们或许想每个屏幕尺寸下显示顺序不同。工具可以提供这个功能通过一起使用['order'][1-12]和媒体查询，并且将最小的尺寸约束在底层DOM顺序（因为这最可能是逻辑线性呈现顺序），当用['order'][1-12]来在其他尺寸屏幕上来确定视觉呈现顺序时。
> 该工具会一致，然后只用['order'][1-12]处理拖放重新排列（然而那样会方便实现）会不一致。

> 注意：客户端，包括浏览器，可用的技术和扩展，会提供立体导航功能。本章节不排除关于['order'][1-12]属性在这样的立体导航模式下确定元素顺序；事实上需要考虑这样的功能起作用。客户端用['order'][1-12]确定有顺序的导航，但是不说明元素间的空间关系（就像由多种CSS布局包括且不限于flex布局表达的），是不一致的。

## 6. flex行
[flex容器][3-2]中的[flex项][2-6]被布局并且对齐在*flex行*内，通过布局算法用于分组和对齐的假想容器。flex容器可以[单行][5-16]或[多行][5-17]的，根据['flex-wrap'][5-2]属性：
+ *单行*flex容器（譬如带有['flex-wrap:nowrap'][5-2]的）在单独的一行内布局它所有的子元素，即便会导致内容溢出。
+ *多行*flex容器（譬如带有['flex-wrap:wrap'][5-2]或者['flex-wrap:wrap-reverse'][5-2]的）会将它的[flex项][2-6]折成多行，和文本在太宽以致于不能适应当前行时折到新的一行是类似的。当新的行被创建时，它们根据['flex-wrap'][5-2]会沿着flex容器的[侧轴][4-13]堆叠。每一个行都至少包含一个[flex项][2-6]，除非flex容器本身是完全空的。

> **例9**  
> 该例子展示了四个边挨着边水平方向不匹配的按钮，而且会折成多行。

```css
#flex {
  display: flex;
  flex-flow: row wrap;
  width: 300px;
}
.item {
  width: 80px;
}
<div id="flex">
  <div class="item">1</div>
  <div class="item">2</div>
  <div class="item">3</div>
  <div class="item">4</div>
</div>
```

> 因为flex容器是300px宽，只有三项在单独的一行上合适。它们占据240px，留下60px的剩余空间。因为['flex-flow'][2-4]属性指定了[多行][5-17]的flex容器（由于['wrap'][5-18]关键字出现在它的属性值中），flex容器会再创建一行来包含最后一项。

一旦内容被折成多行，每一行都会独立布局；弹性长度和['justify-content'][6-1]以及['align-self'][1-13]属性一次只考虑单独在一行的项。

在一个[多行][5-17][flex容器][3-2]（即使只有单独的一行），每一行的[侧轴尺寸][1-11]就是需要包含[flex项][2-6]在这一行的最小尺寸（经过['align-self'][1-13]对齐后）。在一个[单行][5-16][flex容器][3-2]中，这一行的[侧轴尺寸][1-11]就是flex容器的[侧轴尺寸][1-11]，并且['align-content'][4-12]没有影响。这一行的[主轴尺寸][4-20]总是和flex容器内容盒子的[主轴尺寸][4-20]相同。

> **例10**  
> 这有一个和前面相同的例子，除了flex项被给予['flex:auto'][6-2]。第一行有60px的剩余空间，而且所有项都有同样的伸缩性，所以那一行上的三项每一项会接受20px额外的宽度，最终100px宽。剩下的项是在它本身的行上，会伸展到这一整行的宽度，如300px。

## 7. 伸缩性
flex布局的定义目的是使[flex项][2-6]"有弹性"，在它们的宽/高适应[主轴维度][2-7]中的可用空间后。这是由['flex'][6-2]属性实现的。flex容器为它的项分配空闲空间（与它们的[flex伸展因子][7-1]成比例）来填空容器，或者收缩它们（与它们的[flex伸展因子][7-1]成比例）来阻止溢出。

[flex项][2-6]是*完全非弹性的*如果它的['flex-grow'][7-2]和['flex-shrink'][7-3]值是0，否则是*弹性的*。

### 7.1 ['flex'][6-2]缩写
|key|value|
|:--|:--|
|Name:|*'flex'*|
|Value:|none[ \| ][3-1][[<'flex-grow'>][7-2][<'flex-shrink'>?][7-3][ \|\| ][5-22][<'flex-basis'>][7-4]]|
|Initial:|1 0 auto|
|Applies to:|[flex项][2-6]|
|Inherited:|no|
|Percentage:|参考单独属性|
|Media:|Visual|
|Computed value:|参考单独属性|
|Animation type:|参考单独属性|
|Canonical order:|每个语法|

['flex'][6-2]属性声明了*弹性长度*的组成：['flex伸展因子'][7-1]和['flex收缩因子'][7-5]，以及['flex基数'][7-4]。当一个盒子是[flex项][2-6]时，['flex'][6-2]会被征询*而不是*[主轴尺寸属性][4-30]来确定盒子的[主轴尺寸][4-20]。如果一个盒子不是[flex项][2-6]，['flex'][6-2]不起作用。

+ *['<'flex-grow'>'][7-2]*  
该[\<number\>][7-6]组成部分设置了['flex-grow'][7-2][写法][7-7]而且指定了*flex伸展因子*，确定[flex项][2-6]被分配正空余空间时，会相对于flex容器的剩余[flex项][2-6]会伸展多少。被省略时会被设为'1'。  
+ *['<'flex-shrink'>'][7-3]*  
该[\<number\>][7-6]组成部分设置了['flex-shrink'][7-3][写法][7-7]而且指定了*flex收缩因子*，确定[flex项][2-6]被分配负空余空间时，会相对于flex容器的剩余[flex项][2-6]会收缩多少。被省略时会被设为'1'。  
+ *['<'flex-basis'>'][7-4]*  
该组成部分设置了['flex-basis'][7-4][写法][7-7]，指定了*flex基数*：[flex项][2-6]的初始[主轴尺寸][4-20]，在空间按照flex因子被分配之前。  
[<'flex-basis'>][7-4]接受和['width'][2-8]与['height'][2-9]相同的值（除了['auto'][7-8]会被不同对待）外加['content'][7-9]关键字。
  + *'auto'*  
当在一个[flex项][2-6]上指定时，['auto'][7-8]关键字使[主轴尺寸属性][4-30]重新获取为使用的['flex-basis'][7-4]。如果值本身是['auto'][7-10]，那么使用的值时['content'][7-11]。
  + *'content'*  
表示基于[flex项][2-6]的内容自动的尺寸。  

> 注意：这个值没有出现在弹性盒布局的初始版本中，所以一些顺序的实现不会支持它。相同的效果可以通过一起使用'auto'和'auto'的主轴尺寸（['width'][2-8]或['height'][2-9]）

  + [\<'width'\>][2-8]  
对于其他的值，['flex-basis'][7-4]与['width'][2-8]和['height'][2-9]由同样方法被解析。  

[0-1]: http://www.w3.org/TR/CSS/
[1-1]: https://www.w3.org/TR/CSS2/visuren.html#floats
[1-2]: https://www.w3.org/TR/css3-multicol/
[1-3]: https://www.w3.org/TR/css-flexbox-1/#flex-direction-property
[1-4]: https://www.w3.org/TR/css-flexbox-1/#valdef-flex-direction-row-reverse
[1-5]: https://www.w3.org/TR/css-flexbox-1/#order-property
[1-6]: https://www.w3.org/TR/css-flexbox-1/#main-axis
[1-7]: https://www.w3.org/TR/css-flexbox-1/#flex-wrap-property
[1-8]: https://www.w3.org/TR/css-flexbox-1/#flexibility
[1-9]: https://www.w3.org/TR/css-flexbox-1/#alignment
[1-10]: https://www.w3.org/TR/css-flexbox-1/#visibility-collapse
[1-11]: https://www.w3.org/TR/css-flexbox-1/#cross-size
[1-12]: https://www.w3.org/TR/css-flexbox-1/#propdef-order
[1-13]: https://www.w3.org/TR/css-flexbox-1/#propdef-align-self
[1-14]: https://www.w3.org/TR/css-flexbox-1/#auto-margins
[1-15]: https://www.w3.org/TR/CSS21/visuren.html#propdef-display
[1-16]: https://www.w3.org/TR/css-flexbox-1/#biblio-css21
[1-17]: https://www.w3.org/TR/css-pseudo-4/#selectordef-first-line
[1-18]: https://www.w3.org/TR/css-pseudo-4/#first-letter-pseudo
[2-1]: https://www.w3.org/TR/css-flexbox-1/#valdef-display-flex
[2-2]: https://www.w3.org/TR/css-flexbox-1/#valdef-display-inline-flex
[2-3]: https://www.w3.org/TR/css3-writing-modes/#abstract-box
[2-4]: https://www.w3.org/TR/css-flexbox-1/#propdef-flex-flow
[2-5]: https://www.w3.org/TR/css-writing-modes-3/#writing-mode
[2-6]: https://www.w3.org/TR/css-flexbox-1/#flex-item
[2-7]: https://www.w3.org/TR/css-flexbox-1/#main-dimension
[2-8]: https://www.w3.org/TR/CSS21/visudet.html#propdef-width
[2-9]: https://www.w3.org/TR/CSS21/visudet.html#propdef-height
[2-10]: https://www.w3.org/TR/css-flexbox-1/#flex-line
[2-11]: https://www.w3.org/TR/css-flexbox-1/#cross-dimension
[2-12]: http://www.w3.org/TR/css3-sizing/
[2-13]: https://www.w3.org/TR/css-flexbox-1/#biblio-css3-sizing
[3-1]: https://www.w3.org/TR/css3-values/#comb-one
[3-2]: https://www.w3.org/TR/css-flexbox-1/#flex-container
[3-3]: https://www.w3.org/TR/CSS2/visudet.html#containing-block-details
[3-4]: https://www.w3.org/TR/css-overflow-3/#overflow
[3-5]: https://www.w3.org/TR/css-flexbox-1/#biblio-css3col
[3-6]: https://www.w3.org/TR/CSS21/visuren.html#propdef-float
[3-7]: https://www.w3.org/TR/CSS21/visuren.html#propdef-clear
[3-8]: https://www.w3.org/TR/CSS21/visudet.html#propdef-vertical-align
[3-9]: https://www.w3.org/TR/CSS2/visuren.html#dis-pos-flo
[4-1]: https://www.w3.org/TR/CSS2/text.html#white-space-prop
[4-2]: https://www.w3.org/TR/css-text-3/#white-space
[4-3]: https://www.w3.org/TR/css-flexbox-1/#item-margins
[4-4]: https://www.w3.org/TR/css-display/#transformations
[4-5]: https://www.w3.org/TR/css-flexbox-1/#biblio-css3-display
[4-6]: https://www.w3.org/TR/CSS2/visudet.html#abs-non-replaced-width
[4-7]: https://www.w3.org/TR/css-flexbox-1/#static-position-rectangle
[4-8]: https://www.w3.org/TR/css3-align/#alignment-container
[4-9]: https://www.w3.org/TR/CSS2/visudet.html#abs-non-replaced-width
[4-10]: https://www.w3.org/TR/css-writing-modes-3/#block-start
[4-11]: https://www.w3.org/TR/css-writing-modes-3/#inline-start
[4-12]: https://www.w3.org/TR/css-flexbox-1/#propdef-align-content
[4-13]: https://www.w3.org/TR/css-flexbox-1/#cross-axis
[4-14]: https://www.w3.org/TR/css-flexbox-1/#valdef-align-items-stretch
[4-15]: https://www.w3.org/TR/css-flexbox-1/#valdef-align-items-flex-start
[4-16]: https://www.w3.org/TR/css3-positioning/#valdef-z-index-auto
[4-17]: https://www.w3.org/TR/css3-positioning/#propdef-z-index
[4-18]: https://www.w3.org/TR/css3-positioning/#propdef-position
[4-19]: https://www.w3.org/TR/css3-positioning/#valdef-position-static
[4-20]: https://www.w3.org/TR/css-flexbox-1/#main-size
[4-21]: https://www.w3.org/TR/CSS2/intro.html#formatting-structure
[4-22]: https://www.w3.org/TR/CSS21/visufx.html#propdef-visibility
[4-23]: https://www.w3.org/TR/css-flexbox-1/#layout-algorithm
[4-24]: https://www.w3.org/TR/CSS21/visudet.html#propdef-min-width
[4-25]: https://www.w3.org/TR/CSS21/visudet.html#propdef-min-height
[4-26]: https://www.w3.org/TR/css-flexbox-1/#min-width-automatic-minimum-size
[4-27]: https://www.w3.org/TR/css-flexbox-1/#content-size
[4-28]: https://www.w3.org/TR/css-flexbox-1/#specified-size
[4-29]: https://www.w3.org/TR/css-flexbox-1/#transferred-size
[4-30]: https://www.w3.org/TR/css-flexbox-1/#main-size-property
[4-31]: https://www.w3.org/TR/css-flexbox-1/#definite
[4-32]: https://www.w3.org/TR/css-flexbox-1/#cross-size-property
[4-33]: https://www.w3.org/TR/css-sizing-3/#min-content
[4-34]: https://www.w3.org/TR/css-sizing-3/#valdef-width-min-content
[4-35]: https://www.w3.org/TR/css-sizing-3/#valdef-width-max-content
[4-36]: https://www.w3.org/TR/css-sizing-3/#valdef-width-fit-content
[5-1]: https://www.w3.org/TR/css-flexbox-1/#propdef-flex-direction
[5-2]: https://www.w3.org/TR/css-flexbox-1/#propdef-flex-wrap
[5-3]: https://www.w3.org/TR/css-flexbox-1/#propdef-order
[5-4]: https://www.w3.org/TR/css-flexbox-1/#order-accessibility
[5-5]: https://www.w3.org/TR/css-flexbox-1/#overview
[5-6]: https://www.w3.org/TR/css-writing-modes-3/#inline-axis
[5-7]: https://www.w3.org/TR/css-flexbox-1/#main-start
[5-8]: https://www.w3.org/TR/css-flexbox-1/#main-end
[5-9]: https://www.w3.org/TR/css-writing-modes-3/#inline-end
[5-10]: https://www.w3.org/TR/css-flexbox-1/#valdef-flex-direction-row
[5-11]: https://www.w3.org/TR/css-writing-modes-3/#block-axis
[5-12]: https://www.w3.org/TR/css-writing-modes-3/#block-end
[5-13]: https://www.w3.org/TR/css-flexbox-1/#valdef-flex-direction-column
[5-14]: https://www.w3.org/TR/css-writing-modes-3/#propdef-direction
[5-15]: https://www.w3.org/TR/css-flexbox-1/#biblio-css3-writing-modes
[5-16]: https://www.w3.org/TR/css-flexbox-1/#single-line
[5-17]: https://www.w3.org/TR/css-flexbox-1/#multi-line
[5-18]: https://www.w3.org/TR/css-flexbox-1/#valdef-flex-wrap-wrap
[5-19]: https://www.w3.org/TR/css-flexbox-1/#valdef-flex-wrap-wrap-reverse
[5-20]: https://www.w3.org/TR/css-flexbox-1/#cross-start
[5-21]: https://www.w3.org/TR/css-flexbox-1/#cross-end
[5-22]: https://www.w3.org/TR/css3-values/#comb-any
[5-23]: https://www.w3.org/TR/css3-values/#integer-value
[5-24]: https://www.w3.org/TR/CSS2/zindex.html
[5-25]: https://www.w3.org/TR/css3-speech/
[5-26]: https://www.w3.org/TR/html5/editing.html#sequential-focus-navigation-and-the-tabindex-attribute
[5-27]: https://www.w3.org/TR/css-flexbox-1/#biblio-html5
[6-1]: https://www.w3.org/TR/css-flexbox-1/#propdef-justify-content
[6-2]: https://www.w3.org/TR/css-flexbox-1/#propdef-flex
