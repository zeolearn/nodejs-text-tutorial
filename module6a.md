# Authentication and Authorization

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
