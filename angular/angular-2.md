---
layout: default
title: angular2 notes
---

* TOC
{:toc}

## <http://angular.io>
- 跨平台
  - WEB： progressive web
    - 高性能，离线和不用安装
  - 移动： native mobile
    - Ionic2， NativeScript和React Native
  - 桌面： desktop app
    - Electron-Atom框架 （例如vscode）和原生os的javascript API
- 性能改进
  - 采用新web标准： Web Workers
    - polymer貌似更进了一步，可以直接采用新API和浏览器渲染web components
    - polymer只用来搭组件，好处是未来有可能去掉中间层js，直接发送并import html就可以使用组件
  - 服务器端渲染
    - 提高加载速度，平滑用户体验
    - SEO需要
  - 代码分割
    - Component Router： lazy loading
    - 只加载和渲染需要的代码
  - 新的数据模型： RxJS, immutable.js or another push-model？
- 工具改进
  - 声明式模板获得IDE支持
  - Angular CLI (ng)
  - [], {}, ()， * 奇怪的语法很容易被识别，及时编译和提示错误
  - *vscode + ng2 基本成ng-conf标配
- 完整开发工具链
  - 单元测试和功能测试： Karma和Protractor
    - 貌似现在测试都有自动识别网页图像，像素级别比较来确定网页是否正常？TODO: link
  - 动画支持
  - 辅助支持
    - ARIA, guides, a11y?

## QUICKSTART & TUTORIAL
- Simple hello-world
  - npm: package.json
  - typescript: tsconfig.json, typings.json
  - systemjs: systemjs.config.js
    - webpack/jspm
  - index.html
  - bootstrap()
  - app.component, a.component, b.component ...
- TUTORIAL
  - 完整的流程页面切换图,等同与项目设计草图
  - Single Hero component
    - <input [(ngModel)]="hero.name">
  - Master/detail
    - *ngFor="let hero of heroes"
    - *ngIf="selectedHero"
    - [class.selected]="hero === selectedHero"
    - (click)="onSelect(hero)"
  - Components
    - hero.ts
    - hero-detail.component.ts
      - @Input() hero: Hero;
      - selector: "my-hero-detail"
    - app.component.ts
      - <my-hero-detail [hero]="selectedHero"></my-hero-detail>
      - directives: [HeroDetailComponent]
  - Services
    - @Injectable()
    - providers: [HeroService]
    - ngOnInit
      - 不要在构造函数里面放太多逻辑，比如和服务器通讯的代码
      ```
        export class AppComponent implements OnInit {
            ngOnInit() {
            }
        }
        ```
    - 异步获取数据
      - 采用Promise
      - http服务可以直接返回Observable
   - Routing
     - <base href="/">
     - ROUTER_DIRECTIVES, ROUTER_PROVIDERS
     - RouteConfig
     - [routeLink]="['Dashboard']"
     - 后退和前进
       - window.history.back();
       - this.router.navigate(link);
     - Pipes
     - templateUrl, styleUrls
     - 新Routing的不同之处?

## BASICS
- Architecture
  - Module (library/component)
  - Component
  - Template
  - Metadata
  - Data Binding
  - Directive
  - Service
  - Dependency Injection
  - Others (Forms, Events --> RxJS Observable, HTTP, Pipes, Testing)
- Displaying Data
- User Input
- Forms
  - 三种?
- DI
- Template
- Cheat Sheet
- Style Guide
- Glossary

## DEVELOPER GUIDE
- Attribute Directive
- Component Styles
  - 不影响host
- Hierarchical Injectors
- Http Client
- Lifecycle Hooks
- Npm Package
- Pipes
- Router
- Structural Directives
- Testing
- Typescript Configuration
- Upgrading
- Webpack?
  - 基本配置
  - 两种模式通过两个配置文件实现
  - 介绍了各种好用的插件

## COOKBOOK
- Component Interaction
- Dependency Injection
- Dynamic Form
- Set the Document Title
  - [Title] service
  - getTitle(), setTitle(newTitle: string)
- Typescript to javascript
  - 对比和相互如何引用

## 其它
- 多个components， translute？
- 使用web components
- Debug
  - source.map, chrome
  - Arguru

## API Reference

## UI库
- angular material2