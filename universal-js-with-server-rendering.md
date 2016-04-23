## Universal JS with Server Rendering

Let's now set up server rendering of our component via a simple express server:

```
  jspm install --dev npm:express
```

server.js
```javascript
import express from 'express';
import React from 'react';
import ReactDOMServer from 'react-dom/server.js';
import {HelloWorld} from 'jspm-react-component';

let app = express();
app.get('/', (req, res) => {
  let componentHTML = ReactDOMServer.renderToString(React.createElement(HelloWorld));
  res.send('<!doctype html><body>' + componentHTML);
});

app.listen('8080', () => console.log('Listening on port 8080'));
```

To run this server we use `jspm run`:

```
jspm run server.js
Listening on port 8080
```

Navigating to `http://localhost:8080` in the browser then demonstrates server rendering with JSX support in Node.

_With the same configuration and codebase, we are able to run JS universally without any additional effort._

Handling state rehydration is beyond the scope of this tutorial, but can then be implemented using standard techniques.

We won't be using server rendering further here, so we can go ahead and run `jspm uninstall express` to clean up our app,
along with its Node core peer dependencies via `jspm uninstall os module http crypto constants child_process assert querystring string_decoder url vm`.

> To install a NodeJS core library in jspm, use for example `jspm install fs --edge` to get
  a requirable fs package that will work both client and server. These core packages
  are running on alpha so need the `--edge` flag when installing for this jspm 0.17 
  beta pending the stable release.