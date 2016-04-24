## Package Configuration

One of the major changes in jspm 0.17 is using the [SystemJS package configuration](https://github.com/systemjs/systemjs/blob/0.19.16/docs/config-api.md#packages) system
as the fundamental configuration mechanic.

Everything about how to load a package is provided by this single configuration and jspm aligns this
all the way down to reading this same exact SystemJS package configuration for installed packages directly
from their package.json file.

_That is, all the [SystemJS package configuration properties](https://github.com/systemjs/systemjs/blob/0.19.16/docs/config-api.md#packages)
are supported as package.json properties in jspm 0.17, that can be set at the base-level or under the `jspm` prefix. This provides a powerful modular package configuration
mechanism enabling features including custom plugin loads and branched conditional loading, which is covered at the end of this guide._

Let's use package configuration to set up some loading rules in our sample application.
Open `jspm.config.js` and under `packages` there will be an object for our local package
that was created during init (`jspm-react-component` in this guide):

```javascript
SystemJS.config({
  ...

  packages: {
    "jspm-react-component": {
      "main": "jspm-react-component.js",
      "meta": {
        "*.js": {
          "loader": "plugin-babel"
        }
      }
    },

    ...
  }
})
```

Since every module file in the package will be written as an ES Modules, let's set the module format and also rename the main component from `jspm-react-component.js` to `component.js`.

```javascript
...
  packages: {
    "jspm-react-component": {
      "main": "component.js",
      "format": "esm",
      "meta": {
        "*.js": {
          "loader": "plugin-babel"
        }
      }
    }
    ...
```

In `test.js` we can then replace `import {HelloWorld} from 'jspm-react-component/component.js'` with
just `import {HelloWorld} from 'jspm-react-component'`, and SystemJS will no longer use format detection
as it knows what module format to use to interpret modules from this package.

> Usually SystemJS does a simple regex-based detection with `format: 'detect'` as the default which is
  not designed to be highly accurate for performance. For example, this fails on cases where ES module syntax is contained in
  comments in non-ES module files, or when ES modules don't contain any import or export syntax. We avoid these
  issues by setting the format explicitly for SystemJS.
