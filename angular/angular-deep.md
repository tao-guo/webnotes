---
layout: default
title: angular internals
---

* TOC
{:toc}

# Flow
- javascript作为脚本语言,实际上实现了编译的功能
- 编译 --> $compile
   - 库的依赖性,Makefile --> DI, $injector
     - 依赖关系成为一颗树
   - 引用 --> $provider
   - html类比于source code, directive类比于expression, function & object
   - jsx更彻底,抛弃了html语法
   - DOM tree
       - Objects
       - Attributes
       - Scope -- 动态集合
         - watch <--> binding
         - diff <--> callback
- Trees
  - DOM trees (including directives)
    - Deep first in AngularJS
  - Dependency trees
  - Scope trees
- 链接
- Router

# Lightweight AngularJS

<http://blog.mgechev.com/2015/03/09/build-learn-your-own-light-lightweight-angularjs/>
