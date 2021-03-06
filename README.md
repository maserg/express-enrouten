express-enrouten
==================

Route configuration middleware for expressjs.


[![Build Status](https://travis-ci.org/paypal/express-enrouten.png)](https://travis-ci.org/paypal/express-enrouten)
[![NPM version](https://badge.fury.io/js/express-enrouten.png)](http://badge.fury.io/js/express-enrouten)

### API
#### `app.use(enrouten(options))`
```javascript
var express = require('express'),
    enrouten = require('express-enrouten');

var app = express();
app.use(enrouten({ ... }));
// or app.use('/foo', enrouten({ ... }));
```


### Configuration
express-enrouten supports routes via configuration and convention.
```javascript
app.use(enrouten({
        routes: [{
            method: 'GET',
            path: '/foo',
            handler: function (req, res) {
                // ...
            }
        }]
    }
});
```

- `directory` (optional) - String or array of path segments. Specify a directory to have enrouten scan all files recursively
to find files that match the controller-spec API.

```javascript
app.use(enrouten({
    directory: 'controllers'
});
```

- `routes` (optional) An array of route definition objects. Each definition must have a `path` and `handler` property and
can have an optional `method` property (`method` defaults to 'GET').

```javascript
app.use(enrouten({
    routes: [
        { path: '/',    method: 'GET', handler: require('./controllers/index') },
        { path: '/foo', method: 'GET', handler: require('./controllers/foo') }
    ]
});
```

- `index` (optional, overrides `directory` and disables scanning) - String path or array of path segments indicating
the file to load which acts as the route 'index' of the application.

```javascript
// index.js
module.exports = function (app) {

    app.get('/', index);
    app.get('/account', passport.protect, account);

    // etc...
};
```

### Controller Files
A 'controller' is defined as any `require`-able file which exports a function that accepts a single argument. Any files with an extension of `.js` (or `.coffee` if CoffeeScript is registered) will be loaded and if it exports a function that accepts a single argument then this function will be called. **NOTE: Any file in the directory tree that matches the API will be invoked/initialized with the express application object.**

```javascript
// Good :)
// controllers/controller.js
module.exports = function (app) {
    app.get('/', function (req, res) {
        // ...
    });
};

// Bad :(
// Function does not get returned when `require`-ed, use `module.exports`
exports = function (app) {
    // ...
};

// Bad :(
// controllers/other-file-in-same-controller-directory.js
modules.exports = function (config) {
    // `config` will be the express application
    // ...
};

// Acceptable :)
// controllers/config.json - A non-js file (ignored)
// controllers/README.txt - A non-js file (ignored)
// controllers/util.js - A js file that has a different API than the spec (ignored)
module.exports = {
    importantHelper: function () {

    }
};
```
