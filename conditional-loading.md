## Conditional Loading

Conditional loading is supported in SystemJS and jspm 0.17 at the module tree level instead of through code
conditionals.

The benefit of this approach is that conditional loading becomes a natural extension of the module
resolution process. SystemJS Builder is designed to handle conditional build trees
so that we can choose to build all possible conditional branches or just the branches we want by passing
the `conditionals` build option.

> The features shown in this section are SystemJS features more than jspm features, with SystemJS Builder
  doing all of the real work for us here.

So for a rather contrived example, let's say we wanted our `Hello World` text to include a note as to whether
we are running in production or development.

We rewrite the `src/component.js` file to import its text from a separate file:

```javascript
import React from 'react';
import text from './text.js';
export class HelloWorld extends React.Component {
  render() {
    return <h1>{text}</h1>;
  }
}
```

Where `src/text.js` contains:

```javascript
export default 'Hello World - Production';
```

Now to create a separate implementation of the `text.js` file for development, edit `src/text-dev.js`:

```javascript
export default 'Hello World - Development';
```

If we load the app, we will get the production version by default.

To inform SystemJS to use the development version in development we can edit the package configuration
(as we did in the [Package Configuration](package-configuration.md) section) in `jspm.config.js`
to include a _conditional package map_:

```javascript
SystemJS.config({
  ...

  packages: {
    "jspm-react-component": {
      ...

      "map": {
        "./text.js": {
          "~production": "./text-dev.js"
        }
      }
    },

    ...
  }
});
```

The `~production` syntax above means the _NOT production_ condition will use
`src/text-dev.js` instead of `text.js`.
We can add multiple conditions to the conditional package map, which will match
from first to last with the path being left unchanged as the default fallthrough.

By editing the `production` boolean property in `jspm.browser.js`, we can now
control which branch of the component text SystemJS will load.
The important point to note about conditional loading is that conditions are fixed 
for the duration of the execution environment.
The environment is either `node` or the `browser` (although environments like Electron are both `node` and `browser`), either running in `production` or not, until we explicitly restart the execution.

By default when we bundle, we will only bundle the production branches.

Run `jspm bundle jspm-react-component - react` to see how the tree excludes the `text-dev.js`
branch:

```
     Building the bundle tree for jspm-react-component - react...
     
       jspm-react-component/component.js
       jspm-react-component/text.js
       npm:systemjs-plugin-babel@0.0.2.json
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/classCallCheck.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/createClass.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/inherits.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/possibleConstructorReturn.js
     
ok   Built into build.js with source maps, unminified.
```

If we instead run `jspm bundle jspm-react-component - react --dev` then bundling will
explicitly include the development branch only.

To include both possible branch outcomes in the bundle, use `jspm bundle jspm-react-component - react --dev --production`:

```
     Building the bundle tree for jspm-react-component - react...
     
       jspm-react-component/component.js
       jspm-react-component/text.js
       jspm-react-component/text-dev.js
       npm:systemjs-plugin-babel@0.0.2.json
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/classCallCheck.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/createClass.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/inherits.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/possibleConstructorReturn.js
     
ok   Built into build.js with source maps, unminified.
```

_In the same way that we have used `production` in this example, we can also use `node` and `browser`
  as conditional branches that conditionally load and build._

Conditional loading also applies to `jspm build`, but we must always build an exact
branch solution when using `jspm build`. That is, we must build for dev or production,
exclusively, browser or node exclusively.

For example, to build a Node-only single-file production version of React:

```
  jspm build react --node --production --format cjs react-node-production-build.js
```

We can then test this with:

```
  node -e "console.log(require('./react-node-production-build.js'))"
```

to see that React has been correctly built and linked for running in Node in production.

By replacing `--node` with `--browser` in the above build we can also create a 
Browserify-style build of React for production, which will then only include all the 
necessary Browserify shims for the NodeJS core modules.