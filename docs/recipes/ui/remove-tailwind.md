By default, a full SAFE-stack application uses Tailwind CSS for styling. You might not always want to manage your styling using Tailwind, for example because you want to use a CSS framework like [Bulma](https://bulma.io/). In this recipe we describe how to fully remove Tailwind

## 1. Remove Tailwind css classes 

Tailwind uses classes to style UI elements. In `src/Client`, search for all occurances of `prop.className` and `prop.classes` and remove them if they are used for Tailwind support. In a vanilla SAFE template installation, this means removing **all** occurrences of `prop.className`.


## 2. Uninstall NPM packages

Remove NPM packages that were installed for tailwind using

```
 npm uninstall tailwindcss postcss autoprefixer
```

## 3. Remove configuration files

Remove the following files:

```
src/Client/postcss.config.js
src/Client/tailwind.config.js
src/Client/index.css
```

Your SAFE is now tailwind-free.