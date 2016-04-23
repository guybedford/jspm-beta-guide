## Loading Libraries from CDN

If we want to use React from a shared CDN instead of through SystemJS, we can build our bundle with react as and react-dom as excludes:

```
  jspm bundle test.js - react - react-dom app-bundle.js -m
```

Then in `jspm.browser.js`, in order to provide the react and react-dom to the CDN versions, we need to add 
custom paths configs:

```javascript
SystemJS.config({
  baseURL: "/",
  production: true,
  paths: {
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*",
    "jspm-react-component/": "src/",
    "npm:react@0.14.6": "https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react.min.js",
    "npm:react-dom@0.14.6": "https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react-dom.min.js"
  }
});
```

_We use CloudFlare over Facebook's CDN here because Facebook's CDN doesn't set the `Access-Control-Allow-Origin: '*'` header which is needed for SystemJS support._