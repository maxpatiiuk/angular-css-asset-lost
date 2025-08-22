# Angular never loads an external library .css asset

If external library has a CSS import, the asset will be processed by Angular (in
both dev server and build), but never actually loaded in the browser.

## Reproduction

1. Clone this repo

   ```sh
   git clone https://github.com/maxpatiiuk/angular-css-asset-lost
   cd angular-css-asset-lost
   ```

2. Install dependencies

   ```sh
   # To keep reproduction size minimal, I hardcoded a minimal root-level node_modules
   # So, install Angular dependencies only in the app folder:
   cd app
   npm install
   ```

3. Start the development server in the app folder

   ```sh
   npx ng serve
   ```

   See that the CSS is never loaded in the browser (page would turn green if it
   was loaded).

4. Build the app

   ```sh
   npx ng build
   ```

   Build output:

   ```sh
   > ng build

   Initial chunk files | Names         | Raw size | Estimated transfer size
   main-YOCO3XHU.js    | main          | 91.03 kB |                27.28 kB

                       | Initial total | 91.03 kB |                27.28 kB

   Lazy chunk files    | Names         | Raw size | Estimated transfer size
   main-M4DN6OYF.css   | -             | 56 bytes |                56 bytes
   ```

   The lazy chunk is created, but never referenced in the `index.html` or any
   `.js` file.

   You can serve the build in the browser to verify:

   ```sh
   npx serve dist/reproduction/browser -p 4201
   ```

See node_modules/external-library/ for the dummy external library with a CSS
file.

## Extra details

What I tried and does not fix the issue:

- package.json `sideEffects` setting (saying that .css or everything is side
  effect).
- package.json `exports` (explicitly exporting the .css file).
- reproduced in a real package that exports real things besides .css so this
  dummy package is not the issue.

In development server, setting `"optimization": true` build option fixes the
issue, but that does not work in production.
