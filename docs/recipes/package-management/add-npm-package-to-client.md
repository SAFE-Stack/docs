#How do I add an NPM package to the Client?

When you want to [call a Javascript library](https://fable.io/docs/communicate/js-from-fable.html) from your Client, it is easy to import and reference it using [NPM](https://docs.npmjs.com/cli/npm).

Run the following command:
```bash
npm install name-of-package
```

This will download the package into the solution's **node_modules** folder. 

You will also see a reference to the package in the Client's **package.json** file:
```json
"dependencies": {
    "name-of-package": "^1.0.0"
}
``` 

