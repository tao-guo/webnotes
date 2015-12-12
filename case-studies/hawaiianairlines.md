---
layout: default
title: waiianairlines case study
---

<http://www.smashingmagazine.com/2015/12/lessons-learned-in-big-app-development-hawaiian-airlines>

* TOC
{:toc}

## UX Design Stage
- By other companies: <http://www.nurun.com/>
- 500+ pages wireframe document
- annotated Photoshop mockups
- ront-end style guide
- images --> tech
  - SVG, html5 canvas for dynamic beautiful content
  - animating (progress bar, icon)
  - fetch data using AJAX calls, readjust height

## The Front-End Sandbox
- backend complicated (40:1) and not ready yet
- static JSON --> dynamic template -- Angular JS
  - dealing with multiple data stages
    - data inspector (PouchDB --> CouchDB) (mongoDB & miniMongo ??)

## Integration (TOSSING CODE OVER THE FENCE)
- Backend using .NET env
- Source code RCS (Team Foundation Server vs. git)
- VPN to match back-end team with months
- SCRUM process every two-week
- Learnings:
  - git branching strategy at the beginning
  - unify front end and back end
  - unify working environments
  - tools, workflows early on

## CSS And LESS
- Used :extend in LESS files extensively at first to reduce the changes for HTML
- CSS bloated, changed to mixins
- _Should_ use [OOCSS](http://www.smashingmagazine.com/2011/12/an-introduction-to-object-oriented-css-oocss/)
  and [BEM](https://css-tricks.com/bem-101/)
  - Use SASS and some frameworks (uikit, bootstrap) ?
  - Use linter [grunt plugin](https://github.com/jgable/grunt-lesslint)
  - Use flat and reusable classes, keep refactoring

## AngularJS
- [paradigm shift](http://stackoverflow.com/questions/14994391/thinking-in-angularjs-if-i-have-a-jquery-background)
- Good for custom UI components
- Think about AngularJS directives more as self-contained web components with an API
- Performance
  - data-binds with cost
  - [tool to monitor AngularJS performance](https://github.com/blndspt/ngPerformance)
  - [AngularJS performance data on the live website!](https://www.hawaiianairlines.com/?performance=true)
  - [bind-once](https://github.com/Pasvaz/bindonce)
- Learnings:
  - Make sure you understand the inner workings of your chosen framework
  - AngularJS good for reusable UI
  - Think of directives as web components
    - expose an API into them
    - keep scope isolated from the outside world

## Custom Form Controls
- most painful thing
  - accessibility
  - focus, blur, active, inactive states
  - Tab cycle
  - dropdown menus based on the page’s scroll position
  - type up to several letters to jump to an item in a dropdown menu
  - auto-scroll dropdown menu items for long lists
- Appoach
  - OCD-level control at first
  - native counterparts later: [codepen](http://codepen.io/inorganik/pen/QjqzwG)
- Learnings:
  - Use native form
  - Try using background images, SVGs and pseudo-selectors

## Pattern Consistency
- consistency for learning curves and productivity
- UI Docs
  - Yeoman, built and maintained itself -- jekyll
- Code Reviews
  - not merge until at least one team member had reviewed and approved it.
- guidelines
  - [JSHint](http://jshint.com/) not enough
  - [airbnb detailed guidelines](https://github.com/airbnb/javascript),
    [google js](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml),
    [github css](http://primercss.io/),
    [google CSS & HTML](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)
  - enforce as much as possible via Grunt tasks
- Learnings:
  - pattern needs to be across the entire code base
  - Code reviews good
  - UI documentation good
    - [living style guide](http://www.smashingmagazine.com/2015/04/an-in-depth-overview-of-living-style-guide-tools/) better
    - [KSS tool](http://warpspire.com/kss/styleguides/)
  - Enforce detailed guides
  - Use Grunt or Gulp tools

## Conclusion
- 回望总结很重要
- 从错误中学习
- 为自己的作品自豪
