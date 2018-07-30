# Introduction to Node.js

Node.js is a server-side platform for developing web servers, web applications, network servers, and general purpose programming. It is designed in such a way that it handles scalability in network applications leveraging programming language such as JavaScript on a server, asynchronous I/O, and follows other asynchronous paradigms.

Why Node.js is a server side platform and not a programming language in itself? The reason being is that it is built on Google JavaScript Engine technology called V8 on which browsers like Chrome works. It is developed by Ryan Dahl in 2009 and since then the companies like PayPal, Netflix, GoDaddy, Uber, Microsoft have adapted it in their development process especially in web programming. It is also open source and is maintained by a large community across the globe.

I assume, if you are not familiar with jargons like asynchronous I/O, we will be covering what it later in this article. One question you must ask since you are reading this tutorial is that why Node.js is popular?

## Installing Node.js

Before we dive deep into Node.js and what does it mean by asynchronous I/O, let us first install Node.js on our local machines. Based on your operating system, you can install Node.js by visiting https://nodejs.org. It will provide you an installer package through which you can install the latest version (current) or the stable (LTS) version. To verify that Node.js has installed correctly open your terminal.

```shell
node --version

# output
v8.11.2
```

You will be prompted with the version number you choose to install and this does verify that Node.js has installed successfully. Now, we will create a small program to see how it works. Create a file called `index.js`. We will be writing and running our first program in Node.js.

```js
console.log('Hello from NODEJS!');
```

You can run this program from your terminal by typing the below and you will get the same output that you passed as a string in JavaScript's console.log() function.

```shell
node index.js

# output
'Hello from NODEJS!'
```

[Run this program](https://repl.it/@amandeepmittal/m10)

## Event Loop in Node.js

The sole reason behind by Node.js' popularity is its execution model which runs on a single thread. Now, what do we mean by a single thread? Consider this scenario that you are at a fast food joint and there is a queue of individuals who are waiting to order their meals before you. The cash register takes one order at a time. Traditionally, after taking this order he can make the person wait who just placed an order and is standing in front of the queue for his order to complete. This will consume a lot of time and the rest of the members in a queue will have to wait not only to place the order but also to receive it.

This is how a traditional web server works. This concept is often called blocking Input/Output (I/O) which refers to the idle of time of other members (or operations or requests in terms of the web) just like in our fast food joint. To overcome this problem, Blocking I/O servers came up with multiple threads. In comparison to our example, this means that for every other person who wants to place an order, we assign a cash register. This will consume a lot of resources in the fast joint, just like in a computer this will consume a lot of memory (RAM).

Node.js uses Non-Blocking and asynchronous I/O which make possible to run it on a single thread hence, making it consume fewer resources and a cost effective solution than a traditional one. In terms of the fast food joint, this will work like this. A member of the queue places an order and is then assigned a number by the cash register to wait for his order to complete. Meanwhile, the cash register can take more orders from other members of the queue and behind him, other workers will serve these orders simultaneously. This way no one has to wait in the queue idle and only when an operation completes, the person who's ordered gets complete is called.

Similarly, in Node.js when a request comes from the browser, it is received by an event loop which plays the role of the cash register here. The members standing in the queue is equivalent for the requests waiting in the queue. This queue is called an event queue which receives all the orders or requests concurrently. Then the event loop assigns the number to this request in the form of a callback function or a handler function and will only invoke that function when the operation is completed and it has some response to fulfill the request is received. In this way, there is no idle time between two operations in the event queue.

In summary, Event Loop is responsible to handle and execute these callback functions who are asynchronous in nature. This paradigm is also known as Non-blocking I/O.

## Writing Asynchronous code

We just learned what role an asynchronous callback function plays in. To understand it better we must take a look at what these callback functions are in detail with a code example in this section.

The role of a callback function (you can also call the handler function but the convention is callback) is simple. A callback function is nothing but a plain JavaScript function that accepts arguments and instead of returning the result immediately, it will return the result when the process of calculating the result is done. Just like in the previous example, the fast food joint can only serve your order of fries when the fries are ready.

To make this more clear, let us write our second program. Let us name this file `fast-food.js`.

```js
function handleCustomerOrder(order) {
	setTimeout(() => {
		console.log(`Order of ${order} completed and served!`);
	}, 1000);
}

handleCustomerOrder('FRIES');

console.log('Order Placed!');
```

[Run this program](https://repl.it/@amandeepmittal/module1-1)

To run this program type below command and you will get the following output:

```shell
node fast-food.js

# output

Order Placed!
Order of FRIES completed and served!
```

You see how the last line of our program is executed first? This is because we are using a JavaScript Timer function called setTimeout() which simulates in our scenario the time consumed by the fast food joint after placing the order and until the order is completed. In the above example, `handleCustomerOrder` is our callback function that receives the order (or in terms of web, receive requests from client) and responds to those requests. You can change the line `handleCustomerOrder('FRIES');` and try outputting different things. From this, we can observe that the order in which functions declared or execute is not always top-to-bottom in asynchronous programming.

## Alternatives to Node.js

There are many alternatives available to Node.js these days in the world of programming. Popular ones are:

- Go
- Elixir
- Haskell
- Java
- Scala
- Python

Some of them follow Blocking I/O but with the recent development of engines like Akka for Java and Scala, these traditionally running programming languages can now make use of an event-driven asynchronous model. Go is another example of complete Non-blocking I/O programming language.
