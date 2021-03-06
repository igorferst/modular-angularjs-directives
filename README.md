# Modular AngularJS Directives

Modular directives let you write AngularJS directives in an incremental, modular, and naturally hierarchical way.


## Why/when are they useful?

If you have directives that share a lot of code, the standard Angular way of DRYing up your code is encapsulating shared code into a service. However, this can cause an excess of helper services and associated boilerplate in your directives, resulting in less readable, bloated directives. An alterative is applying multiple directives and using Angular's priority feature to make sure they are applied in the correct order.

Modular directives are an alternative paradigm in which directives are defined incrementally by having one modular directive inherit behavior from another. This results in directive code that is more modular and readable, and allows you to write directives in a naturally hierarchical way. Best of all, unlike the multiple directive paradigm, all behavior is captured in a single directive and there's no need to worry about priorities for multiple directives.

Modular directives are not globally superior to helper services, but in the right situation they can significantly improve code quality.


### Example use case

At InSample, we wrote our own chart library. Charts share a lot of functionality related to asynchronously fetching data, responsive resizing, rendering titles and axes, etc. The size and complexity of this shared code was high, and using helper services resulted in an excess of boilerplate inside our directives. Using modular directives allowed us to organize shared chart code into a hierarchy, making our directives leaner and easier to test.

For example, the part of the chart hierarchy that includes the histogram and bar chart looks like this:

1. A base modular directive that contains plot rendering boilerplate;
2. an extension that adds code to asynchronously fetch data;
3. an extension that encapsulates common data preprocessing logic;
4. branching from that, the histogram and bar chart directives, each with their own rendering logic.

Each level of this hierarchy is oblivious to the implementation of, and the services referenced by, the previous level.


## How do they work?

In AngularJS, a directive is defined with a [Directive Definition Object](https://docs.angularjs.org/api/ng/service/$compile) (DDO), which is a plain javascript object whose values configure the directive. A modular directive is a constructor function that instantiate a DDO and is extensible.

Extending a modular directive means that controller and link functions are called in sequence, and isolate scope object are extended (in the object sense). For details, see the comments in `modular-directives.js`.

This extensibility makes it possible to create "directive hierarchies". Once a modular directive has been sufficiently extended, generating a directive is trivial: simply instantiate a DDO and use it to declare a new directive.


## Can I see a demo?

First, run `npm install` and `bower install` inside the project root directory.

The demo is at `demo/demo.html`. The file `demo/js/services.js` defines a simple modular directive hierarchy. The file `demo/test/abstract-demo-widget-spec.coffee` demonstrates how to test a modular directive (using Jasmine + Coffeescript). Both are extensively commented.


## How do I install and use it?

Install using [Bower](http://www.bower.io):

```
bower install modular-directives
```
Then use in your Angular app by including the module `insample.modular_directives`.


## How do I develop?

First, run `npm install` and `bower install` inside the project root directory.

Run the tests with
```
./node_modules/karma/bin/karma start
```

Tests are written in [Coffeescript](http://coffeescript.org/).


## Caveats

A modular directive *must* have an isolate scope.

Modular directives do not support pre-link functions. Link functions must be returned by compile
functions; standalone link functions defined on the `link` key are not supported.
