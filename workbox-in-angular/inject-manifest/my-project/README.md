# MyProject

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 1.7.2.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `-prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

## Workbox Generate Steps:


1. First we can create new Angular Project with Angular-CLI with command: `ng new my-project`

2. Install dependencies (if not automated) with `npm i` or `yarn install`

3. Add dependency `workbox-build` with command:
   npm: `npm i --save-dev workbox-build`
   yarn: `yarn add workbox-build -D`

4. We need to create template for our `sw.js`, let's create new file called `sw-template.js` in our root folder.

5. Add this script as in `sw-template.js` file:

  ```js
  // This is required line
  workbox.precaching.precacheAndRoute([]);

  workbox.routing.registerRoute(
    new RegExp('^https://fonts.(?:googleapis|gstatic).com/(.*)'),
    workbox.strategies.cacheFirst(),
  );

  workbox.routing.registerRoute(
    /\.(?:js|css)$/,
    workbox.strategies.staleWhileRevalidate(),
  );

  workbox.routing.registerRoute(
    /\.(?:png|gif|jpg|jpeg|svg)$/,
    workbox.strategies.cacheFirst({
      cacheName: 'images',
      plugins: [
        new workbox.expiration.Plugin({
          maxEntries: 60,
          maxAgeSeconds: 30 * 24 * 60 * 60, // 30 Days
        }),
      ],
    }),
  );
  ```

6. Create new file for generating our service worker, let's name it `sw-build.js`, we put in root folder.

7. Add simple setup in file `sw-build.js` with script like this:

  ```js
  const { injectManifest } = require('workbox-build');

  const swSrc = './sw-template.js';
  const swDest = 'dist/sw.js';
  injectManifest({
    swSrc,
    swDest,
    globDirectory: 'dist'
  }).then(({count, size}) => {
    console.log(`Generated ${swDest}, which will precache ${count} files, totaling ${size} bytes.`);
  });
  ```

8. Try running build for first time with command: `npm run build`
  It will create new folder `./dist` as our output result.

9. Try running `workbox-build` script with `node ./sw-build.js`.
  It should create file `sw.js` in `./dist` folder.

10. Automate in build process. Just modify `build` script in `package.json`

  ```js
  "scripts": {
    "build": "ng build --prod && node ./sw-build.js"
  }
  ```

11. Check code sample here: https://github.com/mazipan/workbox-in-js-framework/tree/master/workbox-in-angular/inject-manifest/my-project


## Using Workbox-CLI

1. Install `Workbox CLI` with command `npm install workbox-cli --global` or `yarn global add workbox-cli`

2. Create config file, `workbox-config.js` in root folder

3. Add this script in this file:

  ```js
  module.exports =  {
    swSrc: './sw-template.js',
    swDest: 'dist/sw.js',
    globDirectory: 'dist'
  }
  ```

4. Run script with command: `workbox injectManifest ./workbox-config.js`

5. Automate with adding script in your build process in `package.json`

6. Check code sample here: https://github.com/mazipan/workbox-in-js-framework/tree/master/workbox-in-angular/inject-manifest/my-project


## Manual Install Service Worker in Angular

Add this below script in `src/main.ts` file:

```js
platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .then(() => registerServiceWorker())
  .catch(err => console.log(err));

function registerServiceWorker() {
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker
      .register('/sw.js')
      .then(reg => {
        console.log('Service Worker has been registered');
      })
      .catch(e =>
        console.error('Error during service worker registration:', e)
      );
  } else {
    console.warn('Service Worker is not supported');
  }
}
```

