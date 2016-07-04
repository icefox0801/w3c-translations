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
  + 5.3 flex方向和折行：'flex-flow'缩写
  + 5.4 显示顺序：'order'属性
    + 5.4.1 重排和可访问性

+ 6 Flex行线

+ 7 弹性
  + 7.1 flex缩写
    + 7.1.1 flex基本值
  + 7.2 弹性组成
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
- 可以沿着一条（[主][1-6]）轴直线布局或沿着第二条（[侧][1-7]）轴折成多行
- 可以["伸缩"它们的尺寸][1-8]来响应可用空间
- 可以在第二条（[侧][1-7]）轴上相对于它们的容器[对齐][1-9]或者相互对齐
- 可以沿着[主轴][1-6]被动态[收起][1-10]或展开，同时保留容器的[侧轴尺寸][1-11]

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
<table>
<tbody>
  <tr><td>Name:</td><td>['display'][1-15]</td></tr>
  <tr><td>New values:</td><td>flex[ | ][3-1]inline-flex</td></tr>
</tbody>
</table>

*'flex'*  
该值会使一个元素生成一个块级[flex容器][3-2]。  
*'inline-flex'*  
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

> <center>作者应该避免在所有[flex项][2-6]的margin或者padding中使用百分比，因为它们在不同浏览器中表现不一致。</center>

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

<table>
<tbody>
<tr><td>Name:</td><td>['min-width'][4-24], ['min-height'][4-25]</td></tr>
<tr><td>New values:</td><td>auto</td></tr>
<tr><td>New Initial value></td><td>auto</td></tr>
<tr><td>New computed value:</td><td>规定的百分比或绝对长度或关键字</td></tr>
</tbody>
</table>
 
'auto'  
&emsp;&emsp;在一个[主轴][1-6]上['overflow'][3-4]为['visible'][3-4]的[flex项][2-6]上，当指定了[flex项][2-6]的主轴最小尺寸属性时，指定一个*自动最小尺寸*。否则会计算为'0'（除非在将来的规范中另有定义）。

通常来讲，[自动最小尺寸][4-26]是[内容尺寸][4-27]和[指定尺寸][4-28]两者中小的那个。然而，如果盒子有高宽比而且没有[指定尺寸][4-28]，它的[自动最小尺寸][4-26]是[内容尺寸][4-27]和[转换尺寸][4-29]两者中小的那个。

该计算中用到的[内容尺寸][4-27]、[指定尺寸][4-28]和[转换尺寸][4-29]占用了相对了最小/最大/优先尺寸属性所以[自动最小尺寸][4-26]不干扰作者提供的约束，并且定义如下：

*指定尺寸*  
&emsp;&emsp;如果一项的[主轴尺寸属性][4-30]是[明确的][4-31]，那么[指定尺寸][4-28]就是那个尺寸（被[主轴尺寸属性][4-30]限制如果它是[明确的][4-31]）。否是就是未定义的。

*转换尺寸*  
&emsp;&emsp;如果一项有固定的高宽比并且计算后的[侧轴尺寸属性][4-32]是明确的，那么[转换尺寸][4-29]就是那个尺寸（被[主轴尺寸属性][4-30]限制如果它们是[明确的][4-31]）。通过高宽比转换。否则就是未定义的。

*内容尺寸*  
&emsp;&emsp;[内容尺寸][4-26]是[主轴][1-6]上的[最小内容尺寸][4-33]，如果有高宽比，是通过任何由高宽比转换的[明确的][4-31][最小和最大侧轴尺寸][4-32]属性限制的，然后进一步由[最大主轴尺寸属性][4-30]限制如果它是[明确的][4-31]。

出于计算一个元素初始尺寸（譬如元素的[最小内容尺寸][4-33]）的目的，这个值会使元素尺寸在对应轴上变为不明确（就算它的['width'][2-8]属性指定为[明确的][4-31]尺寸）。注意这意味着相对于这个尺寸百分比会被当作'auto'对待。

但是，尽管在某些情况下这需要传递额外的布局去解析百分比，该值（像[[CSS3-SIZING]][2-13]中定义的['min-content'][4-34]、['max-content'][4-35]和['fit-content'][4-36]）不会防止该项内部的百分比被解析。

> 注意虽然基于内容的最小尺寸通常是合适的，而且有助于防止内容重叠或者容器外分割，但是在某些情况却不是这样的：
>
> 特殊地，如果flex尺寸用于一个文档的主体内容区域，最好设置一个确切的相对字体的最小宽度如['min-width: 12em'][4-24]。基于内容的最小宽度会导致一个很大的表格或很大的图片在伸展尺寸到整个区域，从而也会使文本没有必要地长而且难以阅读。
>
> 同时注意，当基于内容的尺寸被用在带有大量内容的项上时，布局引擎在找到最小尺寸前必须得遍历所有的内容，反而如果我们设置了确切的最小值，又是没有必要的。（对于带有少量的内容的项，遍历不重要，也不是性能关注点了。）
 
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
