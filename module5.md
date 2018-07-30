# Nodejs and the Web

Node.js is a great tool to use to build web applications. In fact, the most common use case of a Node.js in a web application is to provide a server or an API. To build one, we are going to see the terminology and interfaces that we need to use in this tutorial.

## Handling Web Requests

A web server is a software that handles request sent by client like web and mobile browsers using HTTP protocol and in, a response is sent to these clients to fulfill that request. This response can be in form of HTML documents, images, style sheets etc. Node.js is used to write the logic to handle these requests and send the response.

HTTP is a protocol that is used for content delivery. This content delivery includes both the requests and responses. It is a way of communicating using the internet. Node.js has a built-in module to support this and is called HTTP. This module is responsible for all the incoming requests and sending the responses to the client. To use this module, we require:

```js
const http = require('http');
```

## Building a Web Server

This HTTP module supports various several objects like http.server, request, respsonse, etc. Using http.server object's method, http.createServer() we can build our own web server. This function uses a callback to handle the incoming request from the web and takes two arguments: request and response object.

This createServer() function is called once for every HTTP request that is made. Thus, it is also known as request handler as a convention. To listen to requests and send the response back to the client, there is another method that we need to define. It is called listen and it defines a particular port on which our server listens to incoming requests.

Below we create our first web server.

```js
const http = require('http');

http
	.createServer((request, response) => {
		let body = [];
		request
			.on('error', err => {
				console.error(err);
			})
			.on('data', chunk => {
				body.push(chunk);
			})
			.on('end', () => {
				body = Buffer.concat(body).toString();
			});
	})
	.listen(8080, () => {
		console.log('Server is listening on port 8080');
	});
```

[Run](https://repl.it/@amandeepmittal/51)

Since HTTP itself is an instance of EventEmitter class it does have support for `.on` method and has its own events such as `error`, `data` and `end`. The `error` event is emitted when there is an error responding to a particular web request.

In every web server, as we know we have two arguments. The request object is used to learn the details about the incoming request such as the URL, HTTP headers, etc. and response object is used to return data back to the client.

For now, our server does not do anything by which it does receive the request successfully but we do not get any response from that we display on the client. Let us rebuild it and this time we will send an HTML web page every time the user will visit `http://localhost:8080/index.html` url.

We will use an `index.html` file to serve:

```html
<html>

<head>
  <title>First Web Page</title>
</head>

<body>
  <h2>Hello from Nodejs</h2>
  <p>First web server!</p>
</body>

</html>
```

The code for our web server:

```js
const http = require('http');
const fs = require('fs');
const url = require('url');

http
	.createServer((request, response) => {
		let pathname = url.parse(request.url).pathname;

		console.log('Request for ' + pathname + ' received.');

		fs.readFile(pathname.substr(1), functikion(err, data) {
			if (err) {
				console.log(err);
				response.writeHead(404, { 'Content-Type': 'text/html' });
			} else {
				response.writeHead(200, { 'Content-Type': 'text/html' });

				response.write(data.toString());
			}

			response.end();
		});
	})
	.listen(8080, () => {
		console.log('Server running at http://localhost:8080/');
	});
```

[Run](https://repl.it/@amandeepmittal/52)

You can now visit the URL: `http://localhost:8080/index.html` and you will get the result in the form of a web page. You will also get the following output in the terminal whenever that url is used in the web browser.

```shell
Request for /index.html received.
```

In the above example, our callback function first parses URL to read them from where the request is coming. This is an optional part and not every web server needs to have it. Next, using `fs` module we read the file name from the url along with its extension to send it back to the client. If there is no error we use response.wrtieHead to send an HTTP status code and a collection of response headers back to the client.

The status code indicates the result of the request. For example, a 404 error indicates that a page could not be found. Our example server returns the code 200, which indicates success in any web application.

The above example server specifies only the `Content-Type` header. This particular header defines the MIME type of the response. In our case, it is `text/html`.

Next, the server executes several calls to `response.write()`. These calls are used to write the HTML page. Lastly, the response.end() method is called that tells the server that the response headers and body is sent to the client and that the request has been fulfilled.

## Understanding the Need for Web Sockets

WebSockets enables web applications to utilize bi-directional messaging. In simple terms, they allow real-time bi-directional flow of data. It is a protocol in which a server can push information to the client at any time since the connection between the now does not depend on HTTP protocol. The server can send information in form of data at any time to the client and the client does not have to send the request to receive data like in HTTP. The same thing applies to the client. It can send the payload of information at any time on the server does not need a success or an error response status from the server.

Applications like email, instant messaging, document editing, pair programming, and online gaming are some of the examples that use WebSockets. This protocol is currently supported by all major web browsers.

## Real-time Interaction using socket.io

Node.js does not have a built-in implementation of WebSockets module. To build an application we have to use a third party module such as ws, websockets or Socket.io. In this section, we are going to take look at Socket.io.

Socket.io allow us to use web sockets both on front-end and the backend of the application. It is not a protocol in itself but a library that implements WebSocket protocol written in JavaScript. It also a cross-browser library that means it is supported by all major web browsers.

We will build a small application that implements this library. To get started, create an empty directory and run the following command.

```shell
npm init --yes

$ npm install --save socket.io
```

Next create new file called `app.js` with the following code:

```js
const fs = require('fs'),
	http = require('http'),
	socketio = require('socket.io');
const server = http
	.createServer(function(req, res) {
		res.writeHead(200, { 'Content-type': 'text/html' });
		res.end(fs.readFileSync(__dirname + '/index.html'));
	})
	.listen(8080, function() {
		console.log('Listening at: http://localhost:8080');
	});
socketio.listen(server).on('connection', function(socket) {
	socket.on('message', function(msg) {
		console.log('Message Received: ', msg);
		socket.broadcast.emit('message', msg);
	});
});
```

We are building a chat application and the above code is our server side code. We start by requiring the essential modules we need like `fs` and `http` and the `socket.io` module that just installed. Then with `http` module we create a server and open the port 8080. Note that, websocket protocol works as an additional layer over HTTP protocol and this is the reason we need to first create a server using HTTP. After that, we `socketio.listen` to listen on the same host (localhost) and port (8080) thus enabling the connection between the server and the client as bi-directional. `socket.on` opens this connection and socket.broadcat.emit listens to an incoming or outgoing response.

Now are going to implement the front end part. Create a new file called `index.html` and we will be using socket.io with jQuery here.

```html
<html>
<head>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
    <script src="/socket.io/socket.io.js"></script>
    <script>
        $(function(){
            var iosocket = io.connect();
            iosocket.on('connect', function () {
                $('#incomingChatMessages').append($('<li>Connected</li>'));
                iosocket.on('message', function(message) {
                    $('#incomingChatMessages').append($('<li></li>').text(message));
                });
                iosocket.on('disconnect', function() {
                    $('#incomingChatMessages').append('<li>Disconnected</li>');
                });
            });
            $('#outgoingChatMessage').keypress(function(event) {
                if(event.which == 13) {
                    event.preventDefault();
                    iosocket.send($('#outgoingChatMessage').val());
                    $('#incomingChatMessages').append($('<li></li>').text($('#outgoingChatMessage').val()));
                    $('#outgoingChatMessage').val('');
                }
            });
        });
    </script>
</head>
<body>
Incoming Chat: <ul id="incomingChatMessages"></ul>
<br />
<input type="text" id="outgoingChatMessage">
</body>
</html
```

[Run](https://repl.it/@amandeepmittal/53)

The webpage is not very attractive but it serves our purpose of implementing the functionality of WebSocket. Open two separate browser windows and in both windows, you must run the same url: `http://localhost:8080`. Now you will get a chat application interface in both the windows.
