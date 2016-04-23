## Installing the JSX Babel Plugin

To upgrade our hello world example to use JSX, we can install the JSX React plugin for Babel since 
we're already using Babel transpilation.

```
  jspm install --dev npm:babel-plugin-transform-react-jsx core-js
```

Inside of `jspm.config.js` under the `jspm-react-component` packages entry, add this plugin to Babel
using package configuration metadata:

```javascript
SystemJS.config({
  ...

  packages: {
    "jspm-react-component": {
      "main": "component.js",
      "format": "esm",
      "meta": {
        "*.js": {
          "babelOptions": {
            "plugins": ["babel-plugin-transform-react-jsx"]
          }
        }
      }
    },
    ...
  }
})
```

> Alternatively we could also update our package to the `jsx` file extension here if we wish.

We can then update our component to use JSX syntax:

```javascript
import React from 'react';
export class HelloWorld extends React.Component {
  render() {
    return <h1>Hello World</h1>;
  }
}
```

> If not using Babel, a custom JSX plugin can be installed to run before the ES module transpiler,
  configured via `jspm install jsx` and setting `{ meta: { '*.jsx': { loader: 'jsx' } } }`
  in the local application package configuration.