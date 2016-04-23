## Development Bundling

When you're working on small experiments or demos, a convenient feature of jspm is that you don't need to run any form of setup task.
jspm loads and transpiles everything in the browser, removing the reliance on OS-installed tooling.

The obvious problem you might notice is that it takes about 3 seconds to render the sample component here in Chrome with devtools open and 2 seconds in Firefox or Chrome with devtools disabled.
While in theory it should be possible for this refresh to be well under a second, the reality is that web browsers are not currently optimized to handle high numbers of interleaved fetch / execution cycles.

This is due to the large number of requests made by React, which we now optimize for further development by switching to a development bundle workflow with watching
to generate a single unminified bundle file to be loaded in the browser.

To add this improvement, we will use the `jspm bundle` command with the `watch` flag:

```
jspm bundle test.js -wid
```

where the flags here are:

* `--watch`: Watch the module tree for file changes to then do a quick cached rebuild.
* `--inject`: Ensure that the bundle is automatically loaded without any further changes necessary.
* `--development`: Create a development bundle, without any production flags or optimizations, which
  are otherwise the default when creating a bundle.

This will then display:

```
     Building the bundle tree for test.js...
     
       github:jspm/nodelibs-process@0.2.0-alpha.json
       github:jspm/nodelibs-process@0.2.0-alpha/process.js
       jspm-react-component/component.js
       npm:fbjs@0.6.1.json
...
       npm:systemjs-plugin-babel@0.0.5.json
       npm:systemjs-plugin-babel@0.0.5/babel-helpers/classCallCheck.js
       npm:systemjs-plugin-babel@0.0.5/babel-helpers/createClass.js
       npm:systemjs-plugin-babel@0.0.5/babel-helpers/inherits.js
      
     npm:systemjs-plugin-babel@0.0.5/babel-helpers/possibleConstructorReturn.js
       test.js
     
ok   build.js added to config bundles.
ok   Built into build.js with source maps, unminified.
Watchman:  Watchman was not found in PATH.  See https://facebook.github.io/watchman/docs/install.html for installation instructions
     Watching . for changes with Node native watcher...
```

Now any changes to any of the modules will automatically rebuild the bundle, refreshing the browser
will then load the changes from that single bundle file for a quick page refresh.

_For performance, it is advisable to close the Chrome devtools when reloading the page, before opening them again as there is a [longer page load with devtools open](https://github.com/systemjs/systemjs/issues/1054)._

> For the fastest workflow it is worth [installing Watchman](https://facebook.github.io/watchman/docs/install.html) as well.