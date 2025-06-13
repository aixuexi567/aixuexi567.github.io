---
title: 《CSS权威指南4》- Chapter1.2 - CSS与HTML结合
categories: 技术总结
date: 2019-04-03 10:06:59
tags:
  - CSS权威指南-第四版
  - 翻译
---

在详细学习任何编写 CSS 之前，我们需要了解如何将 CSS 与文档关联。毕竟，如果不把二者结合，CSS 就没有办法影响文档。我们将在 HTML 设置中探讨这个问题，因为它是最常见的。

### 结合 CSS 和 HTML

我已经提到过，HTML 文档有一个固有的结构，并且需要重申这一点。实际上，这也是旧网页的问题之一：我们之中大部分人忘记了文档应该有一个内在结构，这与视觉结构完成不同。我们急于创建最酷的网页，我们弯曲、扭曲了网页，并通常会忽略一点，页面应该包含有具有某种结构含义的信息。  
这种结构是 HTML 和 CSS 之间关系中固有部分，没有它，就不会有任何关系。为了更好的理解这一点，让我们看一个 HTML 文档的例子，并把它分成几部分：

```HTML
<html>
  <head>
    <title>Eric's World Waffles</title>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <link ref="stylesheet" type="text/css" href="sheet1.css" media="all">
    <style>
      @import url(sheet2.css);
    </style>
  </head>
  <body>
    <h2>Waffles</h2>
    <p style="color: gray;">The most wonderful of all breakfast foods is
the waffle—a ridged and cratered slab of home-cooked</p>
  </body>
</html>
```

现在，让我们研究一下文档与 CSS 关联的多种方式。

#### link 标签

首先，考虑`link`标签的作用：

```HTML
<link ref="stylesheet" type="text/css" href="sheet1.css" media="all">
```

`link`标签是一个很少被关注，但是完全合法的标签，它在 HTML 规范中已经存在多年，等待着被好好利用，施展才华。其基本目的是允许 HTML 创作人员将包含`link`标签的文档与其他文档相关联。CSS 使用它来将样式表链接到文档中。在图`Figure1-5`中，一个名为`sheet1.css`的样式表被链接到文档中。

![Figure 1-5](/image/Figure1-5.png)

这样的样式表并不是 HTML 文档的一部分，但是被 HTML 文档使用，称为**外部样式表**，因为它们是 HTML 文档外部的样式表。  
要成功加载一个外部样式表，`link`标签必须放在`head`元素内部，但不能放在其它元素的内部（如 title）。这样一来，web 浏览器才能找到并加载样式表，然后使用它包含的任何样式来呈现 HTML 文档，如图`Figure1-5`所示。  
图`figure1-5`还显示了通过`@import`声明来加载外部样式表`sheet2.css`.`@import`声明必须放在包含它们样式表的开头，其他的没有限制。  
外部样式表有什么格式呢？它是一个规则列表，就像我们上一节和示例 HTML 文档中见到的一样。但在本例中，规则被保存到其自己的文件中。只要记住，**样式表中不能包含 HTML 或任何其他标记语言，只能有样式规则。**以下是一个外部样式表的内容：

```CSS
h1 {
  color: red;
}
h2 {
  color: maroon;
  backgroud: white;
  font: medium Helvetica;
}
```

这就是它的全部内容，完全没有 HTML 标签和注释，只有简单的样式声明。它们被存入纯文本文件，通常给一个`.css`的扩展名，如`sheet1.css`所示。
`.css`文件扩展名不是必要的，但是一些较老的浏览器无法识别包含样式表的文件，除非文件以`.css`结尾，即使在`link`元素中包含正确的类型.`(type="text/css")`.实际上，有些 web 服务器不会将文件作为`text/css`传递，除非文件名以`.css`结尾，不过通常可以通过更改服务器的配置文件来修复这个问题。

#### 属性

对于`link`标签的剩余部分，其属性和值是相当简单。`rel`代表"关系(relation)"，在这里该“关系”是`stylesheet`.属性`type`总是被设置为`text/css`,这个值描述了使用`link`标签将要被加载的数据的类型，这样，浏览器就知道这个样式表示 CSS 样式表，这一事实将决定浏览器任何处理它导入的数据。毕竟，将来有可能会使用其他的样式语言，因此声明使用何种语言是很重要的。  
接下来，我们看到`href`属性，这个属性的值是样式表的 URL，它可以是绝对 URL，也可以是相当 URL,取决于你要做的工作。在我们的例子中，URL 是相对 URL。  
最后，还有一个`media`属性，这个属性的值是一个或多个媒体描述符，它们是关于媒体类型和这些媒体特性的规则，每个规则使用逗号分割。因此，例如，你可是在屏幕和投影媒体中使用一些链接样式表：

```HTML
<link ref="stylesheet" type="text/css" href="sheet.css" media="screen, projection">
```

媒体描述符可能变得相当复杂，在以后章节会详细解释。现在，我们继续关注所示的基本的媒体类型。  
注意，一个文档可能关联多个链接样式表。如果这样的话，只有那些带有`ref`为`stylesheet`的`link`标签才会在文档的初始显示中使用。因此，如果你想链接 名为`basic.css`和`splash.css`的 2 个样式表，应该这样做：

```HTML
<link rel="stylesheet" type="text/css" href="basic.css">
<link rel="stylesheet" type="text/css" href="splash.css">
```

这将导致浏览器加载这 2 个样式表，合并它们的规则，并将其全部应用于文档，例如：

```HTML
<link rel="stylesheet" type="text/css" href="basic.css">
<link rel="stylesheet" type="text/css" href="splash.css">
<p class="a1">This paragraph will be gray only if styles from the stylesheet 'basic.css' are applied</p>
<p class="b1">This paragraph will be gray only if styles from the stylesheet 'splash.css' are applied</p>
```

有一个属性不在示例标记中，但可用，它是`title`属性。这个属性不经常使用，但将来会变得很重要。如果使用不当，可能会有意想不到的后果。为什么呢？我们将在下一节对探讨。

#### 候选样式表

还可以去定义候选样式表。将`ref`属性的值设置为`alternate stylesheet`就可以定义候选样式表，只有被用户选中才会用于文档的展示。如果一个浏览器能使用候选样式表，它会使用`link`元素的`title`属性的值去生成一个候选样式列表。

```HTML
  <link rel="stylesheet" type="text/css"
href="sheet1.css" title="Default">
<link rel="alternate stylesheet" type="text/css"
href="bigtext.css" title="Big Text">
<link rel="alternate stylesheet" type="text/css"
href="zany.css" title="Crazy colors!">
```

然后用户就能选择他们想用的样式，浏览器会从第一个样式（这本例中，也就是标记为"Default"的样式）切换为用户所选择任何样式。图**Figure 1-6**显示了这种选择机制的实现方法（事实上，是在 CSS 复兴的早期）。  
![Figure 1-6](/image/Figure1-6.png)  
**截止到 2016 年底，候选样式表在大多数基于`Gecko`内核的浏览器中都得到了支持，比如 Firefox,Opera。在 IE 家族中，用户可以通过 javaScript 得到支持，但浏览器本身是不支持的。`web-lit`家族的浏览器(如 Chrome)不支持选择候选样式表的，所有这里就不在讨论它了......**

#### style 元素

`style`元素是包含样式表的一种方式，它出现在文档中

```HTML
<style type="text/css"></style>
```

`sytle`应该始终使用`type`属性，对于 CSS 文档，正确的值是`text/css`,就像`link`元素一样。  
`style`元素应该始终以`<style type="text/css">`开始，如上例所示，其后可跟一个或多个样式，最后以一个结束标记`</style>`结束。还可以给`style`元素一个`media`属性，它的功能与前面讨论的链接样式表功能相同。  
在开始和结束`style`标记中的样式被称为**文档样式表**或**嵌套样式表（因为这个样式表示嵌套在文档中的）**。它将包含很多要应用于文档的样式，还可以使用`@import`指令包含多个指向外部样式表的链接。

#### `@import`指令

现在，我们来讨论在`style`标记中的内容，首先，我们有一些与`link`非常相似的东西：`@import`指令

```CSS
<style type="text/css">
  @import url(sheet2.css);
</style>
```

与`link`一样，`@import`能指导浏览器去加载外部样式表，并且在渲染 HTML 文档时使用其样式。唯一的区别在于命令的语法和位置。可以看到，`@import`出现在`style`容器中，它必须放在其它 CSS 规则之前，否则不起作用。考虑下面的例子：

```HTML
<style type="text/css">
  @import url(styles.css);
  h1 {
    color: gray;
  }
</style>
```

类似于`link`标签，在一个文档中可以出现不止一个`@import`语句。但不同于`link`，每一个`@import`指令的样式表 都会被加载并使用，所以用`@import`无法指定候选样式表。因此，给定以下标记：

```CSS
@import url(sheet2.css);
@import url(blueworld.css);
@import url(sheet1.css);
```

这 3 个外部样式表都会被加载，而且它们所有的样式规则都会在文档的显示中使用。  
与`link`一样，你可以通过在样式表 URL 后列出要应用于此样式表的媒体类型，来限定所导入的样式表应用于一种或多种媒体类型。

```CSS
@import url(sheet2.css) all;
@import url(blueworld.css) screen;
@import url(sheet1.css) projection,print;

<link ref="stylesheet" type="text/css" href="sheet1.css" media="projection and print"></link>
```

如果有一个外部样式表，它需要使用其他外部样式表的内容，那么`@improt`就非常有用。**因为外部样式表中不能包含任何文档标记**，所有不能使用`link`元素，但是可以使用`@import`。因此，可以有一个外部样式表包含以下内容：

```CSS
  @import url(http://example.org/library/layout.css);
  @import url(base-text.css);
  @import url(print.css) print;
  body {
    color: red;
  }
  h1 {
    font-size: 19px;
  }
```

也许具体样式并非如此，但是希望你能了解我的意思。注意，上面提供的例子不仅使用了绝对 URL，还使用了相对 URL，正如`link`元素一样，这 2 中 URL 形式都可以使用。

还是要注意：如示例文档所示，`@import`指令出现在样式表的开头。CSS 要求`@import`指令出现在样式表中其他规则之前，如果出现在其他规则之后，将会被用户代理（浏览器）所忽略。

#### 内联样式（Inline Styles）

对于只想给一个独立的元素添加一些样式，而不需要内部/嵌入样式或外部样式。可以使用 HTML 的`style`属性来设置内联样式。

```HTML
<p style="color:gray;">If you love Dog, we are friends!</p>
```

`style`属性可以和任何 HTML 标签相关联，除了那些在`body`之外的标签（比如 `head`,`title`）。  
`style`属性的语法相当普通，实际上，它看起来很像是在`<style>`容器内定义的声明，除了其中的大括号被双引号替代。因此`<p style="color:maroon;background:yellow"></p>`，将把该段落的文本设置为褐红色（maroon），背景设置为黄色。其他文档部分将不会受到该声明的影响。

注意：你只能将声明块而不是整个的样式表放入`style`属性中.因此,你不能将`@import`放入`style`属性中，也不能包含任何完整的规则。`style`属性的值唯一可以放入的是花括号之间的规则。  
一般不建议使用`style`属性。实际上，它不太可能出现在 HTML 以外的 XML 语言中。CSS 许多主要的优点：组织的集中样式的能力，这些集中的样式控制着整个文档的外观，或 web 服务器上所有文档的外观。它们不赞同把样式规则放入`style`属性。在许多方面，内联样式并不比`font`标签好多少，尽管它们在视觉效果方面的应用方面有很到的灵活性。
