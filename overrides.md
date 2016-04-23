## Overrides

Overrides were one of the biggest pain points in jspm 0.16, and have been completely overhauled for jspm 0.17
to make configuration workflows easier.

> Note this is still a somewhat advanced topic so you may wish to skip this section.

The basic principle of overrides remains though - we have a package installed whose package.json was not
created by ourselves. In order to provide jspm configuration for the package, we need to tell jspm which
configuration options to use in place of the package.json options already provided, that is we provide
a _package.json override_.

For the sake of this example, say we want to override our installed `npm:jspm-react-component-demo` 
component to have `"format": "cjs"` instead of `"format": "amd"` as it is in its package.json.

While this is a small change, it demonstrates how we can override any [SystemJS Package Configuration](https://github.com/systemjs/systemjs/blob/master/docs/config-api.md#packages)
option through the package.json override.

As in jspm 0.16, we can provide an inline override:

```
  jspm install npm:jspm-react-component-demo -o "{format: 'cjs'}"
```

or set the override directly in the local package.json (which the above does anyway):

```
{
  "jspm": {
    ...
    "overrides": {
      "npm:jspm-react-component-demo@0.1.1": {
        "format": "cjs"
      }
    }
  }
}
```

and running `jspm install`.

Overrides will always remain in the package.json, even when overrides are checked into the jspm registry.
This is to ensure full reproducibility of installs without any build dependence on the jspm registry.

It's still somewhat painful to have to edit overrides the above ways and reinstall each time just to test
them.

To make this easier, we can actually edit the package configurations of installed packages directly.

If you look inside of `jspm_packages/npm/` you'll see a whole lot of `.json` files. These are the SystemJS
package configuration files generated on install for each package.

For example, the contents of `jspm_packages/npm/jspm-react-component-demo.json` with our new override can be
seen as:

```json
{
  "main": "jspm-react-component.js",
  "format": "amd"
}
```

SystemJS loads this configuration file before loading any modules from the package, so that it understands
how to load the package. It's just like NodeJS loads the package.json of a package to read its main, but
with all the SystemJS package configuration options available.

These configuration files can be edited directly for instant development feedback. For example
to treat `jspm-react-component.js` as a global module with React as a shimmed dependency:

```json
{
  "main": "jspm-react-component.js",
  "format": "global",
  "meta": {
    "jspm-react-component.js": {
      "globals": {
        "React": "react"
      }
    }
  }
}
```

Hopefully these examples are beginning to demonstrate the flexibility of having SystemJS package
configuration provided by the package.json.

> The `shim` package override property from jspm 0.16 is still supported for backwards compatibility,
  but is deprecated for using package configuration directly as above.

Reloading the `test.html` page should immediately reflect the new interpretation. To verify it is
actually working, try leaving out the `globals` property above.

When we've finished testing out our package configuration changes, we can run 
`jspm install npm:jspm-react-component-demo`, and you should see the following at the top of the install output:

```
ok   The package configuration file
     jspm_packages/npm/jspm-react-component-demo@0.1.1.json has been edited
     manually. To avoid overwriting the change, it has been added to the
     package.json overrides.
```

If we look inside the application `package.json`, you should see the configuration changes we made above
have been persisted directly into the package.json overrides so that our edits will be retained.

> The only way to have overrides not included in the package.json is if the configurations are included
in the original package and published with a new version. The jspm registry will still provide overrides,
but because overrides in the registry can change over time, inclusion in the package.json is needed to guarantee
reproducibility. The jspm package configuration can be deemed stable as of jspm 0.17 to be able to send these PRs
to public packages, and feel free to copy in @guybedford if you need these checked.