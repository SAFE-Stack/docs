Hot Module Replacement (HMR) allows to update the UI of an application while it is running, without a full reload. In SAFE stack apps, this can dramatically speed up the development for web and mobile GUIs, since there is no need to "stop" and "reload" an application. Instead, you can make changes to your views and have them immediately update in the browser, without the need to restart the application.

## How does it work?
In case of web development, the [vite](https://vitejs.dev/) development server will automatically refresh the changed parts of your [elmish](https://github.com/elmish/elmish) views whenever you save a file. Alternatively, in the case of mobile app development, this is achieved through [React Native](https://facebook.github.io/react-native/)'s own bundler.

## Why does it work so well with SAFE?
Since SAFE uses the Model-View-Update architecture with immutable models, the application state only changes when a message is processed; this fits the HMR model very nicely. Here's an example of HMR in action to change the input of a textbox to automatically convert the input to upper case.

![](https://user-images.githubusercontent.com/1781813/39061043-5aa3737e-44c3-11e8-9789-029b285f180c.gif)

## Further reading
* [Hot Module Replacement](https://vitejs.dev/guide/features.html#hot-module-replacement) via Vite
* [Introducing Hot Reloading](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html) in React Native
