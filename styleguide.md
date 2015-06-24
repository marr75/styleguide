# Coding Style & Tenets

The following document is an outline and a snapshot of how we currently think. As with the industry of technology, we are always trying to evolve and improve how we work, including the style we write in and the tenets we maintain.


**Angular (JS)**

- [Documentation](#documentation)
- [Testing](#testing)
- [Structure](#module-structure)
- [Routes](#routes)
- [Directives](#directives)
- [Controllers](#controllers)
- [Factories](#factories)
- [Services](#services)


**Angular (HTML)**

- HTML
- Binding
- ngRepeat
- Filtering


## Angular (JS)

### Documentation

Factories and services are documented, occasionally complex controllers/directives are documented as well. We use [jsdoc](http://usejsdoc.org/) for the documentation of modules/services, and use jsdoc style documentation for individual methods around directives/controllers when helpful.

Every new service and factory should be documented.

``` javascript
/**
 * @constructor
 * @name Person
 * @return {Person}
 */
function Person() {}

/**
 * @function
 * @name Person#greet
 * @param {Object} properties
 * @param {String} properties.name - The name of the person to greet
 * @return {String} - 'Hello, [properties.name], my name is [this.name]'
 */
Person.prototype.greet = function(properties) {
  return 'Hello, ' + properties.name + ', my name is ' + this.name;
};
```


### Testing

**Libraries**

- Mocha
- Chai
- Sinon
- angular.mocks

**Run existing tests**

To run the tests for the front end, you need to have your vagrant instance running (`vagrant up`), and you need to be ssh'ed into the vagrant instance; `vagrant ssh`. Once inside of your vagrant box, `cd /vagrant/mm2/tools/` will take you to the correct directory, and `npm test` will run all of the tests while watching for file changes.

**AAA**

Tests follow the Arrange, Act, Assert pattern of using 3 clauses, one for each word.

``` javascript
describe('An example test', function() {

  it('should pass', function() {
    var setup = true;
    
    var result = example(setup);
    
    expect(result).to.be.true;
  });

});

```


### Module Structure

**Applications**

We create Top Level Modules for each "application" that exists, one for each: mySidewalk, Insights, Distributed profiles, Embeddable content, etc. Each top level module has the ability to reconfigure each of our providers differently for their specific use.

We would like to work towards a structure like this:

```
- mySidewalk
  - user profiles
  - landing page
- insights
  - chart logic
  - choropleth logic
- shared
  - core services
  - common ui fragments
```

**Submodules**

Every angular module is just a module, the fact that we leverage some as an application base is purely semantics. Each submodule should be 1 component, or a wrapper of several other modules.

A route along with it's controller can be a module, a factory can be a module, a directive and controller can be a module, etc. Each module on it's own should be capable of something, together they help us create our applications.

**Namespace**




### Routes

**Resolutions**

**ControllerAs**

****



### Directives

**Scope**

When possible, directives should not use isolate scope.


### Controllers

**ControllerAs rather than $scope**

A new instance of a controller function is created each time it is used. In this way it represents a constructor function. Controllers should bind to the instance of the controller rather than `$scope` when available.

We try to leverage Angular's `controllerAs` property when declaring directives and routes. This provides an alias for the scope that the instance of the controller is published under rather than using the `$scope` object for binding.

``` javascript
// BAD
.controller('ExampleCtrl', ['$scope',
  function($scope) {
    
    $scope.myExampleProperty = false;
    $scope.someExposedMethod = function() {
      return 'magic';
    };

  }
]);

// GOOD
.controller('ExampleCtrl', [
  function() {
    
    var ctrl = this;
    ctrl.myExampleProperty = true;
    ctrl.someExposedMethod = function() {
      return 'magic';
    };

  }
]);
```

**Spacing**

It is helpful to place 1 empty line at the beginning and end of the controller function to see a clear difference between the wrapping function and any functions declared inside of the controller.

``` javascript
// BAD
.controller('ExampleCtrl', [
  function() {
    var ctrl = this;
    ctrl.myExampleProperty = true;
    ctrl.someExposedMethod = function() {
      return 'magic';
    };
  }
]);

// GOOD
.controller('ExampleCtrl', [
  function() {
    
    var ctrl = this;
    ctrl.myExampleProperty = true;

    ctrl.someExposedMethod = function() {
      return 'magic';
    };

  }
]);
```

**Initializing functions**

There should not be any logic floating around a controller, e.g. conditional clauses, function calls, etc. If there is logic or more than one function that should be called on controller initialization, there should be a function named "init" that should be the last defined function, either in a IIFE style or a function that is declared and then immediately called on the following line.

``` javascript
// BAD
.controller('ExampleCtrl', [
  function() {
    
    var ctrl = this;
    ctrl.myExampleProperty = false;
    ctrl.performTrick = magic;
    
    // Floating logic
    if (true) {
      ctrl.setup = true;
    }

    function magic() {
      return 'ta-da';
    }

  }
]);

// GOOD
.controller('ExampleCtrl', [
  function() {

    var ctrl = this;
    ctrl.myExampleProperty = false;
    ctrl.performTrick = magic;

    function magic() {
      return 'ta-da';
    }
    
    // Contained start up logic in a IIFE
    (function init() {
      if (true) {
        ctrl.setup = true;
      }
    })();

  }
]);

```

**$scope for events**

The `$scope` object is still very important even though we bind to the controller's instance for exposing things in our templates. `$scope` is still used for listening to and sending (`$broadcast`\\`$emit`) events to other parts of the application.

We try to be mindful that single occurrence events should be un-subscribed from, and the same for `$watch` statements.

``` javascript
.controller('ExampleCtrl', ['$scope',
  function($scope) {

    var ctrl = this;
    
    function setFilterListener() {
      var unbind = $scope.$on('event', function() {
        ctrl.ready = true;
        $scope.$emit('ready');
        unbind();
      });
    }

    (function init() {
      setFilterListener();
    })();

  }
]);

```

## Angular (HTML)

### HTML

**Buttons vs Links**


### Binding

**One time binding**

**ngIf vs ngShow**




### ngRepeat

**Track by**


### Filtering

Filtering lists in an angular template runs each filter twice. It is best to avoid any filtering/transformations that can be accomplished in a controller environment.
