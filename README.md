# Browserslist Example

<img align="right" width="120" height="120"
     src="https://browserslist.github.io/browserslist/logo.svg"
     alt="Browserslist logo by Anton Lovchikov">

[Browserslist] is a config to share target browsers
between different front-end tools.

This repository shows how Browserslist is used by:

* **[developers](#developers)**
* **[Autoprefixer](#autoprefixer)**
* **[Babel](#babel)**
* **[postcss-preset-env](#postcss-preset-env)**
* **[postcss-normalize](#postcss-normalize)**
* **[ESLint](#eslint)**
* **[Stylelint](#stylelint)**

See [`package.json`] for config example.

<a href="https://evilmartians.com/?utm_source=browserslist-example">
  <img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg"
       alt="Sponsored by Evil Martians" width="236" height="54">
</a>

[`package.json`]: ./package.json
[Browserslist]:   https://github.com/ai/browserslist

## How to Add Browserslist to Your Project

Browserslist config can be defined in `.browserslistrc` file
or in `browserslist` section of `package.json`.

In this example, we defined target browsers in `package.json`
to reduce config files in project dir:

```diff
{
  "private": true,
+ "browserslist": [
+   "Edge 16"
+ ],
  "scripts": {
  }
}
```

Browserslist config is a list of browsers queries
(like `last 2 Chrome versions`). You can find [queries list]
in Browserslist docs.

[queries list]: https://github.com/ai/browserslist#queries


## Who Uses Browserslist

### Developers

Yes, Browserslist is useful not only for tools.
Browserslist is a standard place to find project’s target browsers.

With Browserslist config new developers will not bother you
with questions like “What browsers do we support?”
or “Do we support IE 11”? They will find an answer in the config.

Also, Browserslist has CLI tool to show what browser versions
is selected by project’s Browserslist queries:

```sh
$ npx browserslist
edge 16
```


### Autoprefixer

Autoprefixer is a [PostCSS] plugin to add vendor prefixes to CSS.
It adds only actual prefixes according to Browserslist’s target browsers
and [Can I Use] data.

Let’s install it:

```sh
$ npm install postcss-cli autoprefixer
```

and add to `package.json`:

```diff
  "browserslist": [
    "Edge 16"
  ],
+ "postcss": {
+   "plugins": {
+     "autoprefixer": {}
+   }
+ },
  "scripts": {
+   "build:css": "postcss src/app.css -o build/app.css",
+   "build": "npm run build:css"
  }
```

Now we can forget about prefixes and write clean code in [`src/app.css`]:

```css
body {
  user-select: none
}
```

Autoprefixer will compile it to:

```css
body {
  -ms-user-select: none;
      user-select: none
}
```

As you can see, Autoprefixer added only `-ms-` prefixes because
Browserslist told that we support only Edge 16.

[`src/app.css`]: ./src/app.css
[Can I Use]:     http://caniuse.com/
[PostCSS]:       http://postcss.org/


### Babel

[Babel] is a tool to compile JS files. The most popular way to use it
is to compile future JS syntaxes to JS supported by target browsers.
From Babel 7.0 `@babel/preset-env` loads target browsers
from the same Browserslist config.

Let’s install it:

```sh
$ npm install @babel/core @babel/cli @babel/preset-env
```

and add to `package.json`:

```diff
  "postcss": {
    "plugins": {
      "autoprefixer": {}
    }
  },
+ "babel": {
+   "presets": [
+     [
+       "@babel/preset-env"
+     ]
+   ]
+ },
  "scripts": {
    "build:css": "postcss src/app.css -o build/app.css",
+   "build:js": "babel src/app.js -o dist/app.js",
-   "build": "npm run build:css"
+   "build": "npm run build:css && npm run build:js"
  }
```

Now we can write future JS syntax in [`src/app.js`]:

```js
const array = [1, 2, 3];
const [first, second] = array;
```

Babel will compile it for Edge 16:

```js
const array = [1, 2, 3];
const first = array[0],
      second = array[1];
```

Note, that Babel doesn’t change `const` since Edge 16 supports it.

[`src/app.js`]: ./src/app.js
[Babel]:        https://babeljs.io/


### PostCSS Preset Env

[`postcss-preset-env`] is a “Babel for CSS.” It compiles future CSS syntax
to CSS supported by target browsers. It is similar to cssnext, but more accurate
with CSS spec and has `stage` option.

Let’s install it:

```sh
$ npm install postcss-preset-env
```

and add to `package.json`:

```diff
  "postcss": {
    "plugins": {
+     "postcss-preset-env": {},
      "autoprefixer": {}
    }
  },
```

Now we can write future CSS in [`src/app.css`]:

```css
.popup {
  --text-color: black;
  color: var(--text-color);
  background-color: #bbb9;
}
```

And `postcss-preset-env` will compile it for Edge 16:

```css
.popup {
  --text-color: black;
  color: var(--text-color);
  background-color: rgba(187, 187, 187, 0.6);
}
```

Note, plugin replaced `#bbb9` to `rgba()`, but didn’t replace CSS Custom
Properties, because Edge 16 supports them.

[`postcss-preset-env`]: https://github.com/jonathantneal/postcss-preset-env
[`src/app.css`]: ./src/app.css


### PostCSS Normalize

Browsers have different default styles. To have same styles
in all browsers we need to “normalize” these differences.

[`postcss-normalize`] is a fork of popular Normalize.css with 2 differents:
it doesn’t have opinionated styles, and it adds only necessary fixes according
Browserslist target browsers.

Let’s install it;

```sh
$ npm install postcss-normalize
```

and add to `package.json`:

```diff
  "postcss": {
    "plugins": {
+     "postcss-normalize": {},
      "postcss-preset-env": {},
      "autoprefixer": {}
    }
  },
```

Now plugin will replace `@import-normalize;` to CSS which normalize
Edge 16 styles. You can check output in [`dist/app.css`].

Note that inserted CSS is smaller than standard Normalize.css.
`postcss-normalize` added rules necessary only for Edge 16.

[`postcss-normalize`]: https://github.com/jonathantneal/postcss-normalize
[`dist/app.css`]:      ./dist/app.css


### ESLint

[ESLint] finds mistakes in your JS. [`eslint-plugin-compat`]
is a plugin to ESLint to warn that JS will not work in all target browsers.

Let’s install it:

```sh
$ npm install eslint eslint-plugin-compat
```

and add to `package.json`:

```diff
+ "eslintConfig": {
+   "parserOptions": {
+     "ecmaVersion": 6
+   },
+   "plugins": [
+     "compat"
+   ],
+   "rules": {
+     "compat/compat": "warn"
+   }
+ },
  "scripts": {
    "build:css": "postcss src/app.css -o dist/app.css",
    "build:js": "babel src/app.js -o dist/app.js",
+   "lint:js": "eslint src/*.js",
+   "test": "npm run lint:js",
    "build": "npm run build:css && npm run build:js"
  },
```

Now, if you will write `navigator.serviceWorker`, ESLint will warn
you that Edge 16 doesn’t support Service Workers:

```
$ npm test

> eslint src/*.js

/browserslist-example/src/app.js
  3:1  warning  navigator.serviceWorker() is not supported in Edge 16  compat/compat

✖ 1 problem (0 errors, 1 warning)
```

[`eslint-plugin-compat`]: https://github.com/amilajack/eslint-plugin-compat
[ESLint]:                 https://eslint.org


### Stylelint

[Stylelint] is a “ESLint for CSS” to warn you about mistakes in CSS.
It also has [`stylelint-no-unsupported-browser-features`] plugin
to warn you when CSS property will not work in all target browsers.

Let’s install it:

```sh
$ npm install stylelint stylelint-no-unsupported-browser-features
```

and add to `package.json`:

```diff
+ "stylelint": {
+   "plugins": [
+     "stylelint-no-unsupported-browser-features"
+   ],
+   "rules": {
+     "plugin/no-unsupported-browser-features": true
+   },
+   "defaultSeverity": "warning"
+ },
  "scripts": {
    "build:css": "postcss src/app.css -o dist/app.css",
    "build:js": "babel src/app.js -o dist/app.js",
+   "lint:css": "stylelint src/*.css",
    "lint:js": "eslint src/*.js",
-   "test": "npm run lint:js",
+   "test": "npm run lint:js && npm run lint:css",
    "build": "npm run build:css && npm run build:js"
  },
```

Now, if you will write `will-change: transform` in your CSS, Stylelint will warn
you that Edge 16 doesn’t support this property:

```
$ npm test

> stylelint src/*.css

src/app.css
 14:3  ⚠  Unexpected browser feature     plugin/no-unsupported-browser-features
          "will-change" is not                                                 
          supported by Edge 16
```

[`stylelint-no-unsupported-browser-features`]: https://github.com/ismay/stylelint-no-unsupported-browser-features
[Stylelint]:                                   https://stylelint.io
