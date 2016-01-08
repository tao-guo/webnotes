---
layout: default
title: angular js
---

* TOC
{:toc}

# <https://docs.angularjs.org/tutorial>
- angular-seed
- step-0: &lt;html ng-app&gt;
- step-2:
  - ng-app="phonecatApp"
  - body ng-controller="PhoneListCtrl"
    - $scope.phones
  - li ng-repeat="phone in phones"
- step-3:
  - ng-model="query"
  - ng-repeat="phone in phones | filter:query"
- step-4:
  - ng-repeat="phone in phones | filter:query | orderBy:orderProp"
  - $scope.orderProp = 'age';
- step-5:
  - $http.get('phones/phones.json')
  - XHR and dependency injection
- step-6: images
- step-7:
  - $route
    - $routeProvider
  - phonecatControllers
    - PhoneListCtrl
    - PhoneDetailCtrl
  - ng-view
- step-8:
  - phone details
- step-9:
  - custom checkmark filter
- step-10: click event
  - ng-click="setImage(img)"
- step-11:
  - $resource
  - services, ngResource
- step-12: animations
  - ngAnimate

# <https://angularjs.org/>
- Why - Alternatives - Extensibility
- The Basics
  - ng-app, angular.min.js, ng-model="yourName", {{yourName}}
- Controller
  - Data Binding -  Controller - Plain JavaScript
  - ng-app, angular.min.js, todo.js,
    ng-controller="TodoListController as todoList",
    ng-click, ng-repeat, ng-model="todo.done",
    class="done-{{todo.done}}", ng-submit="todoList.addTodo()"
- Wire up a Backend
  - Deep Linking - Form Validation - Server Communication
  - angular-resource.min.js, firebase.js, angularfire.min.js, ng-view
  - firebase, $routeProvider
  - ng-class, ng-disabled
- Create Components
  - Directives - Reusable Components - Localization
  - app, tabs, pane
  - .directive (restrict, transclude, scope, controller, template, replace)
- Testability Built-in
  - Injectable
  - Testable
    - Unit Tests: [Karma](https://github.com/karma-runner/karma)
      - test/unit
    - End to End Tests: [Protractor](https://github.com/angular/protractor)
      - test/e2e
    - [Jasmine](http://jasmine.github.io/): Behavior-Driven JavaScript
