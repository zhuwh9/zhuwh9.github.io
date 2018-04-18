---
layout: post
title: AngularJS笔记1
date: 2018-04-18 13:56:00+00:00
categories: 日志
tags: 博客
---

# ng-app

`ng-app`属性：

```html
<html ng-app>
    
</html>
```

`ng-app`属性表示一个AngularJS指令，这个指令是用来标记HTML元素作为应用的根元素，因此可以选择是否将整个HTML页面或只有部分HTML元素作为AngularJS



# {{ expression }}

位于双花括号内的表达式`expression`会被执行然后插入DOM中



# View and Template

```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="app.js"></script>
</head>
<body ng-controller="PhoneListController">

  <ul>
    <li ng-repeat="phone in phones">
      <span>{{phone.name}}</span>
      <p>{{phone.snippet}}</p>
    </li>
  </ul>

</body>
</html>
```

- `li` 上的`ng-repeat="phone in phones"`是AngularJS的重复指令，它告诉AngularJS根据`phones`列表里的每个`phone`的属性进行`<li>`元素的创建


- 之后的元素`{{phone.name}}`和`{{phone.snippet}}` 会被求值并替换DOM文本
- 外层`body`标签处还有一个`ng-controller`指令（名叫`PhoneListController` ，它对绑定的`body`元素内部的DOM子树负责，上述`{{phone.name}}` 和`{{phone.snippet}}`的数据绑定都是在这个指令控制器中创建的。
- `ng-app="phonecatApp"`是模块的名字，这个模块包含着`PhoneListController`

# Model and Controller

数据（**Model**）是在`PhoneListController`这个**controller**里初始化的，而**controller**是一个接收*`$scope`*参数的构造函数。

- `var phonecatApp = angular.module('phonecatApp', []);`定义了一个叫"phonecatApp"的模块
- `angular.module`函数返回模块实例给变量`phonecatApp`

定义控制器后，实现`model`和`view`之间的绑定的过程：

- `<body>` 标签中的`ngController`指令，是引用`app.js`中的名叫`PhoneListController`的`controller`。
- `PhoneListController`将`phone`的数据绑定在`$scope`中，而`Controller`的`$scope` 是当应用被定义时，根作用域`Root Scope`的原型后代。
- **重要的是**，`body`标签内部所有的数据绑定都可以访问这个`PhoneListController`的`$scope`。

```javascript
// Define the `phonecatApp` module
var phonecatApp = angular.module('phonecatApp', []);

// Define the `PhoneListController` controller on the `phonecatApp` module
phonecatApp.controller('PhoneListController', function PhoneListController($scope) {
  $scope.phones = [
    {
      name: 'Nexus S',
      snippet: 'Fast just got faster with Nexus S.'
    }, {
      name: 'Motorola XOOM™ with Wi-Fi',
      snippet: 'The Next, Next Generation tablet.'
    }, {
      name: 'MOTOROLA XOOM™',
      snippet: 'The Next, Next Generation tablet.'
    }
  ];
})
```

# Testing global Controllers

如果`controller`在全局命名空间中，可以通过一个`mock`作用域对象来实例化这个`controller`。

- 利用describe, it, expect，可以快速简单地进行单元测试

```javascript
describe('PhoneListController', function() {

  it('should create a `phones` model with 3 phones', function() {
    var scope = {};
    var ctrl = new PhoneListController(scope);

    expect(scope.phones.length).toBe(3);
  });

});
```

# Testing non-global Controllers

如上所述，我们通过`phonecatApp`这个模块实例的构造函数注册了一个`PhoneListController`控制器。

- AngularJS为非全局控制器提供一种服务：`$controller`。它会通过控制器的名字来搜索控制器并返回示例

```javascript
describe('PhoneListController', function() {
// 每次测试之前先加载phonecatApp模块
  beforeEach(module('phonecatApp'));
// 要求AngularJS来inject这个$controller服务到测试函数中
  it('should create a `phones` model with 3 phones', inject(function($controller) {
    var scope = {};
    // 使用$controller方法，创建PhoneListController实例，并获取controller的作用域赋给局部变量
    var ctrl = $controller('PhoneListController', {$scope: scope});

    expect(scope.phones.length).toBe(3);
  }));

});
```



# Component

> - `$ctrl`是`controller`的别名
> - 可以通过在DOM中插入 `<phone-list></phone-list>`来复用代码

**`app/index.html`**:

```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="app.js"></script>
  <script src="phone-list.component.js"></script>
</head>
<body>

  <!-- Use a custom component to render a list of phones -->
  <phone-list></phone-list>

</body>
</html>
```

**`app/app.js`**:

```javascript
// Define the `phonecatApp` module
angular.module('phonecatApp', []);
```

**`app/phone-list.component.js`**:

```javascript
// Register `phoneList` component, along with its associated controller and template
angular.
  module('phonecatApp').
  component('phoneList', {
    template:
        '<ul>' +
          '<li ng-repeat="phone in $ctrl.phones">' +
            '<span>{{phone.name}}</span>' +
            '<p>{{phone.snippet}}</p>' +
          '</li>' +
        '</ul>',
    controller: function PhoneListController() {
      this.phones = [
        {
          name: 'Nexus S',
          snippet: 'Fast just got faster with Nexus S.'
        }, {
          name: 'Motorola XOOM™ with Wi-Fi',
          snippet: 'The Next, Next Generation tablet.'
        }, {
          name: 'MOTOROLA XOOM™',
          snippet: 'The Next, Next Generation tablet.'
        }
      ];
    }
  });
```

## Testing

> - 为了找到并实例化一个component的controller，AngularJS提供`$componentController`服务

**`app/phone-list.component.spec.js`**:

```javascript
describe('phoneList', function() {

  // Load the module that contains the `phoneList` component before each test
  beforeEach(module('phonecatApp'));

  // Test the controller
  describe('PhoneListController', function() {

    it('should create a `phones` model with 3 phones', inject(function($componentController) {
      var ctrl = $componentController('phoneList');

      expect(ctrl.phones.length).toBe(3);
    }));

  });

});
```

### Running Test

通过执行`npm start`来运行测试。

# 代码文件组织形式

> One Feature per file：（不推荐）
>
> - 所有controllers放在同一个文件
> - 所有components放在另一个文件
> - 所有services放在第三个文件
>
> Each feature/entity in its own file：（容易导致一大堆文件）
>
> - 所有单独的controller放在它自己的文件中
> - 所有单独的component在它自己的文件中定义

## 如何解决文件过多的问题

> - 将归属于某个特征的文件放在这个特征为名的文件夹下，如将`phone-list.component.js`和`phone-list.component.spec.js`放在`/app/phone-list/`下
> - 对于在应用不同部分都有使用到的特定特征，可以将它放在`/app/core/`中

`/`：（文件结构）

```
app/
  phone-list/
    phone-list.module.js
    phone-list.component.js
    phone-list.component.spec.js
  app.module.js
```

**`app/phone-list/phone-list.module.js`**：

```javascript
// Define the `phoneList` module
angular.module('phoneList', []);
```

**`app/phone-list/phone-list.component.js`**：

```javascript
// Register the `phoneList` component on the `phoneList` module,
angular.
  module('phoneList').
  component('phoneList', {...});
```

**`app/app.module.js`**：

- 通过在定义`phonecatApp`时传递`phoneList`，AngularJS会让所有注册在`phoneList`上的`entities`在`phonecatApp`上吔可用

```javascript
// Define the `phonecatApp` module
angular.module('phonecatApp', [
  // ...which depends on the `phoneList` module
  'phoneList'
]);
```

# External Templates

`app/phone-list/phone-list.component.js`：

```javascript
angular.
	module('phoneList').
	component('phoneList', {
  	// Note: The URL is relative to our `index.html` file
  	templateUrl: 'phone-list/phone-list.template.html',
  	controller: ...
	});
```

## CDO

> CDO是指component definition object

# Filtering Repeaters

**`app/phone-list/phone-list.template.html`**：

```html
<div class="container-fluid">
  <div class="row">
    <div class="col-md-2">
      <!--Sidebar content-->

      Search: <input ng-model="$ctrl.query" />

    </div>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query">
          <span>{{phone.name}}</span>
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>
```

![](G:\学习资料\Web\Angular\tutorial_05.png)

# Two-way Data Binding

**`app/phone-list/phone-list.template.html`**：

```html
<div class="container-fluid">
  <div class="row">
    <div class="col-md-2">
      <!--Sidebar content-->

      <p>
        Search:
        <input ng-model="$ctrl.query">
      </p>

      <p>
        Sort by:
        <select ng-model="$ctrl.orderProp">
          <option value="name">Alphabetical</option>
          <option value="age">Newest</option>
        </select>
      </p>

    </div>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query | orderBy:$ctrl.orderProp">
          <span>{{phone.name}}</span>
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>
```

## Component Controller

**`app/phone-list/phone-list.component.js`**：

```javascript
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: function PhoneListController() {
      // 每个phone都添加了name和age值
      this.phones = [
        {
          name: 'Nexus S',
          snippet: 'Fast just got faster with Nexus S.',
          age: 1
        }, {
          name: 'Motorola XOOM™ with Wi-Fi',
          snippet: 'The Next, Next Generation tablet.',
          age: 2
        }, {
          name: 'MOTOROLA XOOM™',
          snippet: 'The Next, Next Generation tablet.',
          age: 3
        }
      ];
	  // 设置默认的orderBy值
      this.orderProp = 'age';
    }
  });
```

# XHR & Dependency Injection

> Dependency Injection（DI）：依赖注入
>
> - 利用AngularJS内置的服务`$http`，从服务端获取数据

**`app/phone-list/phone-list.component.js`**：

```javascript
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: function PhoneListController($http) {
      var self = this;
      self.orderProp = 'age';

      $http.get('phones/phones.json').then(function(response) {
        self.phones = response.data;
      });
    }
  });
```

- `$http`服务返回一个`promise`对象，并调用`then`方法来处理异步返回的结果
- 局部变量`self`指向`controller`实例本身

## 避免用$前缀来命名自定义的服务或模块

- AngularJS本身的API和服务大多数以`$`为前缀，因此为了避免冲突，最好不要用`$`命名自定义的服务或模块

## Minification

> - 使用`$inject`属性解决代码压缩时的问题

```javascript
function PhoneListController($http) {...}
PhoneListController.$inject = ['$http'];
...
.component('phoneList', {..., controller: PhoneListController});
```

> - 使用行内声明

```javascript
function PhoneListController($http) {...}
...
.component('phoneList', {..., controller: ['$http', PhoneListController]});
```

**`app/phone-list/phone-list.component.js`**：

```javascript
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: ['$http',
      function PhoneListController($http) {
        var self = this;
        self.orderProp = 'age';

        $http.get('phones/phones.json').then(function(response) {
          self.phones = response.data;
        });
      }
    ]
  });
```

## Testing

> `$httpBackend`：可以利用这个服务来模拟服务器返回响应

**`app/phone-list/phone-list.component.spec.js`**：

- 使用`inject()`来注入`$componentController`和`_$httpBackend_`的实例
- 使用`$httpBackend.expectGet()`方法来告诉`_$httpBackend_`，即将有一个HTTP请求并准备返回响应
- 调用`$httpBackend.flush()`后，才会返回响应。

```javascript
describe('phoneList', function() {
  beforeEach(module('phoneList'));
  describe('controller', function() {
    var $httpBackend, ctrl;
    // The injector ignores leading and trailing underscores here (i.e. _$httpBackend_).
    // This allows us to inject a service and assign it to a variable with the same name
    // as the service while avoiding a name conflict.
    beforeEach(inject(function($componentController, _$httpBackend_) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json')
                  .respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);//返回测试值
      ctrl = $componentController('phoneList');
    }));
    it('should create a `phones` property with 2 phones fetched with `$http`', function() {
  		expect(ctrl.phones).toBeUndefined();
		$httpBackend.flush();
  		expect(ctrl.phones).toEqual([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
	});
    it('should set a default value for the `orderProp` property', function() {
  		expect(ctrl.orderProp).toBe('age');
	});
  });
});
```

# Templating Links & Images

**`app/phone-list/phone-list.template.html`**：

```html
<ul class="phones">
  <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query | orderBy:$ctrl.orderProp" class="thumbnail">
    <a href="#/phones/{{phone.id}}" class="thumb">
      <img ng-src="{{phone.imageUrl}}" alt="{{phone.name}}" />
    </a>
    <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>
```

## Testing

**`e2e-tests/scenarios.js`**：

```javascript
it('should render phone specific links', function() {
  var query = element(by.model('$ctrl.query'));
  query.sendKeys('nexus');

  element.all(by.css('.phones li a')).first().click();
  expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
});
```

# ng-route

> 应用的路由是通过`$routeProvider`声明的，`$routeProvider`是`$route`服务的提供者。



# DI，Injector and Providers

> 依赖注入（DI）是AngularJS的核心：
>
> 当应用启动时，AngularJS创建一个注入器（Injectors），注入器用于寻找并注入所有应用所需的服务。
>
> 但，注入器本身并不知道`$http`和`$route`服务做了什么，甚至不知道这两个服务的存在。
>
> 注入器只执行如下步骤：
>
> - 加载应用中声明的模块定义
> - 注册所有在模块中定义所有Providers
> - 当被要求做时，通过可注入函数（Providers）中的参数以懒加载服务及它们的依赖

## Providers

> Providers是那些能创建服务实例，并暴露（用于控制一个服务创建和运行时的行为的）配置API的对象。对于服务`$route`，`$routeProvider`暴露了允许你去定义应用路由信息的API，所以`$routeProvider`就是`$route`的Provider。

**注**：Providers只能被注入`config`函数中，在运行时不能将`$routeProvider`注入到`PhoneListController`中。



# Template

> `$route`服务通常与`ngView`指令一同使用。
>
> - `ngView`指令的作用是为当前路由引入视图模板

**`app/index.html`**：

```html
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="bower_components/angular-route/angular-route.js"></script>
  <script src="app.module.js"></script>
  <script src="app.config.js"></script>
  ...
  <script src="phone-detail/phone-detail.module.js"></script>
  <script src="phone-detail/phone-detail.component.js"></script>
</head>
<body>

  <div ng-view></div>

</body>
```

## Configuring a Module 

> 模块`module`的`.config()`方法用于配置可用的`providers`

**`app/app.module.js`**：

- 为了令在`ngRoute`中定义的`providers`、`services`和`directives`在整个应用中可用，应当将`ngRoute`在`phonecatApp`模块中作为依赖添加

```javascript
angular.module('phonecatApp', [
    'ngRoute',
    ...
]);
```

**`app/app.config.js`**：

```javascript
angular.
  module('phonecatApp').
  config(['$locationProvider', '$routeProvider',
    function config($locationProvider, $routeProvider) {
      $locationProvider.hashPrefix('!');

      $routeProvider.
        when('/phones', {
          template: '<phone-list></phone-list>'
        }).
        when('/phones/:phoneId', {
          template: '<phone-detail></phone-detail>'
        }).
        otherwise('/phones');
    }
  ]);
```

**`app/phone-detail/phone-detail.module.js`**：

```javascript
angular.module('phoneDetail', [
    'ngRoute'// 相当于引入依赖，后面的component.js才可以用$routeParams
]);
```

**`app/phone-detail/phone-detail.component.js`**：

```javascript
angular.
  module('phoneDetail').
  component('phoneDetail', {
    template: 'TBD: Detail view for <span>{{$ctrl.phoneId}}</span>',
    controller: ['$routeParams',
      function PhoneDetailController($routeParams) {
        this.phoneId = $routeParams.phoneId;
      }
    ]
  });
```

**`app/app.module.js`**：

```javascript
angular.module('phonecatApp', [
  ...
  'phoneDetail',
  ...
]);
```

# Custom Filters

**`app/app.module.js`**：

```javascript
angular.module('phonecatApp', [
  ...
  'core',
  ...
]);
```

**`app/core/core.module.js`**：

```javascript
angular.module('core', []);
```

`app/core/checkmark/checkmark.filter.js`：

```javascript
angular.
  module('core').
  filter('checkmark', function() {
    return function(input) {
      return input ? '\u2713' : '\u2718';
    };
  });
```

# Filters Syntax

```
{{expression | filter}}
```

**`app/phone-detail/phone-detail.template.html`**：

```html
<dl>
  <dt>Infrared</dt>
  <dd>{{$ctrl.phone.connectivity.infrared | checkmark}}</dd>
  <dt>GPS</dt>
  <dd>{{$ctrl.phone.connectivity.gps | checkmark}}</dd>
</dl>
```

## Testing

```javascript
describe('checkmark', function() {

  beforeEach(module('core'));

  it('should convert boolean values to unicode checkmark or cross',
    inject(function(checkmarkFilter) {
      expect(checkmarkFilter(true)).toBe('\u2713');
      expect(checkmarkFilter(false)).toBe('\u2718');
    })
  );

});
```

# Event Handler

**`app/phone-detail/phone-detail.component.js`**:

```javascript
...
controller: ['$http', '$routeParams',
  function PhoneDetailController($http, $routeParams) {
    var self = this;

    self.setImage = function setImage(imageUrl) {
      self.mainImageUrl = imageUrl;
    };

    $http.get('phones/' + $routeParams.phoneId + '.json').then(function(response) {
      self.phone = response.data;
      self.setImage(self.phone.images[0]);
    });
  }
]
...
```

## Component Template

**`app/phone-detail/phone-detail.template.html`**：

```html
<img ng-src="{{$ctrl.mainImageUrl}}" class="phone" />
...
<ul class="phone-thumbs">
  <li ng-repeat="img in $ctrl.phone.images">
    <img ng-src="{{img}}" ng-click="$ctrl.setImage(img)" />
  </li>
</ul>
...
```

## Testing

```javascript
...

describe('View: Phone detail', function() {

  ...

  it('should display the first phone image as the main phone image', function() {
    var mainImage = element(by.css('img.phone'));

    expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
  });

  it('should swap the main image when clicking on a thumbnail image', function() {
    var mainImage = element(by.css('img.phone'));
    var thumbnails = element.all(by.css('.phone-thumbs img'));

    thumbnails.get(2).click();
    expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

    thumbnails.get(0).click();
    expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
  });

});

...
```

**`app/phone-detail/phone-detail.component.spec.js`**：

```javascript
...

describe('controller', function() {
  var $httpBackend, ctrl
  var xyzPhoneData = {
    name: 'phone xyz',
    images: ['image/url1.png', 'image/url2.png']
  };

  beforeEach(inject(function($componentController, _$httpBackend_, _$routeParams_) {
    $httpBackend = _$httpBackend_;
    $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData);

    ...
  }));

  it('should fetch phone details', function() {
    expect(ctrl.phone).toBeUndefined();

    $httpBackend.flush();
    expect(ctrl.phone).toEqual(xyzPhoneData);
  });

});

...
```

# REST and Custom Services

> `ngResource`提供了AngularJS中的RESTful功能

`app/core/phone/phone.module.js`：

```javascript
angular.module('core.phone', ['ngResource']);
```

`app/core/phone/phone.service.js`：

> `factory`函数类似于`controller`构造函数

```javascript
angular.
  module('core.phone').
  factory('Phone', ['$resource',
    function($resource) {
      return $resource('phones/:phoneId.json', {}, {
        query: {
          method: 'GET',
          params: {phoneId: 'phones'},
          isArray: true
        }
      });
    }
  ]);
```

## Component Controllers

**app/phone-list/phone-list.module.js:**

```javascript
angular.module('phoneList', ['core.phone']);
```

**app/phone-list/phone-list.component.js:**

```javascript
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: ['Phone',
      function PhoneListController(Phone) {
        this.phones = Phone.query();
        /* 替换了
        $http.get('phones/phones.json').then(
          function (responese) {
              self.phones = response.data;
          });
        */
        this.orderProp = 'age';
      }
    ]
  });
```

**app/phone-detail/phone-detail.module.js:**

```javascript
angular.module('phoneDetail', [
  'ngRoute',
  'core.phone'
]);
```

**app/phone-detail/phone-detail.component.js:**

```javascript
angular.
  module('phoneDetail').
  component('phoneDetail', {
    templateUrl: 'phone-detail/phone-detail.template.html',
    controller: ['$routeParams', 'Phone',
      function PhoneDetailController($routeParams, Phone) {
        var self = this;
        self.phone = Phone.get({phoneId: $routeParams.phoneId}, function(phone) {
          self.setImage(phone.images[0]);
        });

        self.setImage = function setImage(imageUrl) {
          self.mainImageUrl = imageUrl;
        };
      }
    ]
  });
```

# ngAnimate

> 为了使用animations，需要加载依赖`angular-animate.js`和`jquery.js`以及包含使用了动画的CSS和JavaScript代码

**app/index.html:**

```html
...

<!-- Defines CSS necessary for animations -->
<link rel="stylesheet" href="app.animations.css" />

...

<!-- Used for JavaScript animations (include this before angular.js) -->
<script src="bower_components/jquery/dist/jquery.js"></script>

...

<!-- Adds animation support in AngularJS -->
<script src="bower_components/angular-animate/angular-animate.js"></script>

<!-- Defines JavaScript animations -->
<script src="app.animations.js"></script>

...
```

## Dependencies

**app/app.module.js:**

```javascript
angular.
  module('phonecatApp', [
    'ngAnimate',
    ...
  ]);
```

## CSS Transition Animations：Animating ngRepeat

- 注意下列代码中的phone-list-item

```html
...
<ul class="phones">
  <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query | orderBy:$ctrl.orderProp"
      class="thumbnail phone-list-item">
    <a href="#!/phones/{{phone.id}}" class="thumb">
      <img ng-src="{{phone.imageUrl}}" alt="{{phone.name}}" />
    </a>
    <a href="#!/phones/{{phone.id}}">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>
...
```

**app/app.animations.css:**

```css
.phone-list-item.ng-enter,
.phone-list-item.ng-leave,
.phone-list-item.ng-move {
  transition: 0.5s linear all;
}

.phone-list-item.ng-enter,
.phone-list-item.ng-move {
  height: 0;
  opacity: 0;
  overflow: hidden;
}

.phone-list-item.ng-enter.ng-enter-active,
.phone-list-item.ng-move.ng-move-active {
  height: 120px;
  opacity: 1;
}

.phone-list-item.ng-leave {
  opacity: 1;
  overflow: hidden;
}

.phone-list-item.ng-leave.ng-leave-active {
  height: 0;
  opacity: 0;
  padding-bottom: 0;
  padding-top: 0;
}
```

