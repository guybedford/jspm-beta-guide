## Bundling

To bundle our app for production we use `jspm bundle`:

```
  jspm bundle test.js app-bundle.js --minify
```

The above will build `test.js` and all of its dependencies into a single optimized and minified file.
This includes running Babel and transpiling JSX so that Babel and the JSX plugin are no longer used
as dependencies in the bundle.

In addition, the hot reloader is by default configured to be excluded when bundling and React has been 
configured with a jspm override to use conditional loading to exclude its development
dependencies as well. CommonJS files will have `process.env.NODE_ENV` inlined to the value of `production`,
which is then removed by minification giving us the minimal download footprint for React.

We then include this bundle file in our HTML after the configuration, but before our `SystemJS.import` statement:

```html
<!doctype html>
<meta charset="utf-8">
<script src="jspm_packages/system.js"></script>
<script src="jspm.config.js"></script>
<script src="app-bundle.js"></script>
<body>
  <div id="container"></div>
  <script>
    SystemJS.import('test.js');
  </script>
```

To tell SystemJS to run in production mode, edit `jspm.browser.js` and set the `production: true` config option
(at the same time we can also remove the `trace: true` we used for hot reloading):

```javascript
SystemJS.config({
  baseURL: "/",
  production: true,
  paths: {
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*",
    "jspm-react-component/": "src/"
  }
});
```

Viewing the network tab in the browser, you should then see no external requests being made apart from the HTML scripts.