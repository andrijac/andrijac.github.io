---
layout: post
title: "Javascript breakpoint"
date: 2014-01-31 09:44
comments: true
categories: 
---

This post will describe how to set breakpoint in before any javascript method call from console.

- GitHub repository: [https://github.com/andrijac/break-js](https://github.com/andrijac/break-js)
- break.js: [https://github.com/andrijac/break-js/blob/master/break.js](https://github.com/andrijac/break-js/blob/master/break.js)
- break.min.js: [https://github.com/andrijac/break-js/blob/master/break.min.js](https://github.com/andrijac/break-js/blob/master/break.min.js)

In post:

- [Implementation](#implementation)
- ["Break" Function](#break)
- [Usage](#usage)

##TL;DR

Background <a name="background" href="#background" class="anchor">#</a>
-

To make a long story short, I was working on a big single page web application a year ago, with huge javascript code base. 
Whenever I wanted to set a breakpoint in editor/dev tools/firebug, it took time to find the function. 
Either because it took time for source viewer to load (because of huge javascript files), or to locate in which file is function of interest.

So I have started inserting `debugger;` directive to my scripts when I need to break. 
The problem was, then I wanted to remove the directive, I had find the directive, remove it or comment it out, and to reload the page, but as we were working on SPA, I basically had to restart my debugging sessions.
I also tried to make a global flag, and based on flag to set enable the debugger:

```javascript
var IS_DEBUGGER_ENABLED = false;
...
function breakpoint() {
  if(IS_DEBUGGER_ENABLED) {
    debugger;
  }
}
```
Whenever I wanted to turn debugger on, I could just set in console:
```javascript
IS_DEBUGGER_ENABLED = true
```

Problem was I did not really like to have this function call all over the code. 
Also, I wanted to have control where will code break, so I was then forced to go back and remove `breakpoint()` calls.

Implementation <a name="implementation" href="#implementation" class="anchor">#</a>
-
The goal is to make a function that can insert a breakpoint before the call of any function we are interested in, which is accessible from global object.

Function needs to do following:

- Save reference to original function
- Override original function with wrapper.
- Option to restore original function and remove breakpoint.

Let's say we have following object structure:
```javascript
var foo = {};

foo.bar = {};

foo.bar.func = function () {
  console.log('test');
};    
```
Path to `func` function is through `foo.bar.func`.
This is usually a case in huge javascript libraries such as Ext.js (Sencha), YUI etc.

To be able to set breakpoint, I would need to set `debugger;` right before `foo.bar.func` call.

So, I thought it would be nice to wrap `foo.bar.func` function in new function:

```javascript
function wrapper() {
  debugger;
  foo.bar.func();
}
```
But wrapper also needs to be called `foo.bar.func`, because if function is called it has to actually call wrapper.
But if wrapper is set to `foo.bar.func`, it means it will override the original function, so we need save a reference to original function somewhere.

How to save a reference to a function? <br />
I could pass into wrapper a direct reference to function and assign it to variable, but instead I am passing in a function name. The reason for this is because I need to access a function dynamically and also I will need a key to reference back to original function.
You will see what I mean as I go further in post. I might change this in the future and be able to pass direct reference to function in wrapper.
Ok, as I have only function name, only way I found (at the moment) to call a function was by compiling a new Function object that calls a function.
You could easily call a global function by name through `window` object (in browser):

```javascript
window['function_name'](parameters);
```

This works if function is on global object, but if function in inside another object, this will not work.
So, to invoke `foo.bar.func`:

```javascript
var func_name = 'foo.bar.func';

// Compile a new Function that is returning a reference to function and execute it.
var func_reference = (new Function("return " + func_name + ";"))();
```

Now we have original function reference assigned to variable and we can override function with wrapper.
Wrapper should look like this:
```javascript
function wrapper() {
  // Convert arguments to array
  var args = Array.prototype.slice.call(arguments);
  
  // Break before original function call
  debugger;
  
  // Call a function.
  // 'this' should be the same as it would be in original function since wrapper replaced original function.
  // We will always return result from original function.
  return original_function.apply(this, args);
}
```

Override function:
```javascript
var override = new Function("overrideFunc", funcName + " = overrideFunc;");
```

"Break" function <a name="break" href="#break" class="anchor">#</a>
-
Finally, "break" function:

```javascript
var __break = (function() {
	"use strict";

	// as we need to keep original functions, we will use clousers to keep a original functions here.
	var cache = {},
		cArray = function(args) { return Array.prototype.slice.call(args); },
		concat = function() { return cArray(arguments).join(""); };

	// funcName - name of the function, full name accessable from global object.
	// removeBreakpoint - use it only when you want to remove a breakpoint, set it to any truthful value.
	return function(funcName, removeBreakpoint) {

		// get reference to original function
		var original = (new Function(concat("return ", funcName, ";")))(),
			// compile override function
			override = new Function("overrideFunc", concat(funcName , " = overrideFunc;"));

		// check removeBreakpoint flag if it is true.
		if(!!removeBreakpoint) {
			// restore original function
			override(cache[funcName]);
			// remove from cached collection
			delete cache[funcName];
			return;
		}

		// if function is already overriden, exit
		if(!!cache[funcName]) {
			return;
		}

		// cache original function
		cache[funcName] = original;

		// override original function
		override(function () {
			var args = cArray(arguments);
			debugger;
			return original.apply(this, args);
		});
	};
}());
```

Usage <a name="usage" href="#usage" class="anchor">#</a>
-

To set the breakpoint:
```javascript
__break('foo.bar.func');
```
To remove the breakpoint:
```javascript
__break('foo.bar.func', true);
```

Potentially, we could add this function to `console` object:
```javascript
if(!console.break) {
  console.break = __break;
}
```

Repository is at: [https://github.com/andrijac/break-js](https://github.com/andrijac/break-js)

HTH