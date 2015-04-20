##### Angular Fundamentals in 60 minutes - Notes

Great for Single Page Applications

The challenge with SPA's
+ DOM Manipulation
+ History
+ Module Loading
+ Routing
+ Caching
+ Object Modeling
+ Data Binding
+ Ajax/Promises
+ View Loading

AngularJS is a full featured SPA framework
+ Data binding
+ MVC
+ Routing
+ Testing
+ jqLite
+ Templates
+ History
+ Factories
+ ViewModel
+ Controllers
+ Views
+ Directives
+ Services
+ Dependency Injection
+ Validation

##### Directives, Filters, and Data Binding

A Directive teaches HTML new tricks.
```html
<!DOCTYPE html>
<html ng-app>   <!-- ng-app and ng-model are Directives -->
<head>
	<title></title>
</head>
<body>
	<div class="container"> 	<!-- {{ name }} is a data binding expression -->
		Name: <input type="text" ng-model="name" /> {{ name }}
	</div>
	<script src="angular.js"></script>
</body>
</html>
```
You can also add `data` in front of `ng-app` like this: `data-ng-app` or even `data-ng-app=""`

##### Iterating with the ng-repeat Directive
As a note, we normally don't use ng-init in our html since we use controllers but for this example we will
```html
<div class="container" data-ng-init="names=['Dave','Napur','Heedy','Shriva']">
	<h3>Looping with the ng-repeat directive</h3>
	<ul>
		<li data-ng-repeat="name in names">{{ name }}</li>
	</ul>
</div>
```