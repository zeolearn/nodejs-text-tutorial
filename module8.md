# Node.js Eco System

In previous module we learned that Node.js has three types of packages or modules. In this tutorial, we are going ot discuss about the third party modules that caan only be accessed through `npm`.

## Node Packages

`npm` is a noe package manager. It comes with Node.js installation and is completely accessible through command line once installed. It provides two main functionalities to a Node.js developer:

- Online repositories for node.js packages/modules which are searchable on `npmjs.com`.

- Command line utility to install Node.js packages, do version management and dependency management of Node.js packages.

`npm` can install packages in local or global mode. By default, NPM installs any dependency in the local mode. In local mode, it installs the package in a `node_modules` folder in the root working directory of your project. To install the package locally, we can use --save flag or its shorthand `-S`.

```shell
npm install --save express
```

Global packages are installed in `/lib/node_modules/` of your computer. This means you would have to use sudo to install packages globally, which could cause permission errors when resolving third-party dependencies, as well as being a security concern.

To install a gloabal package, you have to either use --global flag or its shorthand -g.

Local modules are further divided into two types of depenedencies: `devDepenednecies` and `dependencies`. The difference between these two, is that `devDependencies` are modules which are only required during development, while `dependencies` are modules which are required at run the project on a real server. To save a dependency as a devDependency on installation we need to do an `npm install --save-dev`, instead of just an `npm install --save`.

A nice shorthand for installing a devDependency that I like to use is `npm i -D`. The shorthand for saving a regular dependency is `-S` instead of `-D`.

```shell
npm install -global express-generator
```

### Managing Dependencies with package.json

When you install packages locally, you need a package.json file. To generate one you can do that by using `npm init` command. It will prompt up with some questions that by pressing enter you can keep the default values.

```shell
$ npm init
package name: (project)
version: (1.0.0)
description: Demo of package.json
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
```

Think of `package.json` as the keeper of all dependencies or manifestation of a Node.js project. If you want a quicker way to generate a package.json file use `npm init --y`.

List of Common Attributes in a `package.json` file:

- name − name of the package

- version − semantic version of the package

- description − description of the package

- homepage − homepage of the package

- author − author of the package

- contributors − name of the contributors to the package

- dependencies − list of dependencies. NPM automatically installs all the dependencies mentioned here in the node_module folder of the package.

- devDependencies - list of all development specific dependencies

- repository − repository type and URL of the package

- main − entry point of the package

- keywords − keywords

- license - a license for your package so that people know how they are permitted to use it, and any restrictions you're placing on it.

- scripts - The "scripts" property is a dictionary containing script commands that are run at various times in the lifecycle of your package.

- config - object that can be used to set configuration parameters used in package scripts that persist across upgrades.

An example:

```json
{
   "name": "express",
      "description": "Fast, unopinionated, minimalist web framework",
      "version": "4.11.2",
      "author": {

         "name": "TJ Holowaychuk",
         "email": "tj@vision-media.ca"
      },

   "contributors": [{
      "name": "Aaron Heckmann",
      "email": "aaron.heckmann+github@gmail.com"
   },

    ],
   "license": "MIT", "repository": {
      "type": "git",
      "url": "https://github.com/strongloop/express"
   },
   "homepage": "https://expressjs.com/", "keywords": [
      "express",
      "framework",
      "sinatra",
      "web",
      "rest",
      "restful",
      "router",
      "app",
      "api"
   ],
   "dependencies": {
      "serve-static": "~1.8.1",

   },
   "devDependencies": {
      "jade": "~1.9.1",
   },
   "engines": {
      "node": ">= 0.10.0"
   },
   "files": [
      "LICENSE",
      "History.md",
      "Readme.md",
      "index.js",
      "lib/"
   ],
   "scripts": {
      "test": "mocha --require test/support/env
         --reporter spec --bail --check-leaks test/ test/acceptance/",
      "test-cov": "istanbul cover node_modules/mocha/bin/_mocha
         -- --require test/support/env --reporter dot --check-leaks test/ test/acceptance/",
      "test-tap": "mocha --require test/support/env
         --reporter tap --check-leaks test/ test/acceptance/",
      "test-travis": "istanbul cover node_modules/mocha/bin/_mocha
         --report lcovonly -- --require test/support/env
         --reporter spec --check-leaks test/ test/acceptance/"
   },

}
```

The above is a `package.json` of `express` module.

If not using a specific flag such as -s or --dev and installing a module with command `npm install express` will install the module in `node_modules` folder locally but the `package.json` that keep records of every dependency we are using in a project will not be updated with our addition. Thus, the package will be development specific, will not be installed in runtimme environment. Make sure, you always use a proper flag and keep `package.json` file updated.

## Packages of Interest

Below is a list of some commonly used Packages for a Node.js project.

- Express: a Node.js framework to create a web server
- axios: for AJAX Call
- request: HTTP client
- mocha: test runner library
- chai: test assertion library
- jest: test runner library
- moment: provide a lot of functionalities over native Date Object
- grunt: javascript task runner
- pm2: process manager for production applications
- socket.io: websocket library for real time application communication
- passport: authentication strategies for Node.js
- nodemailer: email management within Node.js applications
- hapi: robust server side framework to create Robust APIs

## Deciding Factors
