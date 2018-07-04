# Accessing Local Resources

In this tutorial, we are going to start by taking a look at how to interact and use Core Node.js modules (built-in modules). We are going to go through process object, file system module and buffers.

## Process Object

The `process` object in Node.js is a global object that can be accessed inside any module without requiring it. There are very few global objects or propeties provided in Node.js and `process` is one of them. It is an essential component in Node.js ecosystem as it provides various information sets about the runtime of a program. To explore we will use one of its properties which is called `process.versions`. This property tells us the information about Node.js version we have installed. It has to be used with `-p` flag.

```shell
$ node  -p "process.versions"

# output
{ http_parser: '2.8.0',
  node: '8.11.2',
  v8: '6.2.414.54',
  uv: '1.19.1',
  zlib: '1.2.11',
  ares: '1.10.1-DEV',
  modules: '57',
  nghttp2: '1.29.0',
  napi: '3',
  openssl: '1.0.2o',
  icu: '60.1',
  unicode: '10.0',
  cldr: '32.0',
  tz: '2017c' }
```

Another property you can check is `process.release` that is same to the command `$ node --version` which we used when we installed Node.js but the output this time is going to be more detailed.

```shell
node -p "process.release"

# output
{ name: 'node',
  lts: 'Carbon',
  sourceUrl: 'https://nodejs.org/download/release/v8.11.2/node-v8.11.2.tar.gz',
  headersUrl: 'https://nodejs.org/download/release/v8.11.2/node-v8.11.2-headers.tar.gz' }
```

These are some of the different commands that we can use in command line to access information otherwise no module can provide. This `process` object is an instance of EventEmitter class and it does it contain its own pre-defined events such as `exit` which can be used to know whena program in Node.js has completed its execution. Run the below program and you can observe that the result comes up with status code `0`. In Node.js this status code means that a program has run successfully.

```js
process.on('exit', code => {
	setTimeout(() => {
		console.log('Will not get displayed');
	}, 0);

	console.log('Exited with status code:', code);
});
console.log('Execution Completed');
```

Output of the above program:

```shell
Execution Completed
Exited with status code: 0
```

`Process` also provides various properties to interact with. Some of them can be used in a Node application to provide a gateway to communicate between the Node application and any command line interface. This is very useful if you are building a command line application or utility using Node.js

- process.stdin: a readable stream
- process.stdout: a writable stream
- process.stderr: a wriatable stream to recognize errors

Using `argv` you can always access to arguments that are passed in a command line. `argv` is an array which has Node itself as the first element and the absolute path of the file as the second element. From third element onwards it can have as many arguments.

Try the below program to get more insight of how you can use these various properties and functions.

```js
process.stdout.write('Hello World!' + '\n');

process.argv.forEach(function(val, index, array) {
	console.log(index + ': ' + val);
});
```

If you run the above code with the following command you will get the output and the first two elements are of `argv` are printed.

```shell
$ node test.js

# output
Hello World!
0: /usr/local/bin/node
1: /Users/amanhimself/Desktop/articles/nodejs-text-tuts/test.js
```

Now try running the same command by providing an extra argument.

```shell
$ node test.js 'first argument'

# output
Hello World!
0: /usr/local/bin/node
1: /Users/amanhimself/Desktop/articles/nodejs-text-tuts/test.js
2: first argument
```

If you want to provide strings you to put them inside quote as the command line interface recognize spaces as a break between two different string literals.

## Manipulating File System

As a programmer chances are that you are going access a file system in some application or the other. This includes reading, writing, updating, deleting and renaming a file. Node.js built-in module `fs` takes care of these operations and you can import it in your program using the following syntax:

```js
const fs = require('fs');
```

All of these operations in Node.js have a synchronous and an asynchronous way to exectue the function. Let us take a look at all the important and common methods that we mght use using `fs` module.

### Opening a File

To read or manipulate files in a system, we first need access to them. This access is provided by two things, the path to the file and `open` function which is used to open a file.

```javascript
fs.open('/path/to/file', 'r', (err, fd) => {
	// ...
});
```

The first argument is the path to the file, the second argument is is a flag that determines the mode with which file should open. In our case, `r` indicates that the file should be open as in a read-only mode. There are other modes that we will discuss later. Lastly, is a callback function which when invoked provides a file descriptor `fd` which is used for manipulation of the file.

The other flags that you can use a second argument are:

- `r+`: for reading and writing
- `w`: create a file for writing
- `w+`: for reading and writing, and also creates the file if it does not exist.
- `a`: for appending the file, will begin appending at the last position of a character.

**Reading a File**

Once the file is open, you can read the entire content of the file asynchronously.

```javascript
fs.readFile('/path/to/file', (err, data) => {
	console.log(data);
});
```

The callback possesses two arguments in which `data` contains the content of the file. If character encoding is specified, a raw buffer is returned. To read the file in human readable form we have to mention `utf-8` character encoding as the second argument. Let us understand this better with the following example. We will be using the asynchornous way.

```js
const fs = require('fs');

fs.readFile('./extra-files/abc.txt', (err, data) => {
	if (err) {
		return console.error(err);
	}
	console.log('Asynchronous read: ' + data.toString());
});

console.log('Execution finished');
```

If you run the above snippet, you will get the following output:

```shell
Execution finished
Asynchronous read: I am Text file that contains MORE data.
```

The last console.log statement runs before because `fs.readFile` is an asynchronous function. To run make it synchornous, just replace `fs.readFile` with `fs.readFileSync`.

### **Writing in a File**

To write in a file and content we can make use of `fs.writeFile` method. This function asynchronously writes the data to a file mentioned as the second argument. It will replace any data that already exists in the file. `data` can be sent as a string or a buffer.

```javascript
const fs = require('fs');

console.log('Execution finished');

fs.writeFile(
	'./extra-files/dest.txt',
	'Using Nodejs File system',
	(err, data) => {
		if (err) {
			return console.error(err);
		}
		console.log('Data written!');

		fs.readFile('./extra-files/dest.txt', function(err, data) {
			if (err) {
				return console.error(err);
			}
			console.log('Asynchronous read: ' + data.toString());
		});
	}
);
```

The output of the above program will be

```shell
Execution finished
Data written!
Asynchronous read: Using Nodejs File system
```

### Updating a File

`fs.appendFile()` is the method that can be used to update the file. Behind the scene it uses flag `a` as it does not replaces the data that already exists in the file but it starts writing in the file from the last character position.

```javascript
const fs = require('fs');

console.log('Execution finished');

fs.appendFile(
	'./extra-files/dest.txt',
	'\n Now we are appending this file!',
	(err, data) => {
		if (err) {
			return console.error(err);
		}
		console.log('Data written!');

		fs.readFile('./extra-files/dest.txt', function(err, data) {
			if (err) {
				return console.error(err);
			}
			console.log('Asynchronous read: ' + data.toString());
		});
	}
);
```

The `error` clause of our callback only runs when there is an error in accessing or manipulating the file. The output of the above program will be:

```shell
Execution finished
Data written!
Asynchronous read: Using Nodejs File system
 Now we are appending this file!
```

### Deleting a File

To delete a file using `fs` module, we make use of `fs.unlink()` method.

```javascript
const fs = require('fs');

fs.unlink('./extra-files/third-file.js', error => {
	if (error) {
		console.log(error);
	}
	console.log('File Deleted!');
});
```

If there is a success in deleting the file specified, the last console statement in the callback will print.

## Understanding Buffer

Binary data is simply a set or a collection of `1` and `0`. Each number in a binary is called a _bit_. Computer converts the data to this binary format to store and perform operations. For example, the following are five different binaries:

`10, 01, 001, 1100, 0010011`

JavaScript does not have a binary type data in its core API as compared to other programming languages. To solve this problem, Node.js includes a binary buffer implementation with a global module called `Buffer`. Yes, it is one of the few other global modules that you do need to require in your application.

### Creating a Buffer

There are different ways you can create a buffer in Node.js. You can create an empty buffer by with a size of 10 bytes.

```javascript
const buf1 = Buffer.alloc(10);
```

From strings, you create a buffer like this:

```javascript
const buf2 = Buffer.from('Hello World!');
```

There are different accepted encoding when creating a Buffer:

- ascii
- utf-8
- base64
- latin1
- binary
- hex

There are three separate functions allocated in the Buffer API to use and create new buffers. In above examples we have seen `alloc()` and `from()`. The third one is `allocUnsafe()`.

```javascript
const buf3 = Buffer.allocUnsafe(10);
```

When returned, this function might contain old data that needs to be overwritten.

### Interactions with Buffer

There are different interactions that can be made with the Buffer API. We are going to cover most of them here. Let us start with converting a buffer to JSON.

```javascript
let bufferOne = Buffer.from('This is a buffer example.');
console.log(bufferOne);

let json = JSON.stringify(bufferOne);
console.log(json);

// find length of buffer
console.log(bufferOne.length);

// Conver buffer to string
console.log(bufferOne.toString('utf8'));
```

The JSON specifies that the type of object being transformed is a Buffer, and its data. Converting an empty buffer to JSON will show us that it contains nothing but zeros.

The output of the above program will be:

```shell
<Buffer 54 68 69 73 20 69 73 20 61 20 62 75 66 66 65 72 20 65 78 61 6d 70 6c 65 2e>

{"type": "Buffer", "data": [84,104,105,115,32,105,115,32,97,32,98,117,102,102,101,114,32,101,120,97,109,112,108,101,46]}

25

This is a buffer example.
```

To examine the size of a buffer, we can use `length` method like we did in the above example. The last method `.toString()` let us convert buffer to a readable string, in our case, the utf-8 encoded. `.toString()` by default converts a buffer to a utf-8 format string. This is how you decode a buffer.
