# dslify

Rewrites a JavaScript function, such that any global property access is transformed to call a member of a new _dsl_ argument. Use dslify to interpret domain-specific languages without messing about in global scope.

[![Dependency status](https://david-dm.org/featurist/dslify.png)](https://david-dm.org/featurist/dslify)

### Install

    npm install dslify

### Example

    var dslify = require('dslify');

    var fn = function() { return shout(word); };
    var shouter = dslify.transform(fn);

    var dsl = {
        say: function(it) { return it + "!!"; },
        word: "unicorns"
    };
    console.log(shout(dsl));
    //-> unicorns!!

### Strings Example

Sometimes you might want to operate with strings instead of JavaScript functions. For
example if you are generating templates or want to send JavaScript to the client.

    var dslify = require('dslify');

    var input = "function(input) { return shout(input, globalValue); };";
    var output = dslify.transform(input, {asString: true});

    output // function(input) { return shout(input, _dsl.globalValue); };

### How?
dslify parses functions using [esprima](https://github.com/ariya/esprima), rewriting them as new functions using  [escodegen](https://github.com/Constellation/escodegen).

### Hold on, isn't this just a long-winded JavaScript 'with'?
Yes. But 'with' is [leaky and dangerous](http://www.yuiblog.com/blog/2006/04/11/with-statement-considered-harmful/), wheras dslify is like a sandbox because it rewrites access to global scope, e.g:

    var dslify = require('dslify');

    var dsl = {};

    var withWith = function(dsl) {
        with (dsl) {
            y = 'leaks into global!';
        }
    };
    var withDslify = dslify.transform(function() {
        z = 'global is safe!';
    });

    withWith(dsl);
    withDslify(dsl);

    console.log(global.y);  // leaks into global!
    console.log(global.z);  // undefined
    console.log(dsl.z);     // global is safe!

### Isn't it hard to debug dynamically-generated functions?
Yes. And dynamically generating functions is relatively slow, compared to calling functions. Therefore consider transforming functions at build time instead of run time.

### License
BSD
