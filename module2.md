# Modularizing Code in Node.js

Before writing Node.js applications you must learn and understand about modules and packages in Node.js. Modules in Node.js are the building blocks of an application. It helps you break down your application into smaller pieces. Node.js follows a similar philosophy from the starts of its origin that has been a common practice in the Unix programming world, that is, "one program should do one thing and it should do very well." Modules in Node.js are nothing but JavaScript files that end with `.js` extension that contains JavaScript code along with usage of Node.js Core API and other third party modules from npm.

## Types of modules

We can categorize different modules in Node.js. Overall there are three types of modules:

- core modules (or built-in modules)
- local modules
- third party modules

In simple terms, a module is nothing but an encapsulation over a single piece of code. Let us illustrate this by giving an example. Create a file called `hello.js` with the following code.

```js
// hello.js
function sayHello() {
	console.log('Hello');
}
```

This file can be thought of a utility that provides a function which returns the result hello such that we do not have to write this same piece of code multiple times in out Node.js app. We can use this utility multiple times to get the desired result. To use this utility, we must use a `require()` function to include it in other programs. But just requiring the file, will it work?

The answer is no. Every function or variable declared inside a Node.js file is local to that module. To access any of the function or variable, in our above example, the function `sayHello()` we need to first export it. Each Node.js module type uses `module.exports` to make functions available to different JavaScript files. We can re-write our example as:

```js
// hello.js
module.exports = function sayHello() {
	console.log('Hello');
};
```

Loading a module is a synchronous operation. A module is cached in memory such that calling `require()` returns immediately and return the same object as `module.exports` return. Any variable or a function defined inside a JavaScript file is local to that file only. This means if you are not exporting that property using `exports` then you cannot `require()` it to use it in another file. Now we can use the sayHello function in our `index.js` file by importing the file `hello.js`, passing the fall path as the only argument of require function.

```js
// index.js
const sayHello = require('./hello.js');
```

The above code is equivalent to:

```js
const sayHello = {
	sayHello: () => {
		console.log('Hello');
	}
};
```

We can now access sayHello function inside index.js file.

```js
// index.js
const sayHello = require('./hello.js');

sayHello();

// Output: Hello
```

[Run this program](https://repl.it/@amandeepmittal/m20)

The keyword require returns an object returns the reference of the value of exports for a given file. If we re-assign `module.exports` to a different object or a function then any properties added to the original or previous `module.exports` object will become inaccessible. What if you want to provide multiple properties to be accessed via `hello.js`? What if there are two functions that need to be accessed in `index.js`, for example:

```js
// hello.js
module.exports = function sayHello() {
	console.log('Hello');
};

module.exports = function sayHi() {
	console.log('Hi');
};
```

The above snippet is not the correct way to export from a module. When you want to export multiple objects or functions from a local module, instead of using module.exports we use simply exports. The reason is that `module.exports` refers to the whole module and will only export the last reference, in our example, `sayHi()`. However, `exports` refers to the same object as `module.export`. Take a look at below. Both object means the same.

```js
const exports = module.exports{};

// is equivalent to

module.exports = {
	sayHello: () => {
		console.log('Hello');
	},

	sayHi: () => {
		console.log('Hi);
	}
};
```

Thus, our `hello.js` can be written as:

```js
// hello.js
exports.sayHello = function() {
	console.log('Hello');
};

exports.sayHi = function() {
	console.log('Hi');
};
```

And in `index.js`:

```js
//index.
const { sayHello, sayHi } = require('./hello.js');

sayHello();

// Output: Hello

sayHi();

// Output: Hi
```

[Run](https://repl.it/@amandeepmittal/m22)

## Built-in Modules

The core modules are included with Node.js when we install it and provide bare minimum functionalities. Some of the examples of core modules are http, utils, fs, path, url, etc. These are the modules documented in Node.js documentation. Most core modules are required to be imported inside a JavaScript file to access their methods and properties but there are some exceptions like console module and process object. For example, we can access a core module like this:

```js
const http = require('http');
```

The `require('http')` part is what gives us access to the functions of the http module. The functions of this module help us create a web server which we take a look in a further tutorial on how to build one.

## Local Modules

Second types of modules are local modules. These modules are custom modules which we create in the Node.js application. They end with a file extension called `.js`. These modules are not provided by Node.js core API. To use these modules, again we can use `require()` method. First, let us learn how can we create a local module.

## Techniques of Modularizing JavaScript Code

In this section, we are going to create a custom module or you can say a local module. Create a file called `my-module.js` in which we will define the following code:

```js
const PI = Math.PI;

exports.area = function(r) {
	result = PI * r * r;
	console.log(result);
};

exports.circumference = function(r) {
	result = 2 * PI * r;
	return console.log(result);
};
```

This is an example of a local module. We are defining two functions area and circumference to calculate the value. Modules like these are encapsulated pieces of code which means anything whether a variable or a function defined inside this type of local module is only accessible to within this file.

## Using require() to modularize application code

Node.js has a built-in function called `require()` that help us to import and use functionalities of one module to another. We will create another file called `local-module.js` which will require both the functions area and circumference from `my-module.js`. The require() function accepts an argument that is mandatory. This argument is the path of the other local module from which we want to access variables and functions. This path is absolute.

```js
const calc = require('./my-module.js');

calc.area(2);
calc.circumference(2);
```

If you run this file from the terminal, you will get the following output:

```shell
node local-module.js

# output
12.566370614359172
12.566370614359172
```

[Run](https://repl.it/@amandeepmittal/m23)

But how is this possible? Only in the previous section did we learn that a function or a variable inside a file in Node.js can only be used inside that file. Well, we are using one keyword that we did not mention but you may have noticed is `exports`.

`exports` does what its name says. It is an object through which we can use the properties of a local module in another local module. Node.js understands this keyword and runs the program accordingly. Note that it is not available in JavaScript. This is pure to Node.js. The require function has access to every property of an `exports` object.

## Using npm for Third Party Modules

So far we have seen how to use Node Core module and local module. There is a third type of module that you can use your application. It is called npm modules or third party modules.

npm is a third party registry where anyone can publish a useful local module on the web that people can use. It currently contains more than 400,000 such modules. You can install them through the command line in your project. For that, first, you have to make sure that npm is installed.

npm automatically comes with Node.js binary. To check if you have installed it correctly, please open your terminal:

```shell
npm --version

# output
5.6.0
```

The version of npm depends on which version of Node.js you installed. To install a package locally. To continue our example, we will use `local-module.js` and install this third party module called `circle.js` from npm to calculate the area of a circle.

```shell
npm install --save circle.js
```

Now we will require it in our local-module.js.

```js
const cicrle = require('circle.js');

const result = circle.area(2);

console.log(result);
```

To run this program, use terminal.

```shell
node local-module.js

# output
12.566
```
