# expression-sandbox
This lets you compile and run someone else's arbitrary JavaScript safely within your own JavaScript program.

## Installation
```
npm install --save expression-sandbox
```

## Usage
```js
var compiler = require('expression-sandbox');
var code = compiler('Math.round(a + b / 3)');

var result = code({a: 7, b: 5, Math});
console.log(result); // => 9
```

## What kinds of things are protected against?
If you don't pass anything into the sandbox context, then you're protected EVERYWHERE!

From inside the sandbox...
* You can't modify anything that wasn't created in the sandbox.
	* you can't modify native prototypes
	* you can't modify any object that's reachable from the native prototypes
	* you can't modify anything that was passed into the sandbox
	* you can't modify the sandbox context object itself
* You can't access the global object.
	* This is specifically forbidden. You're not allowed to access it even if it's passed into a sandbox explicitly
* You can't access properties that start with `_` on objects that are passed into the sandbox.
* You can't access any scope anywhere except for what is specifically passed into the sandbox. Anything passed into the sandbox can be accessed, but not modified.

## What kinds of things are NOT protected against?

* Any sensitive information that is passed into the sandbox (unless that information is behind a property that starts with `_`)
* Any functions that are created inside the sandbox, but are returned, and then executed outside of the sandbox

It is recommended to cast the result of a sandboxed execution to a primitive type, to prevent accidentally invoking an insecure function:
```js
var result = String(code(myContext))
```

## Am I wrong?

If you think there are vulnerabilities in the sandbox that I didn't think of, please create a Github issue to bring them to my attention.

## Caveats

This package replaces some built-in objects with [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). In rare cases, this can cause odd behvaior with with `===` operator, if you cache one of these objects before this package is loaded. For this reason, it is recommended that this package is loaded before any other JavaScript code runs.
