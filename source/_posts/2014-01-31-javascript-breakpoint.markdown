---
layout: post
title: "Javascript breakpoint"
date: 2014-01-31 09:44
comments: true
categories: 
---

This post will describe how to set breakpoint in before any javascript method call from console. 

Background
-

To make a long story short, I was working on a big single page web application a year ago, with huge javascript code base. 
Whenever I wanted to set a breakpoint in editor/dev tools/firebug, it took time to find the function. 
Either because it took time for source viewer to load (because of huge javascript files), or to locate in which file is function of interest.

So I have started inserting `debugger;` directive to my scripts when I need to break. 
The problem was, then I wanted to remove the directive, I had find the directive, remove it or comment it out, and to reload the page, but as we were working on SPA, I basically had to start again.
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
When ever I wanted to turn debugger on, I could just set in console:
```javascript
IS_DEBUGGER_ENABLED = true
```

Problem was, I did not really like to have this code all over code. 
Also, I wanted to have control where will code break, so I was then forced to go back and remove `breakpoint()` calls.

Breakpoint
-
Goal of script that I wrote was to set breakpoint before call of any function that is accessable from global object, for example:

```javascript
var foo = {};

foo.bar = {};

foo.bar.func = function () {
  console.log('test');
};    
```
Path to `func` function is through `foo.bar.func`.
This is usually a case in huge javascript libraries such as Ext.js (Sencha), YUI etc.

To be able to set breakpoint, I would need to set `debugger;` right before `foo.bar.func`.

So, I thought it would be nice to wrap `foo.bar.func` function in new function:

```javascript
function wrapper() {
  debugger;
  foo.bar.func();
}
```
But wrapper needs to be also called `foo.bar.func`, because if function is called it has to actually call wrapper.
But if function is set to `foo.bar.func`, it means it will override the original function, so we need "save" function on some other place.

So how to "save" a function?
I could use a variable and set function to it, but I need function by name for reasons you will see further down the post.
Ok, as I have only function name, only way I found, at the moment, to call a function was by compiling a new Function object that calls a function.
You could easily call a global function by name through `window` object (in browser):

```javascript
window['function_name'](parameters);
```

This works if function is on global object, but if function in inside an other object, this will not work.
So, to invoke `foo.bar.func`:

```javascript
var func_name = 'foo.bar.func';
var func_reference = (new Function("return " + func_name + ";"))();
```

Compile a new Function that is returning a pointer to function and execute it.


function name because of cache key, and to access the function.








