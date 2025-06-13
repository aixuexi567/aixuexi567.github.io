---
title: 《CSS权威指南4》- Chapter1.1 - CSS和文档
categories: 技术总结
date: 2019-03-27 14:11:23
tags:
  - CSS权威指南-第四版
  - 翻译
---

## CSS 和文档

层叠样式表（Cascading Style Sheets, CSS）是一个强大的工具，它可以转换文档或一组文档的表现形式。它几乎已经蔓延到 web 的各个角落，并扩展到了表面上非 web 环境中。比如，基于 Gecko 的浏览器使用 CSS 来影响浏览器 chrome 自身的表现，许多 RSS 客户机允许你将 CSS 应用于提要和提要条目，一些即时消息客户端使用 CSS 来格式化聊天窗口。各部分 CSS 也可以在所使用的 JavaScript 框架的语法中找到，甚至在 JavaScript 自身中，它无处不在。

### web 样式简史

在 1994 年 CSS 被首次提及，当时 web 正开始流行起来。那时浏览器给用户提供了各种样式功能（Misaic 中的首选项）。比如，允许用户在每个元素的基础上定义各种各样类型的字体、颜色和大小，而这些功能都没有提供给文档的作者，他们所能做的就是将一段内容标记为段落、某个级别的标题、预定义格式化文本，或其他几种类型元素中的一种。如果一个用给户浏览器配置为 所有 1 级标题都是小的，粉红色的，而所有的 6 级标题都是大的，红色的，那么，就会如他所愿。

CSS 就是在这种环境中被引入的。它的目的是提供一种简单的声明式样式语言，对作者来说是灵活的，更重要的是，为作者和用户都提供了样式功能。通过层叠（cascade）这种方式，样式能被组合在一起，并按优先级顺序排列，因此作者和读者都有发言权，尽管读者总是拥有最后发言权。

工作进展迅速，在 1995 年底，CSS1 完成。而新成立的 CSS 工作组将继续推进 CSS2，但是浏览器难以以一种互操作的方式实现 CSS1 特性。虽然每个独立的 CSS 部分是相当简单，但组合它们却导致了令人惊讶的复杂行为。在早期的实现中也有些不幸的失误，比如在盒子模型实现中臭名昭著的差异。这些问题威胁着 CSS 的发展，幸运的是，一些明智的建议得到了实现，浏览器厂商也趋于和谐统一。在这几年里，由于增强了互操作性和引人注目的开发，比如基于 CSS 改版的《连线》杂志和《CSS 禅意花园》，CSS 开始流行起来。

r 然而，在这以前发生前，CSS 工作组已经在 1998 年初确定了 CSS2 的规范。CSS2 一旦完成，会立即开始 CSS3 的工作，和一个 CSS2 的澄清版本叫做 CSS2.1。
本着时代精神，CSS3 被构建成一系列（理论上）的独立模块，而不是一个单一的整体规范。这种方式反映了当时的情况（例如活跃的 XHTML 规范），出于相同的原因，CSS3 也被分成多个模块。

模块化 CSS3 的基本原理是，每个模块可以按照自己的速度工作，特别重要（流行的）模块可按照 W3C 的进度推进，而不用被其他阻碍。事实上，结果就是如此。截止到 2012 年初，3 个 CSS3（以及 CSS1 和 CSS2.1）模块已经达到完全推荐状态，它们是 3 级的 CSS 样色，CSS 命名空间，3 级的选择器。与此同时，7 个模块处于候选推荐模块，还有十几个在工作草案阶段。在旧的方法下，颜色、选择器、命名空间必须等到规范的其他部分完成或删除之后才能成为完整规范的一部分，多亏有了模块化，它们不必等待。

这种优势的另一个好处，它很难说成是一个单独的“CSS3 规范”，是没有这样说法的，也不可能有。即使每一个 CSS 模块都达到 3 级（说是在 2016 年底），但是已经有 4 级的选择器在处理中了。我们会把它称为 CSS4 吗？所有的 CSS3 孩子发挥作用吗？网格布局甚至还没有达到 1 级？

因此我们不能指着一本大册的书说它是 CSS3，我们可以根据引入的模块名来讨论起特性。灵活性的模块可以弥补它们有时在语义上造成的尴尬。如果你想要一个近于单一完整的规范，CSS 工作组每年会发布一个快照文档。

有了这些，我们就差不多可以开始学习 CSS。不过，首先我们要复习一下标签。

### 元素

元素（Element）是文档结构的基础。在 HTML 中，最常用的元素是很容易辨别的，如`p`,`table`,`span`,`a`,`div`.文档中的每一个元素都对其表现起一定的作用。

### 替换和非替换元素

虽然 CSS 依赖于元素，但并不是所有元素都以同样的方式创建。例如，图像和段落就不是同一类型的元素，`span`和`div`也不相同。在 CSS 中，元素通常有 2 种形式：替换和非替换。

#### 替换元素

替换元素是指用来替换元素内容的部分并不是由文档内容直接呈现。可能最熟悉的替换元素的例子就是`img`标签，它由文档本身之外的一个图像文件来替换。实际上，`img`没有实际的内容，通过以下简单例子可以了解这一点：

```
  <img src="dog.gif">
```

这个标记片段只包含一个元素名和一个属性。除非将其指向外部内容（这里，就是由 src 属性指定的一个图像），否则这个元素不显示任何内容。如果你指向一个有效的图像文件，图像将被放置到文档中，如果没有，它什么也不显示或浏览器显示一个"损坏的图像"的占位符。`input`元素与之类似，它取决于`input`元素的类型，是由一个单选钮，复选框，还是由一个文本输入框替换元素内容。

#### 非替换元素

大部分 HTML 元素是非替换元素。这意味着，它们的内容由用户代理（通常是一个浏览器）在元素自身生成的框中（盒子内部）显示。例如，`<span>hi there</span>`就是一个非替换元素，文本`"hi there"`将有用户代理显示。段落、标题、单元格、列表，以及 HTML 中几乎所有的元素也是这种情况。

### 元素显示角色

除了替换元素和非替换元素，CSS 也使用其他两中基本元素类型：块级元素（block-level）和行内级元素/行内元素（inline-level）。还有更多的显示类型，但是这些事最基本的，也是大多数其他类型所引用的类型.对于花时间研究 HTML 标记以及其在浏览器的显示的作者来说，块级和行内类型是很熟悉的。这些元素如图 **Figure 1-1** 所示。

![Figure1-1](/image/Figure1-1.png)

#### 块级元素

块级元素生成一个元素框，（默认下）它会填充其父元素的内容区域，并且在它的两侧不能有其他元素。换句话说，它在元素框前后生成“分隔符”。最熟悉的来自 HTML 块级元素是`p`和`div`,替换元素可以是块级元素，当通常它们都不是。  
列表项是块级元素的一种特殊情况。除了表现方式与其他块元素一致，列表项还会生成一个标记符———无序列表通常是一个圆点，有序列表则是一个数字，标记符会附加到元素框中。除了这个标记符外，列表项在所有其其他方面都与其他块元素相同。

#### 行内元素

行内元素在一个文本行内生成元素框，也不会打断这行文本。行内元素最好的例子就是 HTML 中的`a`元素，还有`strong`和`em`。这些元素不会在它自身之前或之后生成“分隔符”，所以它们可以出现在另一个元素的内容中，而不会破坏其显示。

注意，虽然“块”和“行内”的名称与 HTML 中的块级和行内级元素有很多共同之处，但也存在一个重要的差别。在 HTML 中，块级元素不能继承行内元素（即不能嵌套在行内元素中），但在 CSS 中，对于显示角色如何嵌套不存在任何的限制。

要了解这是任何工作的，我们来考虑一个 CSS 属性：**display**

```
  Values:   [<display-outside> || <display-inside>] | display-listiem |
            display-internal | display-box | display-legacy

  Definitions:    See below

  Initial value:  inline

  Applies to:     All elements

  Computed value: As specified

  Inherited:      No

  Animatable:     No

  <display-out>: block | inline | run-in
  <display-inside>: flow | flow-root | table | flex | grid | ruby
  <dispay-listitem>: list-item && <display-outside>? && [flow | flow-root]?
  <display-internal>: table-row-group | table-header-group | table-footer-group |
                      table-row | table-cell | table-column-group | table-column |
                      table-caption | ruby-base| ruby-text | ruby-base-container |
                      ruby-text-container

  <display-box>: contents | none
  <display-legacy>: inline-block | inline-list-item | inline-table |
                    inline- flex | inline-grid
```

你也许注意到`display`有很多的值，其中只有`block`,`inline`,`list-item`3 个是我们经常提及到。大部分的值将在这本书的其他部分讨论，例如，`grid`和`inline-grid`将在关于网格布局的单独一章中讨论，表格相关的值，在 CSS 表格布局单独一章讨论。  
目前，我们先只关注`block`和`inline`。

```HTML
  <body>
    <p>This ia paragraph with <em>an inline element</em> within it</p>
  </body>
```

这里有 2 个块元素（body，p）和一个行内元素（em）.根据 HTML 的规范，`em`可以从`p`继承而来，但反过来不行。通常，HTML 层次结构是有效的，所有行内元素可以继承自块元素，而反之则不允许。  
与之不同，CSS 没有这种限制。你可以让标记保持原样，但改变 2 个元素的显示角色。

```CSS
  p {
    display: inline;
  }
  em {
    display: block;
  }
```

这会使得元素在一个行内框中生成一个块框，这是完全合法的，不违反任何 CSS 规范、然而，如果你试图反转元素的嵌套关系，就会遇到问题：

```HTML
<em><p>This is a paragraph improperly enclosed by an inline element .</p></em>
```

不论你通过 CSS 对显示角色做什么改变，ta 在 HTML 中是不合法的。

虽然在 HTML 文档中更改元素的显示角色很有用，但对 XML 来讲，也变的意义重大。XML 文档不太可能有固有的显示角色，所有它要由创作者来定义。例如，你可能想知道如何摆放以下 XML 片段：

```HTML
  <book>
    <maintitle>Casading Style Sheets: The Dfinitive Guide</maintitle>
    <subtitle>Third Edittion</subtitle>
    <author>Eric A. Meyer</author>
    <publisher>O'Reilly and Associates</publisher>
    <pubdate>November 2006</pubdate>
    <isbn type="print">978-0-596-52733-4</isbn>
  </book>

    <book>
    <maintitle>Casading Style Sheets: The Dfinitive Guide</maintitle>
    <subtitle>Third Edittion</subtitle>
    <author>Eric A. Meyer</author>
    <publisher>O'Reilly and Associates</publisher>
    <pubdate>November 2006</pubdate>
    <isbn type="print">978-0-596-52733-4</isbn>
  </book>
```

由于`display`属性的默认值是`inline`,默认地其内容将被渲染成行内文本，如图**Figure 1-2** 所示，这种显示用处不大。

![Figure1-2](/image/Figure1-2.png)

你可以使用`display`来定义基本布局：

```CSS
  book, maintitle, subtitle,author,isbn {
    display: block;
  }
  publisher,pubdate {
    display: inline;
  }
```

我们现在将 7 个元素中的 5 个设置为块元素，2 个设置成行内元素。这意味着每个块元素都会像 HTML 中的`div`一样处理，而 2 个行内元素将以类似于`span`的方式来处理。
这种影响显示角色的基本功能使得 CSS 在各种情况下都非常有用。我们可以将以上规则作为起点，再另外增加一些样式以获得更好的视觉冲击，得到如图 **Figure 1-3**的结果。  
![Figure 1-3](/image/Figure1-3.png)
