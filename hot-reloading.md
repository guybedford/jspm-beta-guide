## Hot Reloading

> This workflow is entirely optional, as managing application state comes with some complexities, but offers the fastest development experience if you're interested in setting it up.

To further improve development performance over the development bundling workflow, we can use hot reloading to just replace the changed modules in the module registry in the browser,
without having to refresh the whole page.

Because hot-reloading requires a module registry in the browser to work, it works naturally on top of SystemJS, 
as was first demonstrated by [Glen Maddern](https://twitter.com/glenmaddern) with the jspm-server project.

Here we will use Jiri Spac's [SystemJS Hot Reloader project](https://github.com/capaj/systemjs-hot-reloader):

```
jspm install --dev systemjs-hot-reloader
```

Update the `test.js` file to initiate hot reloading by importing the hot-reloader listener, and then also providing a `__reload` hook
to maintain the internal component state over hot-reload updates:

```javascript
import 'systemjs-hot-reloader/default-listener.js';

export function __reload(m) {
  if (m.component.state)
    component.setState(m.component.state);
}

import React from 'react';
import ReactDOM from 'react-dom';
import {HelloWorld} from 'jspm-react-component/component.js';

let container = document.getElementById('container');
export let component = ReactDOM.render(React.createElement(HelloWorld), container);
```

Because we are re-rendering the component on hot-reload, the `__reload` hook allows us to pass the state across
the reload event, which takes the previous module value as its argument. We have to export the component instance
via `export let component = ReactDOM.render(...)` to ensure we provide a reference to this state.

Next edit the `jspm.browser.js` file to include `trace: true`, so that it reads:

```javascript
SystemJS.config({
  baseURL: "/",
  trace: true,
  paths: {
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*",
    "jspm-react-component/": "src/"
  }
});
```

Finally, to emit the change events to the browser we install and run the local file event 
emitter designed for systemjs-hot-reloader from the base directory of the project:

```
npm install -g chokidar-socket-emitter
```

```
chokidar-socket-emitter
  chokidar listening on 5776
```

With the `test.html` page open, any edits to the `src/component.js` file will now reflect instantly on save with full
state replication.

> Note that the [SystemJS Hot Reloader project](https://github.com/capaj/systemjs-hot-reloader) is still new 
  and somewhat experimental. If you find a bug or use case that isn't supported contributions will likely be very welcome.