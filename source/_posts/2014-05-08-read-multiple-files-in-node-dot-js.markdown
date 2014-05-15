---
layout: post
title: "Read multiple files in Node.js"
date: 2014-05-08 05:09
comments: true
categories: 
---

I had a requirement to read multiple files in one go in Node.js project. 

Requirement:

- Read UTF8 files asynchronously. 
- Notify when each file read is done.
- Notify when reading all files is done.

I have found few examples on Stackoverflow:

- [Asynchronously reading and caching multiple files in nodejs](http://stackoverflow.com/questions/9618142/asynchronously-reading-and-caching-multiple-files-in-nodejs)
- [Reading and returning multiple files in Node.js using fs.readFile](http://stackoverflow.com/questions/9448336/reading-and-returning-multiple-files-in-node-js-using-fs-readfile)


In both cases answer was to use either [`async`](https://github.com/caolan/async) library or to just use native node.js functions. 

I don't like using external library and I am trying to use native/vanilla as much as possible. If there is one small part of external/third-party library that I have to use, I'd rather write my own. For example, if I have to get reference to DOM element by `id` on web page, I'd avoid using jQuery. If I would have to heavily traverse the DOM then I would definitely use jQuery.

So, I will skip using `async` library and use only native functions.

Here is complete code that we will go through: 

*Latest version of file is in repository on Github: [multiread.js](https://github.com/andrijac/multiple-file-read/blob/master/MultipleFileRead/MultipleFileRead/multiread.js)*

``` javascript multiread.js

	(function () {
	    "use strict";
	
		var fs = require('fs'),
			readline = require('readline'),
			filePathList = [], i, ii,
	
			toArray = function () { return Array.prototype.slice.call(arguments[0]); },
	
			rl = readline.createInterface({
				input: process.stdin,
				output: process.stdout
			});
	
		// validate call, must contain at least 3 arguments
		if (process.argv.length < 3) {
			wl("Usage: node multiread.js [file paths to read]");
			process.exit(0);
		}
	
		// start from 3rd argument, add them to filePathList
		for(i = 2, ii = process.argv.length; i < ii; i++) {
			filePathList.push(getActualFilePath(process.argv[i]));
		}
	
		// check file path
		function getActualFilePath(filePath) {
			var relative;
	
			// check absolute path
			if(fs.existsSync(filePath)) {
				return filePath;
			}
	
			// get absolute path from relative path
			relative = [__dirname, filePath].join("/");
	
			// check relative path
			if(fs.existsSync(relative)) {
				return relative;
			}
	
			throw new Error("File " + filePath + " not found");
		}
	
		/**
		 * @param execFunc {Function} Function that will be called for each argument set in @args array.
		 * @param args {Object[]} Array where each item is array objects which will be used in each call.
		 * @param eachCallback {Function} Callback after each execution.
		 * @param callback {Function} Callback when all arguments are processed.
		 */
		function batch(execFunc, args, eachCallback, callback) {
			var index = -1, cb, iterate, results= [];
	
			cb = function () {
				var callResult = toArray(arguments),
					isLastItem = index == args.length - 1;
	
				// put results from callback to results list for later processing
				// results list is passed into final callback function
				results.push(callResult);
	
				// notify that current call is done
				eachCallback.apply(null, callResult);
	
				if(isLastItem) {
					// if it is last item in args array, call final callback
					callback(results);
				} else {
					// continue iteration through args
					iterate();
				}
			};
	
			iterate = function () {
				index++;
				var i = index,
					argsArray = args[i] || [];
	
				// 'argsArray' collection was created in 'batchRead' method and it contains all arguments needed to invoke a function
				// here we are adding last argument in collection which is callback function 'cb' which is scoped inside parent function
				// inside 'cb' function iterate function will be called again until all arguments are not processed.
				argsArray.push(cb);
				execFunc.apply(this, argsArray);
			};
	
			// first iteration call
			iterate();
		};
	
		/**
		 * @param files {string[]} File path list.
		 * @param eachCallback {Function} Callback after each execution.
		 * @param callback {Function} Callback when all arguments are processed.
		 */
		function batchRead(files, eachCallback, callback) {
			var encoding = 'utf8',
				args = [];
	
			// build args array
			files.forEach(function(file) {
				args.push([file, encoding]);
			});
	
			batch(fs.readFile, args, eachCallback, callback);
		}
	
		batchRead(filePathList,
			// callback after each file read
			function(err, text) {
				console.log("File read done. Text: " + text);
			},
	
			// callback when everything is done
			function(result) {
				var insertTextArr = [];
	
				result.forEach(function(i) {
					insertTextArr.push(i[1]);
				});
	
				console.log("");
				console.log("All:");
	
				console.log(insertTextArr.join("\n"));
			});
	
		// wait in console
		rl.question("", function () { rl.close(); });
	})();
```

## TL;DR ##

### `batch` function
The idea behind `batch` function was to have generic function that will execute array of asynchronous functions and wait for them to finish, something like what `async` doing, as long as signature of function is `function([parameters], callback)`.

Parameter description:

1. `execFunc` - function that is suppose to be executed for each argument set. In this case `execFunc` is `fs.readFile`.
2. `args` - array of 'argument sets' for each execution => 'argument set' is array of arguments without callback. In this case, set contains file path and encoding.
3. `eachCallback` - callback for each `execFunc` call.
4. `callback` - callback when all is done.

Inside `batch` function there are two functions: `cb` and `iterate`.

`iterate` function does actual call to `execFunc`. It will add `cb` function to collection arguments at last position to act as actual callback of `execFunc`. This way we control each callback of `execFunc` and notify outside world when we want that it's all done. Also, this method will queue execution of asynchronous functions i.e. making them execute sequentially.

`cb` function is a `execFunc` callback. As arguments of function are actual result of `execFunc` execution, this is the place where we will catch it.<br /> 
In `batch` function level, there is `results` array that will hold all results returned from `execFunc` executions. This array will be an argument for final callback call. `results` array is populated here on each call. <br /> 
Then, we will call `eachCallback` function to notify outside world that execution of `execFunc` is done.<br /> 
Then, we will check if this was a last `execFunc` call. If yes, call final `callback` sending all results as an argument. If no, continue iteration.

### `batchRead` function

`batchRead` function wrapping a call to `batch` function to simplify call to generic `batch` function.

Parameter description:

1. `files` - array of file paths to be read.
2. `eachCallback` - callback for each `fs.readFile` call.
3. `callback` - callback when all is done.

As `batch` function is accepting `object[object[]]` array for arguments, we need to repack file paths array to new array. Also, in each of these arrays, we will add additional argument => `utf8`. <br />
Then, we are calling `batch` function with all appropriate arguments.

### `batchRead` call

At the end, we will call `batchRead`. `eachCallback` should have same signature as you would use when calling `fs.readFile`.

##Summary

If you have to call same function with different arguments several times, queue each call and have final callback, you can use `batch` function. `batchRead` shows how `batch` function can be utilized by reading multiple files sequentially and notifying the application when all is done.


HTH