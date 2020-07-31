## Sass Demo

This is a repro of [this issue](https://github.com/vercel/next.js/issues/15753). 
To get started:

1. First, clone [this repository](https://github.com/nicholaschiang/with-sass) and CD inside:

```
$ git clone https://github.com/nicholaschiang/with-sass
$ cd with-sass/
```

2. Then, make sure you have [Yarn installed](https://yarnpkg.com/getting-started/install) then run:

```
$ yarn
```

3. Finally, start the development server by running:

```
$ yarn dev
```

### Error

You'll notice that when you run `yarn dev` the Next.js server starts up properly
but the `sass-loader` is unable to resolve my dependency's imports.

It seems to import the `@material/elevation` package just fine:

```scss
// styles/home.module.scss
@use '@material/elevation
```

But it doesn't seem to be able to import `@material/animation` for the
`@material/elevation` package:

```
error - ./styles/home.module.scss (./.yarn/$$virtual/css-loader-virtual-393ee7c517/0/cache/css-loader-npm-3.5.3-0f886851e6-910936f0ac.zip/node_modules/css-loader/dist/cjs.js??ref--5-oneOf-3-1!./.yarn/$$virtual/next-virtual-4df8b3b97e/0/cache/next-npm-9.5.1-e14f31e6e9-effa9056b8.zip/node_modules/next/dist/compiled/postcss-loader??__nextjs_postcss!./.yarn/cache/resolve-url-loader-npm-3.1.1-cf1a268137-7b113ac9e6.zip/node_modules/resolve-url-loader??ref--5-oneOf-3-3!./.yarn/$$virtual/sass-loader-virtual-dbafe3ebab/0/cache/sass-loader-npm-8.0.2-f0d209ad64-e23d9b308f.zip/node_modules/sass-loader/dist/cjs.js??ref--5-oneOf-3-4!./styles/home.module.scss)
SassError: Can't find stylesheet to import.
   ╷
23 │ @use "@material/animation/variables";
   │ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   ╵
  .yarn/cache/@material-elevation-npm-7.0.0-aaa8862010-5ea26d080e.zip/node_modules/@material/elevation/_variables.scss 23:1  @forward
  .yarn/cache/@material-elevation-npm-7.0.0-aaa8862010-5ea26d080e.zip/node_modules/@material/elevation/_index.scss 1:1       @use
  /home/nchiang/repos/with-sass/styles/home.module.scss 1:1                                                                                                                  root stylesheet
```

I've had this error before when using Next.js with 
[MWC](https://github.com/material-components/material-components-web/) but was
able to fix it by specifying an `includePaths` configuration in `next.config.js`
like so:

```javascript
// next.config.js
const path = require('path');

module.exports = {
  sassOptions: {
    includePaths: [
      path.resolve(__dirname, 'node_modules'),
    ],
  },
};
```

Now, I'm not sure how to tell `sass-loader` to use Yarn PNP's module resolution.
