<http://learnlayout.com>

# display
---

每个html元素都有display的属性，一般用到的有inline，block, none, inline-block和flex属性。

- 常见的inline元素: <https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elemente>
  - span, a, button, img, input, label, select, textarea

- 常见的block元素: <https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements>
  - div, ul, li, ol
  - html5元素: header, footer, aside, article, section, figure
  - block元素默认从最左到最右，可以通过设置width和margin: 0 auto来使其居中。但是width如果比屏幕大，只会显示一部分内容。所以最好是通过设置max-width来调整。

- none属性不占用空间，与invisible不同

- inline-block

- flex

- display属性可以被重载，比如li常重载inline作menu使用

# the box model
---

* {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}
这样width不会因border和padding而变化

# position
---

- static
  - 默认值
- relative
  - 需要指定其它属性，比如top, left, width
- fixed
  - 相对于viewport而言的位置
  - 移动设备需要额外的代码<http://bradfrostweb.com/blog/mobile/fixed-position/>
- absolute
  - 相对直系positioned元素位置是固定的,无则相对于body
  - 任何非static元素都是positioned元素
  
- 例子
  container: relative
  nav: absolute
  section: static/default
  footer: fixed





