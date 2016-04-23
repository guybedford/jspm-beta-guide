## Custom Package Resolutions

> This section is somewhat advanced, but describes an important set of features. Feel free to skip over to the next section.

To avoid having react or react-dom installed at all in this app (_and don't need server JS support at all_)
we can indicate we are using custom react resolutions in `jspm.config.js` via map config:

```javascript
SystemJS.config({
  ..
  map: {
    ...
    "react": "react-cdn",
    "react-dom": "react-dom-cdn"
    ...
  },
  ...
});
```

> We could have set the resolution to any custom path if we had our own version of React as well.

By altering the map configs to these non jspm resolutions in `jspm.config.js`, jspm then treats those dependencies as 
manually resolved and locked.

Running `jspm clean` will then remove any package install traces of react and react-dom:

```
jspm clean
     Clearing configuration for npm:react@0.14.6
     Removing unused package.json override npm:fbjs@0.6.1
     Removing unused package.json override npm:react@0.14.6
     Removing package files for npm:fbjs@0.6.1
     Removing package files for npm:react-dom@0.14.6
     Removing package files for npm:react@0.14.6

ok   Project cleaned successfully.
```

We then need to update the `jspm.browser.js` CDN paths to reference `react` and `react-dom`:

```javascript
SystemJS.config({
  ...
  paths: {
    ...
    "react-cdn": "https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react.min.js",
    "react-dom-cdn": "https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react-dom.min.js"
  }
});
```

In order to ensure that `react` and `react-dom` don't get built we can set a custom build config file, `jspm.build.json`:

```json
{
  "meta": {
    "react-cdn": {
      "build": false
    },
    "react-dom-cdn": {
      "build": false
    }
  }
}
```

This config uses the [SystemJS meta configuration](https://github.com/systemjs/systemjs/blob/master/docs/config-api.md#meta) to set build metadata, but could also 
set any other SystemJS loader options, which we then apply on build with the `--config` flag:

```
  jspm bundle test.js app-bundle.js -m --config jspm.build.json
```

giving the same end-result of CDN resolution as in the previous section, but based on our custom resolution and build rules.