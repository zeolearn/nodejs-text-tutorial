# Debugging Nodejs Application

Debugging is a process of finding and fixing errors in an application. This process is completely applicable when you are developing an application and is not to be used in production mode. Debugging Node.js application is easy and has various methods to do so. The most common one is using `console.log()`. Although this method is suitable for quick debugging of Nodejs modules, it has its drawback such as it slows the development process and prints the output on terminal even in production mode.

There are more efficient ways to debug a Nodejs application. Available tools are:

- node-inspect
- Chrome DevTools

## Chrome DevTools

Google's browser Chrome and Nodejs are built using same JavaScript engine called V8. Thus, chrome has an internal support for debugging a Nodejs application.

After running the Nodejs server, open `chrome://insepct. Click on the configure button and make sure that the target host and post numbers are valid.

A chrome based debugging module provides following features that can be used in different scenarios:

- to set breakpoints inside the source code
- control code execution by using step in, step out and step over
- to inspect scopes, variables, and object properties
- break on exceptions
- edit variables and functions from source code view

You can also use its alternative which comes in the form a chrome extension.
