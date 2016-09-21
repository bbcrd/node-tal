# node-tal [![Build Status](https://travis-ci.org/bbc/node-tal.png?branch=master)](https://travis-ci.org/bbc/node-tal)

> Node.js backend library and connect middleware to serve your [TAL application](http://fmtvp.github.io/tal/) quickly.

# Install

```bash
npm install --save tal
```

# Usage

## Connect Middleware

The [express 4.x middleware](http://expressjs.com/4x/api.html) will automatically update the HTTP response
MIME-Type as well as populating a `res.locals.tal` object (see below).

```js
// in app.js
var app = express();

app.use(require('tal').middleware());
```

A TAL application requires an Application ID, and this can be set either as an
option to the middleware function:

```js
app.use(require('tal').middleware({ application_id: 'testapp' });
```

or in your `package.json` file:

```js
"tal": {
  "default": {
    "application_id": "testapp"
  }
}
```

### `res.locals.tal`

This object is populated for each HTTP request connected to the TAL middleware. It contains the following fields,
and are tailored for the requesting TV device:

 * `app_id`: the TAL Application ID used to alias RequireJS path
 * `doctype`: the relevant HTML doctype string
 * `rootTag`: the relevant HTML opening `<html>` tag string
 * `deviceHeaders`: the relevant HTML `Http-Equiv` tags string
 * `deviceBody`: the relevant additional HTML tags string to be added after an opening `<body>` tag
 * `config`: a JavaScript object containing the `deviceConfig` required by the frontend TAL framework to operate

### Using in a template

This example is using [handlebars](http://handlebarsjs.com/) to illustrate how to bootstrap a TAL application:

```html
// in layout.hbs

{{{tal.doctype}}}
{{{tal.rootTag}}}
<head>
  {{{tal.deviceHeaders}}}
  <script>
    var antie = {{{json tal.config compact=true}}};
    var require = {
      baseUrl: "",
      paths: {
        '{{tal.app_id}}': 'js',
        antie : "/path/to/tal/static/script"
      },
      priority: [],
      callback: function() {}
    };
  </script>
</head>
<body>
{{{tal.deviceBody}}}

<div id="static-loading-screen">{{{ body }}}</div>
<div id="app" class="display-none"></div>

<script src="/path/to/requirejs/require.js"></script>
<script src="/path/to/your/talApp/init.js"></script>
</body>
</html>
```

## Library Helpers

In case you would like to tailor a specific use case, you can use the TAL helpers to build your own middleware or HTTP API.

### Initialising

```js
var tal = require('tal').helpers;
var helpers = new tal();
```

### Creating a device config

The device config is provided by the `tal-config` package. See below how to override it and inject your own configuration package.

```js
var deviceConfig = helpers.getDeviceConfigFromRequest({ brand: 'panasonic', model: 'tv_2013' });
```

### Using device helpers

From that point you can retrieve any device specific configuration, including the frontend TAL configuration object.

[See the API more in details in the sourcecode](lib/helpers.js).

### Providing your own device configurations

Simply provide a module respecting the `tal-config` signature to the helper constructor.

```js
var tal = require('tal').helpers;
var talConfig = require('./my/private/tal-config.js');
var helpers = new tal(talConfig);
```

# Testing

Simply run the tests using the following command.

```bash
npm test
```

If you add a feature and don't have any knowledge in testing, propose your code anyway. Explain what you want to achieve,
what are the edge cases and we'll do our best to fill the blanks.

# License

> Copyright 2014 British Broadcasting Corporation

> Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License.
> You may obtain a copy of the License at

> http://www.apache.org/licenses/LICENSE-2.0

> Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
> See the License for the specific language governing permissions and limitations under the License.
