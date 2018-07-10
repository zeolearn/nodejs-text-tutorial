# Debugging Nodejs Application

Debugging is a process of finding and fixing errors in an application. This process is completely applicable when you are developing an application and is not to be used in production mode. Debugging Node.js application is easy and has various methods to do so. The most common one is using `console.log()`. Although this method is suitable for quick debugging of Nodejs modules, it has its drawback such as it slows the development process and prints the output on terminal even in production mode. Another way to debug Nodejs applications are to a tool called Node Inspector.

## Node Inspector

This debugging utility is installed using npm:

```shell
npm install -g node-inspector
```

The interface for the debugger with node inspector is started by:

```shell
node-debug app.js
```

where app.js is the name of your main Node application JavaScript file. This tool works with browser's dev tools and the above command will automatically launch the the default browser with dev tools enabled. Node-insepctor provide following features that can be used in different scenarios:

- to set breakpoints inside the source code
- control code execution by using step in, step out and step over
- to inspect scopes, variables, and object properties
- break on exceptions
- edit variables and functions from the node-inspector source code view
