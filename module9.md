# Debugging Nodejs Application

Debugging is a process of finding and fixing errors in an application. This process is completely applicable when you are developing an application and is not to be used in production mode. Debugging Node.js application is easy and has various methods to do so. The most common one is using `console.log()`. Although this method is suitable for quick debugging of Nodejs modules, it has its drawback such as it slows the development process and prints the output on terminal even in production mode.

There are more efficient ways to debug a Nodejs application. Available tools are:

- `node inspect`
- Chrome DevTools

## node inspect

Node.js includes debugging utility to run the program in debugging mode using a command line interface. This can be accessed using `node inspect app.js` file. For example, we will use the below code. Create a file called app.js with code below.

```js
const hello = function() {
	debugger;
	return 'hello from module';
};
```

Now in command line interface, run

```shell
node inspect app.js
```

You will see the following output:

![1](images/module-9/1.png)

Inserting the keyword `debugger` into our code will enable a breakpoint at that position in the code where we want the debugger to stop itself. The debugger by default starts or breaks at the start and then wherever it finds the first `debugger` statement.

The command line interface comes with keys that we press from the keyboard in order to interact with the debugger. These are the general keys that can be used:

- cont, c - Continue execution
- next, n - Step next
- step, s - Step in
- out, o - Step out
- pause - Pause running code

The debugger take these keys as commands inside the terminal:

```shell
debug> s
```

## Chrome DevTools

Google's browser Chrome and Nodejs are built using same JavaScript engine called V8. Thus, chrome has an internal support for debugging a Nodejs application. The previous technique is a bit confusing and does not support debugging large or advanced usage.

To see this in action, we will use our previous example of `app.js` but this time the command be same.

```shell
node inspect app.js
```

After running the Nodejs debugging command you will be prompted with a familiar command line interface as before. This time go to your chrome based browser and open `chrome://insepct`. Here, you will be be able to access GUI interface to debug a Nodejs app or module using Chrome DevTools.

![2](images/module-9/2.png)

First click on the configure button and make sure that the target host and post numbers are valid. That is, they are the same as shown in commad line:

```shell
Debugger listening on ws://127.0.0.1:9229/2ebe0e3a-7056-404a-aee3-e57fa40aa30e
```

Next, click on the `Open dedicated DevTools` button and it will popup the following window.

![3](images/module-9/3.png)

From here you can work on just like the commond line interface instead of typing commands you can use buttons on the right corner and see everything in action.

The advantage of using this technique to debug Node.js app is that it provide following features that can be used in different scenarios:

- to set breakpoints inside the source code
- control code execution by using step in, step out and step over
- to inspect scopes, variables, and object properties
- break on exceptions
- edit variables and functions from source code view
