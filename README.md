<div align="center">
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200"
      src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
  <h1>Style Loader</h1>
</div>

[![npm][npm]][npm-url]
[![node][node]][node-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]

# style-loader

Inject CSS into the DOM.

## Getting Started

To begin, you'll need to install `style-loader`:

```console
npm install --save-dev style-loader
```

or

```console
yarn add -D style-loader
```

or

```console
pnpm add -D style-loader
```

It's recommended to combine `style-loader` with the [`css-loader`](https://github.com/webpack-contrib/css-loader)

Then add the loader to your `webpack` config. For example:

**style.css**

```css
body {
  background: green;
}
```

**component.js**

```js
import "./style.css";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

## Options

- [**`injectType`**](#injecttype)
- [**`attributes`**](#attributes)
- [**`insert`**](#insert)
- [**`styleTagTransform`**](#styleTagTransform)
- [**`base`**](#base)
- [**`esModule`**](#esmodule)

### `injectType`

Type:

```ts
type injectType =
  | "styleTag"
  | "singletonStyleTag"
  | "autoStyleTag"
  | "lazyStyleTag"
  | "lazySingletonStyleTag"
  | "lazyAutoStyleTag"
  | "linkTag";
```

Default: `styleTag`

Allows to setup how styles will be injected into the DOM.

Possible values:

#### `styleTag`

Automatically injects styles into the DOM using multiple `<style></style>`. It is **default** behaviour.

**component.js**

```js
import "./styles.css";
```

Example with Locals (CSS Modules):

**component-with-css-modules.js**

```js
import styles from "./styles.css";

const divElement = document.createElement("div");
divElement.className = styles["my-class"];
```

All locals (class names) stored in imported object.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          // The `injectType`  option can be avoided because it is default behaviour
          { loader: "style-loader", options: { injectType: "styleTag" } },
          "css-loader",
        ],
      },
    ],
  },
};
```

The loader inject styles like:

```html
<style>
  .foo {
    color: red;
  }
</style>
<style>
  .bar {
    color: blue;
  }
</style>
```

#### `singletonStyleTag`

Automatically injects styles into the DOM using one `<style></style>`.

> ⚠ Source maps do not work.

**component.js**

```js
import "./styles.css";
```

**component-with-css-modules.js**

```js
import styles from "./styles.css";

const divElement = document.createElement("div");
divElement.className = styles["my-class"];
```

All locals (class names) stored in imported object.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: { injectType: "singletonStyleTag" },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

The loader inject styles like:

```html
<style>
  .foo {
    color: red;
  }
  .bar {
    color: blue;
  }
</style>
```

#### `autoStyleTag`

Works the same as a [`styleTag`](#styleTag), but if the code is executed in IE6-9, turns on the [`singletonStyleTag`](#singletonStyleTag) mode.

#### `lazyStyleTag`

Injects styles into the DOM using multiple `<style></style>` on demand.
We recommend following `.lazy.css` naming convention for lazy styles and the `.css` for basic `style-loader` usage (similar to other file types, i.e. `.lazy.less` and `.less`).
When you `lazyStyleTag` value the `style-loader` injects the styles lazily making them useable on-demand via `style.use()` / `style.unuse()`.

> ⚠️ Behavior is undefined when `unuse` is called more often than `use`. Don't do that.

**component.js**

```js
import styles from "./styles.lazy.css";

styles.use();
// For removing styles you can use
// styles.unuse();
```

**component-with-css-modules.js**

```js
import styles from "./styles.lazy.css";

styles.use();

const divElement = document.createElement("div");
divElement.className = styles.locals["my-class"];
```

All locals (class names) stored in `locals` property of imported object.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        exclude: /\.lazy\.css$/i,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.lazy\.css$/i,
        use: [
          { loader: "style-loader", options: { injectType: "lazyStyleTag" } },
          "css-loader",
        ],
      },
    ],
  },
};
```

The loader inject styles like:

```html
<style>
  .foo {
    color: red;
  }
</style>
<style>
  .bar {
    color: blue;
  }
</style>
```

#### `lazySingletonStyleTag`

Injects styles into the DOM using one `<style></style>` on demand.
We recommend following `.lazy.css` naming convention for lazy styles and the `.css` for basic `style-loader` usage (similar to other file types, i.e. `.lazy.less` and `.less`).
When you `lazySingletonStyleTag` value the `style-loader` injects the styles lazily making them useable on-demand via `style.use()` / `style.unuse()`.

> ⚠️ Source maps do not work.

> ⚠️ Behavior is undefined when `unuse` is called more often than `use`. Don't do that.

**component.js**

```js
import styles from "./styles.css";

styles.use();
// For removing styles you can use
// styles.unuse();
```

**component-with-css-modules.js**

```js
import styles from "./styles.lazy.css";

styles.use();

const divElement = document.createElement("div");
divElement.className = styles.locals["my-class"];
```

All locals (class names) stored in `locals` property of imported object.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        exclude: /\.lazy\.css$/i,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.lazy\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: { injectType: "lazySingletonStyleTag" },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

The loader generate this:

```html
<style>
  .foo {
    color: red;
  }
  .bar {
    color: blue;
  }
</style>
```

#### `lazyAutoStyleTag`

Works the same as a [`lazyStyleTag`](#lazyStyleTag), but if the code is executed in IE6-9, turns on the [`lazySingletonStyleTag`](#lazySingletonStyleTag) mode.

#### `linkTag`

Injects styles into the DOM using multiple `<link rel="stylesheet" href="path/to/file.css">` .

> ℹ️ The loader will dynamically insert the `<link href="path/to/file.css" rel="stylesheet">` tag at runtime via JavaScript. You should use [MiniCssExtractPlugin](https://webpack.js.org/plugins/mini-css-extract-plugin/) if you want to include a static `<link href="path/to/file.css" rel="stylesheet">`.

```js
import "./styles.css";
import "./other-styles.css";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.link\.css$/i,
        use: [
          { loader: "style-loader", options: { injectType: "linkTag" } },
          { loader: "file-loader" },
        ],
      },
    ],
  },
};
```

The loader generate this:

```html
<link rel="stylesheet" href="path/to/style.css" />
<link rel="stylesheet" href="path/to/other-styles.css" />
```

### `attributes`

Type:

```ts
type attributes = HTMLAttributes;
```

Default: `{}`

If defined, the `style-loader` will attach given attributes with their values on `<style>` / `<link>` element.

**component.js**

```js
import style from "./file.css";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          { loader: "style-loader", options: { attributes: { id: "id" } } },
          { loader: "css-loader" },
        ],
      },
    ],
  },
};
```

```html
<style id="id"></style>
```

### `insert`

Type:

```ts
type insert =
  | string
  | ((htmlElement: HTMLElement, options: Record<string, any>) => void);
```

Default: `head`

By default, the `style-loader` appends `<style>`/`<link>` elements to the end of the style target, which is the `<head>` tag of the page unless specified by `insert`.
This will cause CSS created by the loader to take priority over CSS already present in the target.
You can use other values if the standard behavior is not suitable for you, but we do not recommend doing this.
If you target an [iframe](https://developer.mozilla.org/en-US/docs/Web/API/HTMLIFrameElement) make sure you have sufficient access rights, the styles will be injected into the content document head.

#### `string`

##### `Selector`

Allows to setup custom [query selector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) where styles inject into the DOM.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              insert: "body",
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

##### `Absolute path to function`

Allows to setup absolute path to custom function that allows to override default behavior and insert styles at any position.

> ⚠ Do not forget that this code will be used in the browser and not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc. We recommend using [`babel-loader`](https://webpack.js.org/loaders/babel-loader/) for support latest ECMA features.
> ⚠ Do not forget that some DOM methods may not be available in older browsers, we recommended use only [DOM core level 2 properties](https://caniuse.com/#search=DOM%20Core), but it is depends what browsers you want to support

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              insert: require.resolve("modulePath"),
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

A new `<style>`/`<link>` elements will be inserted into at bottom of `body` tag.

#### `function`

Allows to override default behavior and insert styles at any position.

> ⚠ Do not forget that this code will be used in the browser and not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc, we recommend use only ECMA 5 features, but it is depends what browsers you want to support
> ⚠ Do not forget that some DOM methods may not be available in older browsers, we recommended use only [DOM core level 2 properties](https://caniuse.com/#search=DOM%20Core), but it is depends what browsers you want to support

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              insert: function insertAtTop(element) {
                var parent = document.querySelector("head");
                // eslint-disable-next-line no-underscore-dangle
                var lastInsertedElement =
                  window._lastElementInsertedByStyleLoader;

                if (!lastInsertedElement) {
                  parent.insertBefore(element, parent.firstChild);
                } else if (lastInsertedElement.nextSibling) {
                  parent.insertBefore(element, lastInsertedElement.nextSibling);
                } else {
                  parent.appendChild(element);
                }

                // eslint-disable-next-line no-underscore-dangle
                window._lastElementInsertedByStyleLoader = element;
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

Insert styles at top of `head` tag.

You can pass any parameters to `style.use(options)` and this value will be passed to `insert` and `styleTagTransform` functions.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              injectType: "lazyStyleTag",
              // Do not forget that this code will be used in the browser and
              // not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc,
              // we recommend use only ECMA 5 features,
              // but it is depends what browsers you want to support
              insert: function insertIntoTarget(element, options) {
                var parent = options.target || document.head;

                parent.appendChild(element);
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

Insert styles to the provided element or to the `head` tag if target isn't provided. Now you can inject styles into Shadow DOM (or any other element).

**custom-square.css**

```css
div {
  width: 50px;
  height: 50px;
  background-color: red;
}
```

**custom-square.js**

```js
import customSquareStyles from "./custom-square.css";

class CustomSquare extends HTMLElement {
  constructor() {
    super();

    this.attachShadow({ mode: "open" });

    const divElement = document.createElement("div");

    divElement.textContent = "Text content.";

    this.shadowRoot.appendChild(divElement);

    customSquareStyles.use({ target: this.shadowRoot });

    // You can override injected styles
    const bgPurple = new CSSStyleSheet();
    const width = this.getAttribute("w");
    const height = this.getAttribute("h");

    bgPurple.replace(`div { width: ${width}px; height: ${height}px; }`);

    this.shadowRoot.adoptedStyleSheets = [bgPurple];

    // `divElement` will have `100px` width, `100px` height and `red` background color
  }
}

customElements.define("custom-square", CustomSquare);

export default CustomSquare;
```

### `styleTagTransform`

Type:

```ts
type styleTagTransform =
  | string
  | ((
      css: string,
      styleElement: HTMLStyleElement,
      options: Record<string, any>
    ) => void);
```

Default: `undefined`

#### `string`

Allows to setup absolute path to custom function that allows to override default behavior styleTagTransform.

> ⚠ Do not forget that this code will be used in the browser and not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc, we recommend use only ECMA 5 features, but it is depends what browsers you want to support

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              injectType: "styleTag",
              styleTagTransform: require.resolve("module-path"),
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

#### `function`

Transform tag and css when insert 'style' tag into the DOM.

> ⚠ Do not forget that this code will be used in the browser and not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc, we recommend use only ECMA 5 features, but it is depends what browsers you want to support
> ⚠ Do not forget that some DOM methods may not be available in older browsers, we recommended use only [DOM core level 2 properties](https://caniuse.com/#search=DOM%20Core), but it is depends what browsers you want to support

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              injectType: "styleTag",
              styleTagTransform: function (css, style) {
                // Do something ...
                style.innerHTML = `${css}.modify{}\n`;

                document.head.appendChild(style);
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

### `base`

```ts
type base = number;
```

This setting is primarily used as a workaround for [css clashes](https://github.com/webpack-contrib/style-loader/issues/163) when using one or more [DllPlugin](https://robertknight.me.uk/posts/webpack-dll-plugins/)'s. `base` allows you to prevent either the _app_'s css (or _DllPlugin2_'s css) from overwriting _DllPlugin1_'s css by specifying a css module id base which is greater than the range used by _DllPlugin1_ e.g.:

**webpack.dll1.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

**webpack.dll2.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          { loader: "style-loader", options: { base: 1000 } },
          "css-loader",
        ],
      },
    ],
  },
};
```

**webpack.app.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          { loader: "style-loader", options: { base: 2000 } },
          "css-loader",
        ],
      },
    ],
  },
};
```

### `esModule`

Type:

```ts
type esModule = boolean;
```

Default: `true`

By default, `style-loader` generates JS modules that use the ES modules syntax.
There are some cases in which using ES modules is beneficial, like in the case of [module concatenation](https://webpack.js.org/plugins/module-concatenation-plugin/) and [tree shaking](https://webpack.js.org/guides/tree-shaking/).

You can enable a CommonJS modules syntax using:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        loader: "style-loader",
        options: {
          esModule: false,
        },
      },
    ],
  },
};
```

## Examples

### Recommend

For `production` builds it's recommended to extract the CSS from your bundle being able to use parallel loading of CSS/JS resources later on.
This can be achieved by using the [mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin), because it creates separate css files.
For `development` mode (including `webpack-dev-server`) you can use `style-loader`, because it injects CSS into the DOM using multiple `<style></style>` and works faster.

> ⚠ Do not use together `style-loader` and `mini-css-extract-plugin`.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== "production";

module.exports = {
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          devMode ? "style-loader" : MiniCssExtractPlugin.loader,
          "css-loader",
          "postcss-loader",
          "sass-loader",
        ],
      },
    ],
  },
  plugins: [].concat(devMode ? [] : [new MiniCssExtractPlugin()]),
};
```

### Named export for CSS Modules

> ⚠ Names of locals are converted to `camelCase`.

> ⚠ It is not allowed to use JavaScript reserved words in css class names.

> ⚠ Options `esModule` and `modules.namedExport` in `css-loader` should be enabled.

**styles.css**

```css
.foo-baz {
  color: red;
}
.bar {
  color: blue;
}
```

**index.js**

```js
import { fooBaz, bar } from "./styles.css";

console.log(fooBaz, bar);
```

You can enable a ES module named export using:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: "style-loader",
          },
          {
            loader: "css-loader",
            options: {
              modules: {
                namedExport: true,
              },
            },
          },
        ],
      },
    ],
  },
};
```

### Source maps

The loader automatically inject source maps when previous loader emit them.
Therefore, to generate source maps, set the `sourceMap` option to `true` for the previous loader.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          "style-loader",
          { loader: "css-loader", options: { sourceMap: true } },
        ],
      },
    ],
  },
};
```

### Nonce

There are two ways to work with `nonce`:

- using the `attributes` option
- using the `__webpack_nonce__` variable

> ⚠ the `attributes` option takes precedence over the `__webpack_nonce__` variable

#### `attributes`

**component.js**

```js
import "./style.css";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              attributes: {
                nonce: "12345678",
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

The loader generate:

```html
<style nonce="12345678">
  .foo {
    color: red;
  }
</style>
```

#### `__webpack_nonce__`

**create-nonce.js**

```js
__webpack_nonce__ = "12345678";
```

**component.js**

```js
import "./create-nonce.js";
import "./style.css";
```

Alternative example for `require`:

**component.js**

```js
__webpack_nonce__ = "12345678";

require("./style.css");
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

The loader generate:

```html
<style nonce="12345678">
  .foo {
    color: red;
  }
</style>
```

#### Insert styles at top

Inserts styles at top of `head` tag.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              insert: function insertAtTop(element) {
                var parent = document.querySelector("head");
                var lastInsertedElement =
                  window._lastElementInsertedByStyleLoader;

                if (!lastInsertedElement) {
                  parent.insertBefore(element, parent.firstChild);
                } else if (lastInsertedElement.nextSibling) {
                  parent.insertBefore(element, lastInsertedElement.nextSibling);
                } else {
                  parent.appendChild(element);
                }

                window._lastElementInsertedByStyleLoader = element;
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

#### Insert styles before target element

Inserts styles before `#id` element.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              insert: function insertBeforeAt(element) {
                const parent = document.querySelector("head");
                const target = document.querySelector("#id");

                const lastInsertedElement =
                  window._lastElementInsertedByStyleLoader;

                if (!lastInsertedElement) {
                  parent.insertBefore(element, target);
                } else if (lastInsertedElement.nextSibling) {
                  parent.insertBefore(element, lastInsertedElement.nextSibling);
                } else {
                  parent.appendChild(element);
                }

                window._lastElementInsertedByStyleLoader = element;
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

#### Custom Elements (Shadow DOM)

You can define custom target for your styles for the `lazyStyleTag` type.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: "style-loader",
            options: {
              injectType: "lazyStyleTag",
              // Do not forget that this code will be used in the browser and
              // not all browsers support latest ECMA features like `let`, `const`, `arrow function expression` and etc,
              // we recommend use only ECMA 5 features,
              // but it is depends what browsers you want to support
              insert: function insertIntoTarget(element, options) {
                var parent = options.target || document.head;

                parent.appendChild(element);
              },
            },
          },
          "css-loader",
        ],
      },
    ],
  },
};
```

Insert styles to the provided element or to the `head` tag if target isn't provided.

**custom-square.css**

```css
div {
  width: 50px;
  height: 50px;
  background-color: red;
}
```

**custom-square.js**

```js
import customSquareStyles from "./custom-square.css";

class CustomSquare extends HTMLElement {
  constructor() {
    super();

    this.attachShadow({ mode: "open" });

    const divElement = document.createElement("div");

    divElement.textContent = "Text content.";

    this.shadowRoot.appendChild(divElement);

    customSquareStyles.use({ target: this.shadowRoot });

    // You can override injected styles
    const bgPurple = new CSSStyleSheet();
    const width = this.getAttribute("w");
    const height = this.getAttribute("h");

    bgPurple.replace(`div { width: ${width}px; height: ${height}px; }`);

    this.shadowRoot.adoptedStyleSheets = [bgPurple];

    // `divElement` will have `100px` width, `100px` height and `red` background color
  }
}

customElements.define("custom-square", CustomSquare);

export default CustomSquare;
```

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.

[CONTRIBUTING](./.github/CONTRIBUTING.md)

## License

[MIT](./LICENSE)

[npm]: https://img.shields.io/npm/v/style-loader.svg
[npm-url]: https://npmjs.com/package/style-loader
[node]: https://img.shields.io/node/v/style-loader.svg
[node-url]: https://nodejs.org
[tests]: https://github.com/webpack-contrib/style-loader/workflows/style-loader/badge.svg
[tests-url]: https://github.com/webpack-contrib/style-loader/actions
[cover]: https://codecov.io/gh/webpack-contrib/style-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/style-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=style-loader
[size-url]: https://packagephobia.now.sh/result?p=style-loader
