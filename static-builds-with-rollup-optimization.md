## Static Builds with Rollup Optimization

To build our component for use in other projects, we can use the SystemJS static build to create
an optimized component file to share.

This is handled via `jspm build` in jspm which takes similar arguments to `jspm bundle`, but instead of storing
a registry entry for each module, it will try to fully optimize the inter-module dependencies.

Whenever there are sub-trees of ES modules within the build tree, SystemJS Builder will inline any of these trees together
using [Rollup](http://rollupjs.org). This allows unused exports to be removed via code tree analysis.
If the whole module tree consists only of ES modules, then the entire tree can be inlined with Rollup, giving the 
best possible code optimization.

> In case it is not already obvious, [Rollup](http://rollupjs.org) is amazing.

Let's create a `dist` folder then output a built component into this folder:

```
jspm build jspm-react-component - react dist/jspm-react-component.js \
  --format umd --global-name jspmReactComponent --global-deps "{'react':'React'}" --skip-source-maps
```

To break down the options we've provided:

* On the first line, we indicate we want to build the _expression_ `jspm-react-component - react`
  into the output build file `dist/jspm-react-component.js`.
* This build _expression_ uses tree arithmetic. The use of the space-separated ` - ` 
  operator indicates we build `jspm-react-component` and all its dependencies except for `react`
  and all its dependencies.
* `--format umd` outputs a built file that will work as AMD, CommonJS or a global.
* `--global-name jspmReactComponent` means that when running as a global, it will create `global.jspmReactComponent`
  as the value of `jspm-react-component`
* `--global-deps "{'react':'React'}"` indicates that our dependency on `react` should refer to the global `React`
  when running as a global.
* `--skip-source-maps` since we don't need references to the original sources anymore we can skip source map generation.

_Neither `--global-name` nor `--global-deps` would have been necessary if running a CJS, AMD or ESM format build._

We should then see the following output in the console:

```
     Creating the single-file build for jspm-react-component - react...
     
       ┌─ jspm-react-component/component.js
       ├─ npm:systemjs-plugin-babel@0.0.2/babel-helpers/classCallCheck.js
       ├─ npm:systemjs-plugin-babel@0.0.2/babel-helpers/createClass.js
       ├─ npm:systemjs-plugin-babel@0.0.2/babel-helpers/possibleConstructorReturn.js
       └─ npm:systemjs-plugin-babel@0.0.2/babel-helpers/inherits.js
     
ok   Fully-optimized - entire tree built via Rollup static optimization.
     
ok   Built into dist/jspm-react-component.js with source maps, unminified as umd.
```

_Fully-optimized_ indicates that the entire tree has been built with Rollup, including
inlining the Babel transform helpers.

The full output of this file can be seen at https://github.com/guybedford/jspm-react-component-demo/blob/master/dist/jspm-react-component.js.

> By default builds, like bundles, build for the browser in production. We can pass the `--node` or `--dev`
options to both the build and bundle commands to build the Node and development conditional paths respectively.
These conditional loading techniques are covered in the [last two sections](conditional-loading.md).