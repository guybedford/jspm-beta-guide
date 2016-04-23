## Conditional Substitution

Let's internationalize our Hello World component example.

The previous _conditional package map_ doesn't scale well to dealing with many different possible
conditional outcomes as with a language set. For this, we can use _conditional substitution_.

First create some language variations:

* src/en/text.js: `export default 'Hello World';`
* src/de/text.js: `export default 'Hallo Welt';`
* src/es/text.js: `export default 'Hola Mundo';`
* src/zh/text.js: `export default '你好，世界';`

Then in the component, we can load the correct variation using conditional substitution with:

```javascript
import React from 'react';
import text from './#{lang}/text.js';
export class HelloWorld extends React.Component {
  render() {
    return <h1>{text}</h1>;
  }
}
```

_This conditional syntax `#{...}` is entirely a SystemJS feature and construct._

`lang` is simply a module reference itself equivalent to `import lang from 'lang';`. That is,
it is not provided for us at all, so we need to ensure we create this correct language detection
module for our environment.

For this example, let's just select the language randomly by creating this conditional
environment module in `src/lang.js` as:

```javascript
export default ['en', 'de', 'es', 'zh'][Math.floor(Math.random() * 3)];
```

We can then provide the map alias for `lang` to this language condition module 
in the main map config inside `jspm.config.js`:

```javascript
SystemJS.config({
  ...

  map: {
    ...
    "lang": "jspm-react-component/lang.js",
    ...
  },

  ...
});
```

At risk of spelling out the obvious, this relative map informs the package that any load to `lang` within 
the package should use the file `./lang.js` relative to the package itself. Since the conditional substitution
is treated just like a require within the package, it will respect these mappings.

Loading the `test.html` page will no display a different language variation on each page load.

To create language bundles, we can just bundle our app normally:

```
jspm bundle test.js - react
```

SystemJS builder will by default include all substitution variations within the tree giving the output:

```
    Building the bundle tree for test.js - react...
     
       github:capaj/systemjs-hot-reloader@0.5.3.json
       jspm-react-component/component.js
       jspm-react-component/de/text.js
       jspm-react-component/en/text.js
       jspm-react-component/es/text.js
       jspm-react-component/lang.js
       jspm-react-component/zh/text.js
       npm:react-dom@0.14.6.json
       npm:react-dom@0.14.6/index.js
       npm:systemjs-plugin-babel@0.0.2.json
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/classCallCheck.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/createClass.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/inherits.js
       npm:systemjs-plugin-babel@0.0.2/babel-helpers/possibleConstructorReturn.js
       test.js
     
ok   Built into build.js with source maps, unminified.
```

The conditional substitution variations are detected by globbing the filesystem for possible resolutions.
Substitution values are not allowed to cross package or map boundaries or include a `/` separator so that
this globbed resolution remains well-defined.

We can also create a main bundle for our app with separate language bundles that get loaded conditionally.

First we create the base bundle without any language variations, by passing SystemJS builder `conditions`
environment trace object.

```
jspm bundle test.js common-bundle.js --conditions "{'src/lang.js':false}"
```

We include this `common-bundle.js` directly in the `test.html` page:

```html
<!docype html>
<meta charset="utf-8">
<script src="jspm_packages/system.js"></script>
<script src="jspm.browser.js"></script>
<script src="jspm.config.js"></script>
<script src="common-bundle.js"></script>
<body>
  <div id="container"></div>
  <script>
    SystemJS.import('test.js');
  </script>
```

Next, to build individual language bundles (this process can likely be optimized):

```
jspm bundle test.js - common-bundle.js bundle-en.js --conditions "{'src/lang.js':'en'}"
jspm bundle test.js - common-bundle.js bundle-de.js --conditions "{'src/lang.js':'de'}"
jspm bundle test.js - common-bundle.js bundle-es.js --conditions "{'src/lang.js':'es'}"
jspm bundle test.js - common-bundle.js bundle-zh.js --conditions "{'src/lang.js':'zh'}"
```

And to load those language bundles conditionally in the environment, we add a `bundles`
entry to ths `jspm.browser.js` config:

```
SystemJS.config({
  baseURL: ".",
  production: true,
  paths: {
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*",
    "jspm-react-component/": "src/"
  },
  bundles: {
    'bundle-#{lang}.js': ['src/*/text.js']
  }
});
```

Loading the `test.html` with Chrome devtools closed for performance, the page should then randomly 
load a fully-built language pack bundle.