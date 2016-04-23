## Peer Dependencies

Peer dependencies on jspm follow the same principle as npm - that there can only be one version
of a peer dependency package in jspm.

The way jspm handles this is by:

* Installing the peer dependency into the base-level SystemJS map configuration as if it had been installed
  as a primary dependency in the first place.
* Adding the peer dependency itself to the package.json peerDependencies property of the app. This way
  every app that installs a peer dependency manages the install constraints of that peer dependency itself.
* Peer dependency conflicts happen when one package expects the peer dependency to be installed to one range (say `npm:react@^0.14.0`),
  while another package expects the peer dependency to be installed to another (like `npm:react@~0.13.2`).
* Whenever there is a peer dependency conflict, a prompt is provided to select the correct resolution range to install
  the dependency to. In this way peer dependencies never error in jspm, but may not work when out of range.
  Think of it just like traditional package management without encapsulated multi-version support.

To demonstrate the conflict resolution, let's set up exactly the scenario above.

First uninstall our published component which will provide the conflict:

```
jspm uninstall jspm-react-component-demo
```

Then install React to an invalid peer range:

```
jspm install npm:react@~0.13.2 --peer
```

Now if we install the published component that has a peer dependency on `npm:react@0.14.0`:

```
jspm install npm:jspm-react-component-demo
```

the peer dependency conflict resolution prompt is displayed:

```
     Updating registry cache...

     Peer dependency conflict for react:
       Currently installed as npm:react@0.13.3.
       Package npm:jspm-react-component-demo@0.1.1 requires npm:react@^0.14.6.

Would you like to install react to the new install range? [Yes]: 
```

If we select the default `Yes` option, we upgrade to React 0.14.6 and the remaining install runs smoothly.

Alternatively, if we enter `No`, we get the following prompt:

```
Would you like to retain the current resolution? [Yes]: 
```

If we select `No` again we then get:

```
Enter a custom package resolution range: 
```

We can now enter any install target, even something like `npm:my-react-fork@dev`.