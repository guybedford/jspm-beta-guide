## npm Package Support

The fact that we can install express through jspm and [have React work both client 
and server](universal-js-with-server-rendering.md) without any custom configuration is due to the completely rewritten Node conversion 
layer in the npm registry endpoint of jspm 0.17.

This layer handles converting the NodeJS resolution algorithm into SystemJS package configuration
for every package installed from npm, handling each resolution difference between the systems
through custom meta and map configuration, respecting Node core libs to work client 
and server, as well as supporting the `browser` field in the package.json of npm packages.

This support for the `browser` field is then managed through the conditional loading
mechanism of SystemJS which is covered in the last two sections of this tutorial.

The above gives us almost complete parity with npm package features, with the 
remaining limitations of not supporting browserify transforms, npm install 
hooks, executable bin path creation and native addons.