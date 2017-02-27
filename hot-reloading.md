## Hot Reloading

> This workflow is entirely optional, as managing application state comes with some complexities, but offers the fastest development experience if you're interested in setting it up.

To further improve development performance over the development bundling workflow, we can use hot reloading to just replace the changed modules in the module registry in the browser,
without having to refresh the whole page.

Because hot-reloading requires a module registry in the browser to work, it works naturally on top of SystemJS,
as was first demonstrated by [Glen Maddern](https://twitter.com/glenmaddern) with the jspm-server project.

Here we will use [Alexis Vincent's](https://github.com/alexisvincent)'s [systemjs-hot-reloader](https://github.com/alexisvincent/systemjs-hot-reloader) project.

```
jspm install --dev systemjs-hot-reloader
```

Add `"@hot": "@empty"` to your `jspm.config.js` map section
```js
{
  ...
  "map": {
    ...
    "@hot": "@empty"
  }
}
```

Update `test.html` so that you first import the hot reloader
```html
<!doctype html>
<meta charset="utf-8">
<script src="jspm_packages/system.js"></script>
<script src="jspm.config.js"></script>
<body>
  <div id="container"></div>
  <script>
    SystemJS.import('systemjs-hot-reloader').then(function(connect) {
      connect()
      SystemJS.import('test.js');
    })
  </script>
</body>
```

> The next step is optional and seeks to give an example of how to maintain state across reloads.

And then update the `test.js` file to maintain the internal component state over hot-reload updates. We import `module`
from `@hot` which will give us our previous module instance,
or `false` on first load. Then if `module` is truthy (this is a
reload), we set the current component state to the old component state. Note that to get a hold of the old component state we need to export it from the module.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import {HelloWorld} from 'jspm-react-component/component.js';

import { module } from '@hot'

let container = document.getElementById('container');
export let component = ReactDOM.render(React.createElement(HelloWorld), container);

if (module)
  component.setState(module.component.state);
```

Finally, to emit the change events to the browser we install and run the local file event
emitter designed for `systemjs-hot-reloader` from the base directory of the project:

```
npm install -g chokidar-socket-emitter

chokidar-socket-emitter
```

With the `test.html` page open, any edits to the `src/component.js` file will now reflect instantly on save with full
state replication.
