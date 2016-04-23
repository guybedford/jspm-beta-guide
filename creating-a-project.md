## Creating a Project

### Initialize a new empty project

```
  jspm init .

     Select an init mode for jspm project configuration.

Init Mode (Quick, Standard, Custom) [Quick]:
```

Choose the following options (all defaults except the package name):

* Init Mode (Quick, Standard, Custom) [Quick]: `Quick`
* Local package name (recommended, optional): `jspm-react-component`
* package.json directories.lib [src]: `src`
* System.config browser baseURL (optional): `/`
* System.config local package main [jspm-react-component.js]: `jspm-react-component.js`
* System.config transpiler (Babel, Traceur, TypeScript, None) [babel]: `babel`

After which you should see:

```
     Updating registry cache...
ok   Installed dev dependency plugin-babel to npm:systemjs-plugin-babel@^0.0.9 (0.0.9)
     Install tree has no forks.

ok   Verified package.json at package.json
     Verified config files at jspm.config.js and jspm.browser.js
     Looking up loader files...
       system.js
       system-polyfills.js
       system-polyfills.src.js
       system.js.map
       system.src.js
       system-polyfills.js.map

     Using loader versions:
       systemjs@0.19.26
ok   Loader files downloaded successfully
```

### Install React

We're going to use a React hello world example here by first installing [ReactDOM](https://www.npmjs.com/package/react-dom):

```
  jspm install react-dom
```

> NOTE: `react-dom` above is an alias to `npm:react-dom`. Yo You can check it out in the [registry json](https://github.com/jspm/registry/blob/master/registry.json)


```
     Updating registry cache...
     Downloading npm:react-dom@0.14.6
     Downloading npm:react@0.14.6
     Downloading npm:fbjs@0.6.1
ok   Installed peer react to npm:react@^0.14.6 (0.14.6)
ok   Installed npm:fbjs@^0.6.1 (0.6.1)
     Downloading github:jspm/nodelibs-process@0.2.0-alpha
ok   Installed peer process to github:jspm/nodelibs-process@^0.2.0-alpha
     (0.2.0-alpha)
ok   Installed react-dom to npm:react-dom@^0.14.6 (0.14.6)
     Install tree has no forks.

ok   Install complete.
```

Now we can create our hello world component, `src/component.js`:

```javascript
import React from 'react';
export class HelloWorld extends React.Component {
  render() {
    return React.createElement('h1', null, 'Hello World');
  }
}
```

And we'll create a simple test renderer to view the component, `test.js`:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import {HelloWorld} from 'jspm-react-component/component.js';

let container = document.getElementById('container');
let component = ReactDOM.render(React.createElement(HelloWorld), container);
```

Create a new file, `test.html`:

```html
<!docype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="jspm_packages/system.js"></script>
    <script src="jspm.browser.js"></script>
    <script src="jspm.config.js"></script>
</head>
<body>
  <div id="container"></div>
  <script>
    SystemJS.import('test.js');
  </script>
</body>
</html>
```

> Note: `jspm.browser.js` **must** be included before `jspm.config.js` as it sets the browser-specific paths that must be preset for `jspm.config.js` to work._

_If you prefer running test apps in Chrome with the `--file-access-from-files` flag enabled to save spinning up a local server set the `jspm.browser.js` `baseURL: '.'` during init, or directly in the `jspm.browser.js` file._

We include `SystemJS.import` at the end of the body to ensure that the body container is already present when `test.js` runs.

Open up `test.html` through a local server or a file URL and that's it.
