---
layout: default
title: angular js learning notes
---

* TOC
{:toc}

# Why Angular JS
- Web技术的演化:
  1. 服务器端开发为主
     - 纯html, 无任何代码
     - javascript诞生,处理client输入
     - Dom文档结构, javascript可以动态修改
     - Ajax, jquery: 前端从服务器取html部件和数据,插入到DOM
     - PHP, JSP, Django, ROR等等，都是modified html with template ability， 后端生成动态网页
  2. 客户端开发为主
     - html5的标准化
     - 客户端载入bootstrap的js
     - js控制浏览器加载框架, 模板，应用代码 --- 类似applet和flex控件加载
     - 客户端js控制整个页面的渲染和最终显示，用户输入监听和后台数据的发送获取(json格式)
     - 客户端可以控制路由，服务器端只负责数据的处理
     - 各种客户端MVC纷纷涌现

- Angular JS的特点
  - 扩展了html语义
    1. ng-* ...
       - uikit其实也有类似的用法, data-uk-xxx，可以很方面扩展html进行页面设计
       - data-* 是html5支持的格式
       - Angular支持进一步扩展，directive A
    2. 可以嵌入expression
       - \{\{obj.xxx.yyy\}\}, \{\{expression | filter\}\}
       - jekyll也有类似，模板语言一般都支持这种范式
    3. 直接自定义组件
       - ionic: <ion-tabs ...></ion-tabs>
       - 在Angular JS框架里，改名叫做directive, E
       - 向未来web components标准靠拢
  - 双向绑定：ng-model=x {{x}},监听所有model，性能有代价, watcher <= 2000
    - 在2.0里面有改进，变为非强制
  - 其他必要功能
    - 支持路由
    - 支持数据传输 
      - 为何要单独强调？还取个Deep Linking？？？ 相比其他js框架有特别？？
  - 测试框架比较全 
    - 有人诟病并不是那么有用？
  - 相比如reactjs
    - 所有都用js生成(jsx render())
      - java时代的界面开发，layout，大小这些都在代码里面定义
      - 但是render函数提供了一部分的描述功能, mixed html with js
    - 优缺点--口水仗

- Controller
  - 其实并不是真正的C, 在1.x里面，一般是model和methods的集合:
    - ng-controller = TodoListController as todoList
      - todoList.todos.length
      - todoList.archive()
  - 在2.0里面，去掉了ng-controller，移到了directive里面
    - Directive E
      - html模板
      - data model
      - control --> methods
  - 单独的Controller是否有意义？

- Ugly的语法和术语
  - 很多人提到了Aurelia: aurelia.io, 非常干净
  - 创造太多不必要的术语
  - 2.0的语法更ugly `, *...
  - <http://oredev.org/2014/sessions/angularjs-2-0>
    - 里面提到了why -- web components
    - Also why aurelia ?

- DI
  - 需要在框架特别强调吗？
  - 类似Makefie，可以裸写，也可以用cmake更智能的语法，但核心没太大区别

