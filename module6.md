# Building Web Applications with Expressjs

Using Node.js as a backend for web applications and websites help developers to start working on their application. In this tutorial, we are going to look into Expressjs which is a Node.js framework for web development that comes with features like routing and rendering and support for REST APIs.

## Installing Express

Expressjs is developed by T.J. Holowaychuk and is not maintained by Node.js foundation and other open source developers. It is the most popular Node.js framework. It requires a minimum setup to bootstrap an application and is fast, and unopinionated at the same time. It does not have its own philosophy, and nor does it enforces one on how servers should be built.

Now that we have a `package.json` file, we can install Express by running the command:

```shell
npm install --save express
```

We can confirm that Express has correctly installed by two ways. First way is that there will be a new JSON object in `package.json` file named `dependencies` under which the Express module exists:

```json
{
	"name": "express-web-server",
	"version": "0.1.0",
	"description": "",
	"main": "index.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"keywords": [],
	"license": "MIT",
	"dependencies": {
		"express": "4.16.0"
	}
}
```

Second way to verify is that a new folder called `node_modules` suddenly appears in the root of our project directory. This folder stores the dependencies we install locally to our web server project.

## Building a Simple Server with Express

In this section we will build a simple server using Express. It is a bare-minimum application that you can develop using this Node.js framework. The first line of our code is using the `require` function to include the `express` module. Before we start using Express, we need to define an instance of Express module that handles the request and response from the server to the client. In our case, it is the constant `app`.

`app.get()` is a function that tells the server what to do when a `get` request at the given route is called. It has a callback function `(req, res)` associated that listens to the incoming request `req` object and respond accordingly using `res` response object. Both `req` and `res` are made available to us by the Express framework.

Create an `app.js` file next to `package.json` and write the following code.

```javascript
const express = require('express');

const app = express();

app.get('/', (req, res) => res.send('Hello World!'));

app.listen(3000, () => console.log('Server listening on port 3000!'));
```

To start the server, go to your terminal and type:

```shell
node app.js
```

This bare-minimum server listens on port 3000. We make a request through our browser on `http://localhost:3000` and our server will respond with `Hello World`.

## Routing

Routing in server application refers to endpoint made from a client to get the proper response. An endpoint consists of a URI which is a path such as `/user` and an HTTP method such as GET, POST, etc.

Syntax for an Express router is:

```js
app.HTTPmethod(path, callback);
```

Each route has a callback function that takes request and response as arguments. In our example, we define a home route `/`:

```js
app.get('/', (req, res) => res.send('Hello World!'));
```

This is also known as routing paths.

## Parameters and Queries in Routing

Routing parameters are URL segments that are defined using a name segment to capture different variable values. These values are dynamic in nature and can change accordingly.

For example, we have a route that response back with the information regarding to different book. Each book has a unique id that can be referred in thr URL to access them. In this case, the client will request using a URL such as `/books/124` and in server our route will have a dynamic route such as `/books/:id`. This `:id` is the named segment that can have different values as route parameters which is made of valid ([A-Za-z0-9_]) characters.

In our previously build server we will add two dynamic routes. First, will send the information regarding a book and second will use symbol such as `*` as a wildcard to indicate that no route exist in our server. Open app.js to modify:

```js
const express = require('express');

const app = express();

app.get('/', (req, res) => res.send('Hello World!'));

app.get('/books/:id', (req, res) => {
	res.send(`You are now accessing information of book ${id}`);
});

// this must be defined as the last route
app.get('*', (req, res) => {
	res.send('404! This is an invalid URL.');
});

app.listen(3000, () => console.log('Server listening on port 3000!'));
```

Start the server by using `node app.js` command and visit an abstract URL from the browser such as `http://localhost:3000/abcd` and you will notice that you get a 404 message.

## Building Views using Template Engine

Template engines are libraries that allow us to use different template languages. A template language is a special set of instructions (syntax and control structures) that instructs the engine how to process data. Using a template engine is easy with Express.

The popular template engines such as Pug, EJS, Swig, and Handlebars are compatible with Express. However, Express comes with a default template engine, Jade, which is the first released version of Pug. To demonstrate how to use a Template Engine, we will be using Pug.

```shell
npm install --save pug
```

We will first create a new directory called views in the root of our project and then we will add the first layout in a file called `hello.pug`.

```js
doctype html
  html
    head
      title="Hello from Pug"
    body
      p Hello World!  
```

To run this page, we will add the following configuration and route to our application.

```js
// add this before any route declaration
app.set('view engine', 'pug');
app.set('views', './views');

app.get('/hello', (req, res) => {
	res.render('hello');
});
```

Express has a method `res.render()` to autmotically identify the template engine we are using and convert that particular template engine file into HTML for the browser to display.

If you start the server and visit the route `http://localhost:3000/hello` you will see the output `Hello World` on a web page.

## Using Blocks for Layout

We will define a layout file in the views directory so that we do not have to write boiler plate code such as `doctype html` in every `.pug` file. `block` is the keyword that allows us to connect a web page to an already existing layout.

Create a layout.pug file.

```pug
doctype html
  html
    head
      title="Server App"
    body
      block content
```

Now, create another file called index.pug.

```pug
extends layout

block content
  h1 Expressjs
  p Welcome to Expressjs
```

Also, modify `hello.pug`

```pug
extends layout

block content
  h1 Expressjs
  p Hello World
```

Now we will modify our routes in `app.js`:

```js
const express = require('express');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views');

// modify this route
app.get('/', (req, res) => res.render('index'));

app.get('/hello', (req, res) => {
	res.render('hello');
});

app.get('/books/:id', (req, res) => {
	res.send(`You are now accessing information of book ${id}`);
});

// this must be defined as the last route
app.get('*', (req, res) => {
	res.send('404! This is an invalid URL.');
});

app.listen(3000, () => console.log('Server listening on port 3000!'));
```

## Displaying Data

The advantage of using a template engine is that we can send data to reflect on a web page from inside our route. This is helpful when using dynamic routes. We will create a new view called `books.pug`.

```pug
extends layout

block content
  h1 Book #{id}
  p #{message}
```

To display the data on the web page when it gets rendered we will modify our only dynamic route:

```js
app.get('/books/:id', (req, res) => {
	res.render('books', {
		id: `${id}}`,
		message: `You are now accessing information of book ${id}`
	});
});
```

## Working With Forms

In this section, we will create a form using Pug and Express route. To start, create a file called `form.pug` in `views` directory.

```pug
extends layout

block content
  form(name="add-bookTitle", method="post")
        div.input
            span.label Book Title
            input(type="text", name="title")
        div.actions
            input(type="submit", value="add")
```

To make this form working, we will add a POST route in our Express app. Note that, POST request is to handle forms and other data that is incoming from a client. You cannot use GET in this case.

```js
app.post('/form', function(req, res) {
	// this will print the title of the book on our console
	console.log(req.body.title);
	res.render('form');
});
```

## Serving Static Files

Often in a server app, there are going to be file such as scripts, stylesheets or images that are going to remain the same. To send them to the client, we use a concept of serving static files.

Express support this concept by default. All you need is to create a public directory and put some files in it such as an image file. We use a built in function called `express.static` to serve this folder on the client. By default, Express does not allow to serve static files. We have to use this middleware function.

```js
const express = require('express');

const app = express();

app.set('view engine', 'pug');
app.set('views', './views');

// modify this route
app.get('/', (req, res) => res.render('index'));

app.get('/hello', (req, res) => {
	res.render('hello');
});

app.get('/books/:id', (req, res) => {
	res.send(`You are now accessing information of book ${id}`);
});

app.post('/form', function(req, res) {
	// this will print the title of the book on our console
	console.log(req.body.title);
	res.render('form');
});

// this must be defined as the last route
app.get('*', (req, res) => {
	res.send('404! This is an invalid URL.');
});

// add this after the route config
app.use(express.static('public'));

app.listen(3000, () => console.log('Server listening on port 3000!'));
```

## Working with Cookies and Sessions

Most web applications maintain and store user data in sessions. Basically, a session is just a place to store data that can be used with a client's requests, sometimes multiple requests.

Consider this scenario. You create a new Twitter account and after , you access your Twitter account for some amount of time and then you close your browser. Next day you open the web browser and visit Twitter's home URL and but now you do not have to login. How? The browser has stored your credentials as tokens or sessions.

There are different ways to store a session:

- application memory
- cookie
- memory cache
- database

To use session in an Express app, you install the package:

```shell
npm install --save express-session
```

To create a simple session you add a function after importing the module to configure its settings.

```js
const session = require('express-session');

// before any route
app.use(
	session({
		session: 'SessionSecret',
		cookie: { maxAge: 60000 }
	})
);

// TO Access the session you use req.session
app.get('/', (req, res) => {
	let sessData = req.session;
	sessData.someAttribute = 'myName';
	res.send('Request Fulfilled');
});

app.get('/readsession', (req, res) => {
	let someAttribute = req.session.someAttribute;
	res.send(`This will print the attribute I set earlier: ${someAttribute}`);
});
```

In the above example, after enabling session middleware function, we are setting some data that will be sent on the route `/`. Using this, we can read the data in the other route `/readsession`.

## Authentication and Authorization

Authentication is a common real time practice in modern web applications and mobile applications. Most of the work is done on the server side and that is the reason we are going to learn how to do it in this section. We will be using Passportjs which is a middleware function that offers different implementation strategies which let us use them to avoid a lot of boilerplate code and behind the scenes connections. It uses session or local storage to store objects that contain user information.

We will also be using Token strategy, an implementation of its called JSON Web Tokens (JWT, pronounced as JOT). Since HTTP is a stateless protocol, whenever a user logs into a web application, the client has to send a piece of information about the user on every request. The server side then has to perform authentication on every request by checking the login status of the user, even the user is already logged in. To avoid this, JWT is used which an encrypted object of data that uses some unique identifier to know which user logged in. This encrypted data is used in request-response cycle.

To get started, we need to install below dependencies:

```shell
npm install --save passport passport-local passport-jwt jsonwebtoken
```

In our , we will start at the point where a user logs in. At this point, the backend will create a signed token (encrypted JWT) and return it to the client as a response. From there, the client will store this token in localstorage and send it back on every request that needs authentication. All requests on server that need authorization are going to be passed through middleware which will be provided by Passportjs.

Let us implement the business logic of login first. Create a file called `passport.js`.

```js
const passport = require('passport');
const passportJWT = require('passport-jwt');
const JWTStrategy = passportJWT.Strategy;
const ExtractJWT = passportJWT.ExtractJwt;

const LocalStrategy = require('passport-local').Strategy;
passport.use(
	new LocalStrategy(
		{
			usernameField: 'email',
			passwordField: 'password'
		},
		function(email, password, cb) {
			return UserModel.findOne({ email, password })
				.then(user => {
					if (!user) {
						return cb(null, false, { message: 'Incorrect email or password.' });
					}
					return cb(null, user, { message: 'Logged In Successfully' });
				})
				.catch(err => cb(err));
		}
	)
);

passport.use(
	new JWTStrategy(
		{
			jwtFromRequest: ExtractJWT.fromAuthHeaderAsBearerToken(),
			secretOrKey: 'your_jwt_secret'
		},
		function(jwtPayload, cb) {
			return UserModel.findOneById(jwtPayload.id)
				.then(user => {
					return cb(null, user);
				})
				.catch(err => {
					return cb(err);
				});
		}
	)
);
```

In the above example, the `UserModel` is coming from NoSQL database. The first strategy is the local strategy that allows us to authroize the user on the basis of correct login credentials. Next we create another strategy, `JWTStrategy` to implement JWT tokens.

Next step is to require this file as configuration middleware in our `app.js`. First, we require needed dependencies and then we define a post route `/login` that will allow the client to make request whenever a user submits his login credentials through a login form. We are also passing `session: false` because we are not storing user information inside JWT token in browser's session. You can make it this option true to use browser's session storage instead of localStorage.

```js
const express = require('express');
const jwt = require('jsonwebtoken');
const passport = require("passport”);

require('./passport');

const app = express();

// create routes related to authentication
app.post('/login', (req, res, next) => {
    passport.authenticate('local', {session: false}, (err, user, info) => {
        if (err || !user) {
            return res.status(400).json({
                message: 'Something is not right',
                user   : user
            });
        }
       req.login(user, {session: false}, (err) => {
           if (err) {
               res.send(err);
           }
           const token = jwt.sign(user, 'your_jwt_secret');
           return res.json({user, token});
        });
    })(req, res);
});

app.get('/user/profile', passport.authenticate('jwt', {session: false}), (req, res, next) => {
    res.send(req.user);
});
```

To get user profile, we pass `passport.authenticate()` middleware function provided by the Passportjs library itself in which we pass the `jwt` as option to use JSON Web Tokens.

## Handling Errors

Expressjs provides a standard middleware function to handle errors across the server side of the web application. Error handling in Expressjs is referred to catching and processing errors that occur both asynchronously and synchronously. The default error handling function takes four arguments and this how it is understood by the Expressjs that this function is an error handling function. These four arguments include req, res, next and an extra argument that is used is called error.

```js
function errorHandler(err, req, res, next) {
	if (res.headersSent) {
		return next(err);
	}
	res.status(500);
	res.render('error', { error: err });
}
```
