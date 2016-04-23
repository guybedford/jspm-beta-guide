## Linking

When working on a number of your own packages together in an application, it 
is cumbersome to have to publish on every change just to be able to update the application.

The linking workflow in jspm 0.17 has been highly optimized to use direct symlinking for instant changes.

Instead of installing our package link in the previous publish step, we can link it with:

```
jspm link dist
```

If you followed the previous step you will get the prompt:

```
     npm:jspm-react-component-demo@0.1.1 is already installed, are you sure
     you want to link over it?

Replace installed version? [Yes]:
```

Followed by:

```
     Updating registry cache...
     Processing linked package npm:jspm-react-component-demo@0.1.1
ok   Symlinked jspm-react-component-published as npm:jspm-react-component-demo@0.1.1
     (0.1.0)
     
     Run this link command again or jspm install
     npm:jspm-react-component-demo@0.1.1 to relink changes in the package.json file.
     Run jspm install --unlink to unlink and install all original packages.
     Linked packages can also be uninstalled normally.
```

What just happened here?

* `jspm link <div>` takes any path to a local folder.
* It then reads the package.json of that folder, and will detect the `name`, `version` and `registry`
  properties of the package.
* The `jspm_packages/npm/jspm-react-component-demo@0.1.1` folder is then symlinked to our local `dist` folder.
* To link into a custom name we can also use eg `jspm link dist github:custom/package@name` to specify any
  target name during linking.
* If the `name`, `version` and `registry` had not been provided at all, jspm would default to linking as `local:dirname@master`,
  which enables a workflow to install private local folders. For packages that will be published, the linked name should typically 
  always match the name that will be published as this allows us to easily switch between the local and remote 
  versions without having to change package names.

We can now edit our `dist/jspm-react-component.js` file directly and changes will reflect in the app through
the linked package.

> SystemJS Hot Reloader is not currently setup to work with linked packages in jspm_packages.
  This would be a nice feature to have though.

Changes to the package.json file itself require the package to be relinked. This can be done via 
running `jspm link dist` again when we want package.json changes in the linked package to reflect in our app.

To unlink and revert to the remote installed package we use:

```
jspm install --unlink npm:jspm-react-component-demo
```

which will then switch back to installing the remote version of the package instead of symlinking.