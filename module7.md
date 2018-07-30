# Scaling Node Applications

Need to scale a Node.js application comes when there is an increase in the workload of your application. Node.js does run on a single thread but that does not mean that it can take advantage of multiple processes that modern day servers tend to support. Most modern day servers use multi-core processors. In this section, we are going to take a look at these topics.

## The Child Process Model

Node.js has child modules in its core API that provide and manage multiple processes. These child processes can communicate with each other. This module allows us to use an Operating System's functionalities using streams and events.

The need to use child process arrives in a Node.js application when there are CPU intensive tasks to be done. To maintain the concurrency using its event loop model, it is recommended that high memory usage tasks or CPU intensive tasks must be done by different processes.

This module is accessible through requiring `child_process` and provides four different methods to work with.

- `spawn`
- `fork`
- `exec`
- `execFile`

## spawn()

The spawn function is used to launch a new process in a way that a unix based command can be passed as an argument. The result of this function is a child process instance that can listen and remove to certain event listeners such as `on`, `disconnect`, `close`, `error` and `exit`.

Every child process also has access to three standard streams such as stdin, stdout, stderr. The spawn() method returns these streams. It uses a stream to process over the data that is passed as an argument. For example, below in our example, we are going to use a unix based command `wc` which counts characters and lines.

```js
const { spawn } = require('child_process');

const child = spawn('wc');

process.stdin.pipe(child.stdin);

child.stdout.on('data', data => {
	console.log(`${data}`);
});

child.on('exit', function(code, signal) {
	console.log('child process exited with ' + `code ${code}`);
});
```

To run the following command, first, run `node spawn.js` where `spawn.js` is the file name and then type a few characters. After you have entered something, press newline character from the keyboard and then press `Ctrl + D`. You will see the following output.

```shell
$ node spwan.js
Hello World
       1       2      12

child process exited with code 0
```

In the above example, we are using a pipe on a readable stream `stdin` into the child process `child.stdin` which is a writable stream. Since every stream emit events, we then add an event listener `data` on `child.stdout` to print the output of the unix command passed as the argument.

## exec()

This is one of the simplest child process methods. It uses a shell (aka console) to execute the command. It can take up to three arguments, the first will be the command itself, second is options which is optional and third is the callback function. Since it uses a shell, when passing the command as the first argument, we can make use of shell's pipe `|` directly.

In the below example, we use a combination of two unix commands to display what we just learned. First, we will read the number of files using `find` and then pipe the number to `wc` to print the count.

```js
const { exec } = require('child_process');

exec('find . -type f | wc -l', (err, stdout, stderr) => {
	if (err) {
		console.error(`exec error: ${err}`);
		return;
	}

	console.log(`Number of files ${stdout}`);
});
```

Output:

```shell
$ node exec.js
Number of files      308
```

## execFile()

This function is similar to `exec()` in working but has a few major differences in how it works. First, it does not invoke a shell to run the command and second, the first argument is not a unix based command but rather a file itself. Thus, it can take four arguments:

- name of the file
- unix command
- options object
- lastly, a callback function to execute

## fork()

This function is a similar to spawn() function we learned about earlier. The only difference between the two is that fork establishes a communication mechanism between the parent and child process using the fork and this communication can be held by exchanging messages via the global object process.

In the example below, we define two files: `parent-fork.js` and `child-fork.js`. On executing parent file which contains a message will be printed and then the child file will send its own values to display.

```js
// parent-fork.js
const { fork } = require('child_process');

const forked = fork('child.js');

forked.on('message', msg => {
	console.log('Message from child', msg);
});

forked.send({ hello: 'world' });

// child-fork.js

process.on('message', msg => {
	console.log('Message from parent:', msg);
});

let counter = 0;

setInterval(() => {
	process.send({ counter: counter++ });
}, 1000);
```

Run the `parent-fork.js` to see the output and how it works.

## Using Cluster Module

Node.js Cluster module offers a set of functions that help us create programs that can use all CPUs available. This is how using in Node.js you can take advantage of multi-core systems and use scalability to your leverage. Using a cluster module, you can fork a parent module into multiple child processes.

The cluster module comes with the built-in modules of Node.js and can require with the following syntax:

```js
const cluster = require('cluster');
```

We can mimic a cluster module example on your local machines too. Create a file called `cluster.js`. We will start by requiring the dependencies. Then, we will first check of the process running is master or a child one. Remember that, the child processes are forked from the master and new processes are created. When a child process is created and gets executed, it then executes the `if` statement (see example below). One of the differences here is that for the child process the value of `cluster.isMaster()` is false, so they end running the childProcess function.

```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
	masterProcess();
} else {
	childProcess();
}

function masterProcess() {
	console.log(`Master ${process.pid} is running`);

	for (let i = 0; i < numCPUs; i++) {
		console.log(`Forking process number ${i}...`);
		cluster.fork();
	}

	process.exit();
}

function childProcess() {
	console.log(`Worker ${process.pid} started and finished`);

	process.exit();
}
```

To run this program, you can run the command `$ node cluster.js` and you will get the following output.

```shell
Master 7013 is running
Forking process number 0...
Forking process number 1...
Forking process number 2...
Forking process number 3...
Worker 7014 started and finished
Worker 7016 started and finished
Worker 7017 started and finished
Worker 7015 started and finished
```

The communication between a child process and its master is done through a channel which is called IPC channel.
