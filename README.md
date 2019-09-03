# webpack-bundle-tracker

Spits out some stats about webpack compilation process to a file.

This is the fork of `webpack-bundle-tracker` (https://github.com/owais/webpack-bundle-tracker) with added functionality for async chunks.

There are two types of chunks, named e.g: `main`, `app`, `vendors` and with `id` - service chunks

Named chunks needs to be preload an service chunks needs to be prefeched.

I use this plugin in combination with `django-webpack-loader` (https://github.com/owais/django-webpack-loader)

<br>

## Install

```bash
npm install --save-dev webpack-bundle-tracker-async
```

<br>

## Usage

```javascript
var BundleTracker  = require('webpack-bundle-tracker');
module.exports = {
    context: __dirname,
    entry: {
      app: ['./app']
    },

    output: {
        path: require("path").resolve('./assets/bundles/'),
        filename: "[name]-[hash].js",
        publicPath: 'http://localhost:3000/assets/bundles/',
    },

    plugins: [
      new BundleTracker({
        path: __dirname,
        filename: './assets/webpack-stats.json',
        preload: 'async_preload', // for preloading named chunks
        prefetch: 'async_prefetch', // for prefetching service chunks
      })
    ]
}
```

`./assets/webpack-stats.json` will look like,

```json
{
  "status": "done",
  "chunks": {
    "app": [
      {
        "name": "app-0828904584990b611fb8.js",
        "publicPath": "http://localhost:3000/assets/bundles/app-0828904584990b611fb8.js",
        "path": "/home/user/project-root/assets/bundles/app-0828904584990b611fb8.js"
      }
    ],
    "runtime": [
      {
        "name": "runtime-58e2b6c82f0f9e50524c.js",
        "publicPath": "http://localhost:3000/assets/bundles/runtime-58e2b6c82f0f9e50524c.js",
        "path": "/home/user/project-root/assets/bundles/runtime-58e2b6c82f0f9e50524c.js"
      }
    ]
  },
  "entryPoints": {
    "main": [
      [
        {
        "name": "app-0828904584990b611fb8.js",
        "publicPath": "http://localhost:3000/assets/bundles/app-0828904584990b611fb8.js",
        "path": "/home/user/project-root/assets/bundles/app-0828904584990b611fb8.js"
        }
      ],
      [
        {
        "name": "runtime-58e2b6c82f0f9e50524c.js",
        "publicPath": "http://localhost:3000/assets/bundles/runtime-58e2b6c82f0f9e50524c.js",
        "path": "/home/user/project-root/assets/bundles/runtime-58e2b6c82f0f9e50524c.js"
        }
      ]
    ]
  }
}
```

In case webpack is still compiling, it'll look like,


```json
{
  "status":"compiling",
}
```



And errors will look like,
```json
{
  "status": "error",
  "file": "/path/to/file/that/caused/the/error",
  "error": "ErrorName",
  "message": "ErrorMessage"
}
```

`ErrorMessage` shows the line and column that caused the error.



And in case `logTime` option is set to `true`, the output will look like,
```
{
  "status":"done",
  "chunks":{
   "app":[{
      "name":"app-0828904584990b611fb8.js",
      "publicPath":"http://localhost:3000/assets/bundles/app-0828904584990b611fb8.js",
      "path":"/home/user/project-root/assets/bundles/app-0828904584990b611fb8.js"
    }]
  },
  "startTime":1440535322138,
  "endTime":1440535326804
}
```

By default, the output JSON will not be indented. To increase readability, you can use the `indent`
option to make the output legible. By default it is off. The value that is set here will be directly
passed to the `space` parameter in `JSON.stringify`. More information can be found here:
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify

<br>

## Bundle Tracker Options

* `filename` - Name of the bundle tracker JSON file.
* `logTime` - Optional boolean property to output `startTime` and `endTime` in bundle tracker file.
* `path` - Optional bundle tracker output path.
* `publicPath` - Optional property to override `output.publicPath`.
* `preload` - name of key for chunks to preload (named chunks)
* `prefetch` - name of key for chunks to prefetch (service chunks)
