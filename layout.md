---
layout: default
title: layout
---

<http://learnlayout.com>

* TOC
{:toc}

# display
---
每个html元素都有display的属性，一般用到的有inline，block, none, inline-block和flex属性。

- inline
  - [常见的inline元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elemente)
    - span, a, button, img, input, label, select, textarea

- block
  - [常见的block元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements)
    - div, ul, li, ol
    - html5元素: header, footer, aside, article, section, figure
  - block元素默认从最左到最右，可以通过设置width和margin: 0 auto来使其居中。但是width如果比屏幕大，只会显示一部分内容。所以最好是通过设置max-width来调整。

- none
  - 属性不占用空间，与invisible不同

- inline-block
  - 元素本身inline，但是以块显示可以有高度和宽度
  - 不需要clearfix
  - 被vertical-align影响，一般设置成top
  - 需要设置width
  - columes之间有空隙?

- [flex](#flexbox)
- display属性可以被重载，比如li常重载inline作menu使用

# the box model
---
- margin, border & padding:
  - [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model): CSS Box Model
- devtools
  - mozilla和chrome的开发窗口很容易[查看](https://hacks.mozilla.org/2014/05/editable-box-model-multiple-selection-sublime-text-keys-much-more-firefox-developer-tools-episode-31/)
- border-box
  - width不会因border和padding而变化
  <pre><code>
  * {
    -webkit-box-sizing: border-box;
       -moz-box-sizing: border-box;
            box-sizing: border-box;
  }
  </code></pre>

# position
---
- static
  - 默认值
- relative
  - 需要指定其它属性，比如top, left, width
  - 元素原来占的空间还在
- fixed
  - 相对于viewport而言的位置
  - 移动设备需要额外的[代码](http://bradfrostweb.com/blog/mobile/fixed-position/)
- absolute
  - 相对直系positioned元素位置是固定的,无则相对于body
  - 任何非static元素都是positioned元素
- Example
  <pre><code>
    container: relative
    nav: absolute
    section: static/default
    footer: fixed
  </code></pre>
- Extra
  - [直观视图](http://nigelbuckner.com/downloads/handouts/web/pos-explained/index.html)
  - [relative与absolute的主要区别](http://developer.51cto.com/art/201009/225201_all.htm)
    - 正常流中的位置存在与否
    - 定位相对层
      - relative定位的层总是相对于其最近的父元素，无论其父元素是何种定位方式
      - absolute定位的层总是相对于其最近的定义为absolute或relative的父层，而这个父层并不一定是其直接父层。如果其父层中都未定义absolute或relative，则其将相对body进行定位
    - margin属性值的定义也符合上述规则

# float & clean
---
- img (inline元素)，额外指定margin
- box设置了float在左的属性, .after-box需要加clear: left
- 如果float的元素比container还大，需要设置overflow: auto
- 所有position可以用float来代替,包括流行的css库比如bootstrap和uikit

# 比例
---
- percent width
- 结合[media queries](https://developer.mozilla.org/en-US/docs/CSS/Media_queries) (min-width, max-width)
  - mobile还可以用[meta viewport](https://dev.opera.com/articles/an-introduction-to-meta-viewport-and-viewport/)

# multi-columes
---
- float
- inline-block
- 本身的column属性(column-count, column-gap)

# flexbox
---
- <http://flexboxfroggy.com/>
- <https://css-tricks.com/snippets/css/a-guide-to-flexbox/>
- <https://philipwalton.github.io/solved-by-flexbox/>

# 补充
---
- z-index
  - <https://developer.mozilla.org/en-US/docs/Web/CSS/z-index>
- 翻面效果
  - 动画可以由css的transform完成
  - ...
- 立体效果
  - overlap
  - ...

# uikit
---
- grid
- align
- flexbox
