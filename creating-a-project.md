## Creating a Project

First, make sure you've installed jspm. You can install jspm like so:

```
npm install -g jspm@beta
```

And also it's best practice to install it locally to your project:

```
npm install --save-dev jspm@beta
```

_Once jspm 0.17 is stable and released you'll be able to just run `npm install jspm`, but for the beta version you must specify the version._


Once you've got jspm installed, it's time to initialize a project:

```
  jspm init .

     Select an init mode for jspm project configuration.

Init Mode (Quick, Standard, Custom) [Quick]:
```

Choose the following options (all defaults except the package name, jspm-react-component):

* Init mode (Quick, Standard, Custom) [Quick]: Quick
* Local package name (recommended, optional): jspm-react-component
* package.json directories.baseURL: .
* package.json configFiles folder [./]: ./
* Use package.json configFiles.jspm:dev? [No]: No
* SystemJS.config browser baseURL (optional): /
* SystemJS.config Node local project path [src/]: src/
* SystemJS.config local package main [app.js]: app.js
* SystemJS.config transpiler (Babel, Traceur, TypeScript, None) [babel]: babel

After which you should see:

```
     Updating registry cache...
ok   Installed dev dependency plugin-babel to npm:systemjs-plugin-babel@^0.0.12
     (0.0.12)
     Install tree has no forks.
     
ok   jspm init completed. For the standard project layout see
     http://jspm.io/0.17-beta-guide/creating-a-project.html.
     To rerun these init prompts at any time use jspm init.
     
ok   package.json at package.json
     Config at jspm.config.js
     Looking up loader files...
       system.js
       system-polyfills.src.js
       system.js.map
       system-polyfills.js
       system.src.js
       system-polyfills.js.map
     
     Using loader versions:
       systemjs@0.19.31
ok   Loader files downloaded successfully

```

We're going to use a React hello world example here by first installing ReactDOM and also React.

```
  jspm install react react-dom
```

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
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <script src="jspm_packages/system.js"></script>
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

_If you prefer running test apps in Chrome with the `--file-access-from-files` flag enabled to save spinning up a local server set the `jspm.config.js` `browserConfig.baseURL: '.'` during init, or directly in the `jspm.config.js` file._

We include `SystemJS.import` at the end of the body to ensure that the body container is already present when `test.js` runs.

Open up `test.html` through a local server or a file URL and you'll see 'Hello World' on the screen.
