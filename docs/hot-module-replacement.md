## What is Hot Module Replacement (HMR)?

Hot Module Replacement (HMR) allows to update the UI of an application while it is running, without a full reload. 

In SAFE stack this can speed up the development for web and mobile GUIs. In case of web development the [webpack](https://webpack.js.org/) development server will automatically refresh the changed parts of your [elmish](https://github.com/fable-elmish/elmish) views whenever you save a file. In the case of mobile app development it is done by [React Native](https://facebook.github.io/react-native/)'s own bundler.

## Why is it working so well with SAFE?

Since SAFE is using the Model-View-Update architecture with immutable models, the application state only changes when a message is processed.
This is fitting the HMR model very nicely.

## Further reading

* [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) via webpack
* [Introducing Hot Reloading](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html) in React Native
