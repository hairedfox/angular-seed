### Template

```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="lib/angular/angular.js"></script>
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

app/app.js
```js
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
});
```


### Components

Create new component
```js
angular.
  module('myApp').
  component('greetUser', {
    template: 'Hello, {{$ctrl.user}}!',
    controller: function GreetUserController() {
      this.user = 'world';
    }
  });
```

```html
<body>
  <!-- The following line is how to use the `greetUser` component above in your html doc. -->
  <greet-user></greet-user>
</body>
```

Using component
```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="lib/angular/angular.js"></script>
  <script src="app.js"></script>
  <script src="phone-list.component.js"></script>
</head>
<body>

  <!-- Use a custom component to render a list of phones -->
  <phone-list></phone-list>  <!-- This tells AngularJS to instantiate a `phoneList` component here. -->

</body>
</html>
```

app/phone-list.component.js
```js
// Register `phoneList` component, along with its associated controller and template
angular.
  module('phonecatApp').
  component('phoneList', {  // This name is what AngularJS uses to match to the `<phone-list>` element.
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

### One Feature per File
It might be tempting, for the sake of simplicity, to put everything in one file, or have one file per type; e.g. all controllers in one file, all components in another file, all services in a third file, and so on. This might seem to work well in the beginning, but as our application grows it becomes a burden to maintain. As we add more and more features, our files will get bigger and bigger and it will be difficult to navigate and find the code we are looking for.

Instead we should put each feature/entity in its own file. Each stand-alone controller will be defined in its own file, each component will be defined in its own file, etc.

Luckily, we don't need to change anything with respect to that guideline in our code, since we have already defined our phoneList component in its own phone-list.component.js file. Good job!

We will keep this in mind though, as we add more features.

#### Structure
module
```js
angular.
  module('phonecatApp').
  component('phoneList', ...);
```

```
app/
  phone-list/
    phone-list.module.js
    phone-list.component.js
    phone-list.component.spec.js
  app.module.js
```

### External Template
```js
angular.
module('phoneList').
component('phoneList', {
  // Note: The URL is relative to our `index.html` file
  templateUrl: 'phone-list/phone-list.template.html',
  controller: ...
});
```

### Final Directory/File Layout
```bash
app/
  phone-list/
    phone-list.component.js
    phone-list.component.spec.js
    phone-list.module.js
    phone-list.template.html
  app.css
  app.module.js
  index.html
```

### Filtering
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

### Two-way Data Binding
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
- First, we added a <select> element bound to $ctrl.orderProp, so that our users can pick from the two provided sorting options.

- We then chained the filter filter with the orderBy filter to further process the input for the repeater. orderBy is a filter that takes an input array, copies it and reorders the copy which is then returned.

- AngularJS creates a two way data-binding between the select element and the $ctrl.orderProp model. $ctrl.orderProp is then used as the input for the orderBy filter.

```js
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: function PhoneListController() {
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

      this.orderProp = 'age';
    }
  });
```

### XHR & Dependency Injection

app/phone-list/phone-list.component.js
```js
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

`app/phone/phones.json`
```json
[
  {
    "age": 13,
    "id": "motorola-defy-with-motoblur",
    "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
    "snippet": "Are you ready for everything life throws your way?"
    ...
  },
  ...
]
```

### Template Links & Images

`app/phone-list/phone-list.template.html`
```html
...
<ul class="phones">
  <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query | orderBy:$ctrl.orderProp" class="thumbnail">
    <a href="#!/phones/{{phone.id}}" class="thumb">
      <img ng-src="{{phone.imageUrl}}" alt="{{phone.name}}" />
    </a>
    <a href="#!/phones/{{phone.id}}">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>
...
```

To dynamically generate links that will in the future lead to phone detail pages, we used the now-familiar double-curly brace binding in the href attribute values. In step 2, we added the {{phone.name}} binding as the element content. In this step the {{phone.id}} binding is used in the element attribute.

We also added phone images next to each record using an image tag with the ngSrc directive. That directive prevents the browser from treating the AngularJS {{ expression }} markup literally, and initiating a request to an invalid URL (http://localhost:8000/{{phone.imageUrl}}), which it would have done if we had only specified an attribute binding in a regular src attribute (<img src="{{phone.imageUrl}}">). Using the ngSrc directive, prevents the browser from making an HTTP request to an invalid location.

### Routing & Multiple Views

`package.json`
```json
{
  "name": "angular-phonecat",
  ...
  "dependencies": {
    "angular": "1.8.x",
    "angular-route": "1.8.x",
    "bootstrap": "3.3.x"
  },
  ...
}
```

`app/index.html`
```html
<head>
  ...
  <script src="lib/angular/angular.js"></script>
  <script src="lib/angular-route/angular-route.js"></script>
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

`app/app.module.js`
```js
angular.module('phonecatApp', [
  'ngRoute',
  ...
]);
```

`app/app.config.js`
```js
angular.
  module('phonecatApp').
  config(['$routeProvider',
    function config($routeProvider) {
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

`app/phone-detail/phone-detail.module.js`
```js
angular.module('phoneDetail', [
  'ngRoute'
]);
```

`app/phone-detail/phone-detail.component.js`
```js
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

`app/app.module.js`
```js
angular.module('phonecatApp', [
  ...
  'phoneDetail',
  ...
]);
```

### More Templating
In addition to phones.json, the app/phones/ directory also contains one JSON file for each phone:

`app/phones/nexus-s.json`
```json
{
  "additionalFeatures": "Contour Display, Near Field Communications (NFC), ...",
  "android": {
    "os": "Android 2.3",
    "ui": "Android"
  },
  ...
  "images": [
    "img/phones/nexus-s.0.jpg",
    "img/phones/nexus-s.1.jpg",
    "img/phones/nexus-s.2.jpg",
    "img/phones/nexus-s.3.jpg"
  ],
  "storage": {
    "flash": "16384MB",
    "ram": "512MB"
  }
}
```

`app/phone-detail/phone-detail.component.js`
```js
angular.
  module('phoneDetail').
  component('phoneDetail', {
    templateUrl: 'phone-detail/phone-detail.template.html',
    controller: ['$http', '$routeParams',
      function PhoneDetailController($http, $routeParams) {
        var self = this;

        $http.get('phones/' + $routeParams.phoneId + '.json').then(function(response) {
          self.phone = response.data;
        });
      }
    ]
  });
```

The inline, TBD placeholder template has been replaced with a full blown external template, including lists and bindings that comprise the phone details. Note how we use the AngularJS {{expression}} markup and ngRepeat to project phone data from our model into the view.

`app/phone-detail/phone-detail.template.html`
```html
<img ng-src="{{$ctrl.phone.images[0]}}" class="phone" />

<h1>{{$ctrl.phone.name}}</h1>

<p>{{$ctrl.phone.description}}</p>

<ul class="phone-thumbs">
  <li ng-repeat="img in $ctrl.phone.images">
    <img ng-src="{{img}}" />
  </li>
</ul>

<ul class="specs">
  <li>
    <span>Availability and Networks</span>
    <dl>
      <dt>Availability</dt>
      <dd ng-repeat="availability in $ctrl.phone.availability">{{availability}}</dd>
    </dl>
  </li>
  ...
  <li>
    <span>Additional Features</span>
    <dd>{{$ctrl.phone.additionalFeatures}}</dd>
  </li>
</ul>
```

### Custom Filters
Since this filter is generic (i.e. it is not specific to any view or component), we are going to register it in a core module, which contains "application-wide" features.

`app/core/core.module.js:`
```
angular.module('core', []);
```

`app/core/checkmark/checkmark.filter.js:`
```js
angular.
  module('core').
  filter('checkmark', function() {
    return function(input) {
      return input ? '\u2713' : '\u2718';
    };
  });
```

The name of our filter is "checkmark". The input evaluates to either true or false, and we return one of the two unicode characters we have chosen to represent true (\u2713 -> ✓) and false (\u2718 -> ✘).

Now that our filter is ready, we need to register the core module as a dependency of our main phonecatApp module.

`app/app.module.js`
```js
angular.module('phonecatApp', [
  ...
  'core',
  ...
]);
```

`app/index.html`
```html
...
<script src="core/core.module.js"></script>
<script src="core/checkmark/checkmark.filter.js"></script>
...
```

`app/phone-detail/phone-detail.tempalte.html`
```html
...
<dl>
  <dt>Infrared</dt>
  <dd>{{$ctrl.phone.connectivity.infrared | checkmark}}</dd>
  <dt>GPS</dt>
  <dd>{{$ctrl.phone.connectivity.gps | checkmark}}</dd>
</dl>
...
```

### Event Handlers

`app/phone-detail/phone-detail.component.js:`
```js
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

In the phoneDetail component's controller, we created the mainImageUrl model property and set its default value to the first phone image URL.

We also created a setImage() method (to be used as event handler), that will change the value of mainImageUrl.


`app/phone-detail/phone-detail.template.html:`
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

### Rest & Service

`app/core/phone/phone.module.js`
```js
angular.module('core.phone', ['ngResource']);
```

`app/core/phone/phone.service.js`
```js
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

Update template
```html
<head>
  ...
  <script src="lib/angular-resource/angular-resource.js"></script>
  ...
  <script src="core/phone/phone.module.js"></script>
  <script src="core/phone/phone.service.js"></script>
  ...
</head>
```

We can now simplify our component controllers (PhoneListController and PhoneDetailController) by factoring out the lower-level $http service, replacing it with the new Phone service. AngularJS's $resource service is easier to use than $http for interacting with data sources exposed as RESTful resources. It is also easier now to understand what the code in our controllers is doing.

`app/phone-list/phone-list.module.js`
```js
angular.module('phoneList', ['core.phone']);
```

`app/phone-list/phone-list.component.js:`
```js
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: ['Phone',
      function PhoneListController(Phone) {
        this.phones = Phone.query();
        this.orderProp = 'age';
      }
    ]
  });
```

`app/phone-detail/phone-detail.module.js:`
```js
angular.module('phoneDetail', [
  'ngRoute',
  'core.phone'
]);
```

`app/phone-detail/phone-detail.component.js:`
```js
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

Notice how in PhoneListController we replaced:
```js
$http.get('phones/phones.json').then(function(response) {
  self.phones = response.data;
});
```
with just 
```js
this.phones = Phone.query();
```
