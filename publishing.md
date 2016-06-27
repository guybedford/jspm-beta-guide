## Publishing

With the static build of the component created, we can now publish this component.

While we can publish directly from the base-level, to keep things simple and avoid creating a `.npmignore`,
we can just create a new package.json file inside our `dist` folder, `dist/package.json`:

```json
{
  "name": "jspm-react-component-demo",
  "version": "0.1.1",
  "registry": "npm",
  "peerDependencies": {
    "react": "^15.1.0"
  },
  "jspmPackage": true,
  "main": "jspm-react-component.js",
  "format": "amd"
}
```

We can then publish the package to npm with:

```
cd dist
npm publish .
```

To explain the properties we've set:
* `name`, `version`: These are required for publishing to npm.
* `registry`: This property is a jspm-specific property which tells jspm how to interpret the dependencies
  of the package. It's not strictly needed here, but is advisable to always set and we'll need it for the
  linking workflow shortly.
* `peerDependencies`: We set the react dependency in our component as a peerDependency, as React doesn't support
  multi-versions. This way it must always be resolved to a single version in the target environment.
* `jspmPackage`: This tells jspm to skip the Node package conversion process when installing the package.
   In jspm 0.16 this was called `jspmNodeConversion: false`.
* `main`, `format`: While `main` is supported by npm and NodeJS, `format` is a jspm-specific property which means
  effectively that our umd build will be loaded as CommonJS in npm and AMD in jspm (this is mainly illustrative, there's no great reason why we've chosen to do this here).
  _These properties are both [SystemJS package configuration options](https://github.com/systemjs/systemjs/blob/master/docs/config-api.md#packages) - jspm allows any of these package configuration options to be set directly in the package.json file._

> The way we have written the package.json above is fully compatible with npm, while also
providing jspm-specific properties for added functionality in jspm. This approach of publishing based
on compatibility while enabling extra options for jspm is the recommended way to handle publishing to
the widest audience. If only publishing a package for jspm consumption, then this of course not a concern.

We can then install this component into the demo app under a new name to avoid conflicts with:

```
jspm install npm:jspm-react-component-demo
```

If we edit the original `test.js` file and replace the line:

```javascript
import {HelloWorld} from 'jspm-react-component';
````

with:

```javascript
import {HelloWorld} from 'jspm-react-component-demo';
```

This completes the loop of building a component for production to install back into a new app,
and since we've been careful with our package.json configuration, this component will also support 
use in standard Node and npm install workflows as well.