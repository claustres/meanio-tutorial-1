# MEAN.IO (1/3)

This first article in a series on MEAN.IO will allow us to introduce the main concepts of the framework and get a functioning development environment for practical exercises for future articles. This framework starting to know a "boom" (over 6000 and 2000 recommendations forks on GitHub) Thank Program! allow me the opportunity to introduce one of the first French-language resources on the subject.

## Introduction

[MEAN.IO] (http://mean.io/) is a Javascript framework "full-stack" to quickly create a single-page web application implementation type (SPA) with [MongoDB] (http: // www .mongodb.org /), [Node.js] (http://www.nodejs.org/) [Express] (http://expressjs.com/) and [angularjs] (http: // angularjs. org /) (MEAN). The term "full-stack" indicates that helps manage all layers of the application in Javascript, since the database, via the back-end to front-end API. We will see that MEAN.IO is a framework under "framework" to develop an application rather than the entire direction of libraries / components.

Although this article discusses the use of technologies MEAN (and others like [Mongoose] (http://mongoosejs.com/) or [Bootstrap] (http://getbootstrap.com/)) through their use in MEAN.IO, I advise you to acquire the prerequisites basics of programming in these frameworks / libraries.

## Installation and Update

### Prerequisites

To simplify my life I used to use pre-packaged solutions for the development or production infrastructure. For MEAN.IO I use those [Bitnami] (https://bitnami.com/stack/mean) available in Windows, Linux Ubuntu or type cloud like Amazon. They also have the advantage of integrating Apache, an administration tool for MongoDB named [RockMongo] (https://github.com/iwind/rockmongo) and [Git] (http://git-scm.com/ download /) you will need to MEAN.IO. Currently I work with v0.12.4 Node.js, MongoDB v3.0.3, v0.5 MEAN.IO.

Some dependencies MEAN.IO use [node-gyp] (https://github.com/TooTallNate/node-gyp) (such as MongoDB drivers) must therefore have prerequisites in Python Version 2.7.x and a C ++ compiler GCC as Linux or Microsoft Visual Studio C ++ under Windows.

The tool used by MEAN.IO to perform the different tasks necessary for the development, testing and production start is [gulp] (http://gulpjs.com/). It should also install [Bower] (http://bower.io/) for dependency management front-end side of npm being used Node.js backend side. So you will proceed as follows:
`` `
npm install -g gulp
npm install -g bower
`` `

Although it is possible to clone the GitHub directly deposit MEAN.IO, it is advisable to go through the dedicated command-line tool named [mean-cli] (https://www.npmjs.com/package/ mean-cli) by installing it via:
`` `
npm install -g mean-cli
`` `

### Installing and Configuring MEAN.IO

It is then possible to initialize an application in a folder with the following commands:
`` `
init mean folder_name
cd folder_name
npm install
`` `

> MEAN.IO includes a script (see file * tools / scripts *) performed during the install command that installs the back-end and front-end dependencies of all application modules, so there is no need to run the `classic bower install`

Similarly it will be possible (we'll get to that later) to initialize a package (ie a module) application via:
`` `
mean package package_name
`` `

The first thing to do is to create a database with a user with access rights for that use MongoDB shell by running through your administrator account database (configured at installation):
`` `
mongo admin --username root root --password
db = db.getSiblingDB ('mean-dev')
db.createUser ({user: "mean-dev" pwd "mean-dev", Role: ["ReadWrite", "dbadmin"]})
`` `
You must then change the default configuration MEAN.IO to use this base and this user, why open the development.js * * * config file in the folder / env * and change the value of the key ** db ** :
`` `javascript
module.exports = {
  db: 'mongodb: // mean-dev: mean-dev @ localhost: 27017 / mean-dev'
  debug: true,
  ...
`` `
To start the server simply run the command `gulp` then log on with your browser at [http: // localhost: 3000 /] (http: // localhost: 3000 /). You can create your first user to connect to the application through the entrance * * Join in the menu bar. At this point you should have two entries available in the menu bar in addition to the menu associated with your profile (see Figure 1). To make the user created application administrator using the following command:
`` `
email@google.com --addRole mean user admin
`` `
Once connected a new sidebar appears on the left and contains the different configuration options available in administrator mode.

! [Figure 1] (Figure1.png "Figure 1: Home application MEAN.IO default once logged in (non-administrator mode the vertical menu on the left is not apparent)")

> ** Tips & Tricks **: run the following command in the root folder of the application to get all the information about MEAN.IO version (useful for example when submitting bugs to the tracker)
> `` `
> Mean status
> `` `

> ** Tips & Tricks **: Windows sometimes nesting node modules poses problems too long ways and it becomes possible to erase the node_modules * * folder, for that I use the tool [rimraf] ( https://github.com/isaacs/rimraf):
> `` `
> -g Npm install rimraf
> Rimraf node_modules
> `` `

### Update MEAN.IO

Finally the back of a MEAN.IO application is actually a Git repository, since the `mean init` command uses Git to install the template code. She created a default remote repository (remote) named ** ** upstream. To stay updated with the latest version so just do:
`` `
git pull upstream master
npm install
`` `

Maintain prerequisites can sometimes help fix some bugs during the installation:
`` `
npm update -g gulp
npm update -g bower
`` `

## Operation

The main objective of MEAN.IO is to provide a framework in terms of software factory and base structure, and an extension mechanism, to develop an application. This requires the development of a package or a module that is a set of features (back-end services + front-end components) to extend the canvas. As it is not necessarily required for a given application, it fits within the canvas according to a defined rule if needed such a "plugin" (ie "graft").

### Runtimes

Conventionally MEAN.IO an application can be launched in different environments:

- ** ** Development: environment used during development
- ** Test ** environment used to run the tests
- Production ** ** used in the production environment

To set the environment it is possible to go through the environment variable ** ** NODE_ENV before starting the server:
`` `
set NODE_ENV = Production
gulp
`` `
It is also possible to directly invoke the server through the environment setting:
`` `
gulp Production
`` `

The default tasks are performed by gulp after the environment are:

- ** ** Development: execution of [JSHint] (http://jshint.com/) [CSSLint] (https://github.com/CSSLint/csslint) [Less] (http: // lesscss. org /), [CoffeeScript] (http://coffeescript.org/) code and the server start in debug mode
- ** Test **: execution of [Karma] (http://karma-runner.github.io/) and [Mocha] (http://mochajs.org/)
- ** ** Production: minification of CSS / JS dependencies and launch the server in production mode (multithreaded)

The server side logs output format is also dependent on the environment, [tiny] (https://github.com/expressjs/morgan#tiny) developing and [combined] (https://github.com/expressjs/ # morgan combined) in production:
`` `
// Development environment
GET /system/views/index.html 2379 304 ms - -
GET / admin / menu / main ms 304 8687 - -
// Production Environment
:: 1 - - [22 / Mar / 2015: 13: 13: 42 +0000] "GET /modules/aggregated.js?group=header HTTP / 1.1" 200 0 "http: // localhost: 3000 /" "Mozilla /5.0 (Macintosh, Mac OS X Intel 10_10_2) AppleWebKit / 537.36 (KHTML, like Gecko) Chrome / Safari 41.0.2272.101 / 537.36 "
:: 1 - - [22 / Mar / 2015: 13: 13: 42 +0000] "GET / HTTP / 1.1" 200 - "-" "Mozilla / 5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit / 537.36 (KHTML , like Gecko) Chrome / Safari 41.0.2272.101 / 537.36 "
`` `

### Anatomy of an application

Once initialized the back of a MEAN.IO app has the following structure:
`` `
Application folder
--- Bower_components contains the dependencies Bower (front-end)
--- Config: configuration files (Express, database, etc.)
    --- Ca.
    --- Middleware
--- Gulp: files describing the various tasks gulp depending on the environment
--- Logs: Contains the logs output Node.js
--- Node_modules: contains Node.js dependencies (back-end)
--- Package: contains all application modules
    --- Core: core modules included with MEAN.IO
    --- Custom: modules specific to the application logic
--- Tools:
    --- Scripts
    --- test
`` `

At the root is found all the configuration files for the software tools that are factory npm, bower, gulp, jshint, karma, protractor, etc. ** ** The config directory contains all configuration files, including * * express.js and middleware ** ** folder (logging, etc.) Express and folder ** ** for approx own options to MEAN.IO. Options common to all environments are stored in the file ** env / ** all.js the environment-specific options are stored in a file with the name of the environment in the folder ** ** approx. A non-exhaustive list of configuration options is as follows:

- ** ** Root: the path to the application root
- ** Db **: the URL to access the database, may include a login / password of the form * mongodb: // username: password @ host: port / base *
- ** ** Hostname: the name of the host
- Http ** / ** https.port: the port number to use HTTP or HTTPS
- ** ** Logging.format: Format [morgan] (https://github.com/expressjs/morgan#predefined-formats) server logs
- ** ** App.name: the name of the application
- ** ** Aggregate: true / false to enable / disable aggregation (useful in debug mode)
- Secret ** ** private key for securing via JWT (see below)
- Information to connect via social networks

### Anatomy of a module

The file of a package or MEAN.IO module has the following structure:
`` `
Module folder
--- Docs: contains the API documentation (back-end)
--- Public: public part of the module on the site
    --- Assets: static data (images, css, front-end dependencies)
    --- Controllers: front-end controllers (angularjs)
    --- Roads: front-end routing (AngularUI Router)
    --- Services: front-end services (angularjs)
    --- Tests: front-end tests (Jasmine)
    --- Views: Views (angularjs)
--- Server: contains the back-end code
    --- Controllers: back-end controllers (Express)
    --- Roads: routing backend (Express)
    --- Models: back-end data model (Mongoose)
    --- Test: back-end tests (Jasmine)
    --- Views: views HMTL (templating Swig)
--- Node_modules: contains Node.js module dependencies (back-end)
`` `

At the root is found as in the case of applying the configuration files to NPM, and bower MEAN.IO (** mean.json **). The most important is the file app.js ** ** which is the module entry point. All files inside the folder Public ** ** will be publicly accessible at the URL * / module-name / file-relative-path *. For example, to access a angularjs controller named 'controller' in the module named 'module' is the URL module * / controllers / controller.js *.

To add a module to the canvas just give it a unique name in the application and copy the packages in the directory ** / ** custom application, MEAN.IO does the rest!

### Authentication

The outline included the management of users and their roles. So you have a page to register a new user from one page to connect to the application, a password reset page (requires however configure an SMTP server) and a page 'publicly available index. If you have the administrator role ('admin') will also inherit a user management GUI (add, delete, role assignment).

If authentication allows adpater the content of the application (menus and pages) depending on the role of the user, it also serves to protect access to the API backend side. To do this it is based on JSON Web Token ([JWT] (http://jwt.io/)), which is a specification for authentication. A JWT is a JSON object that encodes the server by using a private key. The encoded JSON object (which is in fact the user in MEAN.IO) is in the form of a token sent to the client that is authenticated successfully. In MEAN.IO this happens at login that triggers a call to the URL * / api / login * API. Then, with an interceptor angularjs, the client part MEAN.IO associate this token with every request made to the server. If using the server's private key is able to decode the token, it ensures the client's authenticity and authorize the request.

### Aggregation

In order to add a module to the canvas without having to modify any file, MEAN.IO incorporates an automatic mechanism of aggregation for both application files for dependencies. This is based primarily on the server side templating, by default all the JS files from the public folder ** ** of each module are aggregated except in assets ** ** folder reserved for the storage of external bookshops, images and CSS files. In production environment files are minified.

Regarding MEAN.IO outbuildings offers two options:

 - The use of global file config ** / ** assets.json that contains a list of files JS / CSS External
 - Adding external files directly through the source code of each module
 
The first approach is used internally by MEAN.IO for global dependencies canvas as angularjs or jQuery. Nothing prevents you from doing the same for your application, however, this will make it more monolithic in the sense that the dependencies of your modules will be stored globally. It will become impossible to add or remove a module by simply moving his record. The advantage is to have against all dependencies localized in one place: ** ** bower_components for Bower (front-end) and ** ** node_modules for Node.js (back-end).

The second approach allows each module to declare its dependencies and thus to remain completely independent of the canvas. The path of JS and CSS files must be given in the records ** public / assets / js ** and ** public / assets / css **. This is done in the file ** ** app.js the module:
`` `javascript
// Adding an external library
Module.aggregateAsset ('js', 'lib.js');

// Add the module CSS file
Module.aggregateAsset ('css', 'module.css');
`` `
It is possible to control the aggregation more finely if necessary
`` `javascript
// Adding an external library in the header and before the following (default weight is 0)
Module.aggregateAsset ('js', 'first.js', {weight: -1, group 'header'});
// Adding an external library in the footer
Module.aggregateAsset ('js', 'last.js', {group: 'footer'});
`` `
With this approach the dependencies of a module are usually locally installed module. The installation script MEAN.IO effect automatically route all modules and runs inside a `npm / install` bower. NPM then install the dependencies of the file indicated package.json ** ** in the ** file ** node_modules and it seems impossible to change this default behavior. Nevertheless, as NPM modules uses a recursive search strategy this poses no particular problem. The strategy Bower is via a file **. ** Bowerrc, to submit install dependencies bower.json ** ** ** file in the public folder / assets / js **.
`` `
{
  "directory": "public / assets / lib"
}
`` `
> ** Tips & Tricks **: Personally I prefer to install the dependencies in the bower_components ** ** to the root folder because as Bower does not use a recursive search strategy unlike NPM it is possible that even used dependence by two different modules is duplicated, causing the same problems at runtime.

## To contribute

The company that launched MEAN.IO (Linnovate) also deployed an infrastructure to allow the community to share and make available modules, it is the [MEANetwork] (https://network.mean.io ). The first thing is to use this infrastructure to register using the command line tool:
`` `
mean register
`` `
Once registered you can post a module by positioning yourself in the folder of the module and running the `publish` command:
`` `
cd packages / custom / Module
Publish mean
`` `
When posting a module, its source code will actually be released on the service [GitLab] (http://git.mean.io) specific to MEANetwork. For the user named 'user' and module named 'package' the associated deposit will be accessible at the URL http://git.mean.io/user/package. [GitLab] (https://gitlab.com/) is an Open Source equivalent service [GitHub]
(https://github.com/) which can be deployed internally.

## Conclusion

We have reviewed in this article the main principles of MEAN.IO. For my part, although it is evident that the framework is not yet mature (the current version 0.5 at the time of writing is announced as the release candidate version 1.0), it seems to me already provide good basis for structuring full SPA JavaScript web applications:

 - Oriented modular architecture component (a module is a plugin that can be added to the application without having to change the existing)
 - A file organization within each module that simplifies development by automating the reporting templates, roads and outbuildings
 - A software factory (code analysis, minification, documentation, etc.)
 - The basic features required by most applications (authentication, role management, menu management, configuration options, etc.)
 - An emerging network expansion modules supported by the community
 - While being open source remains supported by a company that uses it for its internal projects, which can avoid rapid panting community

> Note that the lead developer of historical MEAN.IO left the company and created a fork named [MEAN.JS] (http://meanjs.org/). At this stage it remains less popular despite a fairly strong start, the most disturbing is the fact that the commits appear to be rare in recent months. Moreover, MEAN.JS separates the back-end and front-end parts in contrast to the approach component MEAN.IO, which seems to me a less elegant structure standpoint.

In future articles we will discuss the creation and operation of a comprehensive MEAN.IO module with a case of actual use.
