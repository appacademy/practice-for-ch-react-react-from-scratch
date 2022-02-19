# Setting Up a React Project From Scratch

While Create React App is a great tool for easily setting up a React project,
and there's no reason not to use it in most cases, it is useful to know how to
set up a React project from scratch. Getting comfortable with build tools and
project setup is a great transferable skill to have as a frontend developer, and
it can help for interviews too.

## Step 1: NPM and __package.json__

This section will cover the configuration process for automating node module
installation for apps that have multiple JS dependencies.

### Generating __package.json__ with `npm init`

Much like Ruby's __Gemfile__, the Node Package Manager can be used with a manifest
file that lists all of an app's JavaScript dependencies. This file is called
__package.json__. While you can write this file by hand, NPM's CLI (Command Line
Interface) significantly simplifies the process.

To initialize an app with NPM, first create a directory for your app--e.g.,
__test/__. Then, `cd` into this directory and run the following command:

```
npm init --yes
```

This generates a __package.json__ file with default values for common, important
metadata fields like `"name"` and `"version"`. If you omit the `--yes` flag, you
will enter an interactive session in your terminal which will prompt you to
provide your own values for each of the default fields (or use the default).

You __package.json__ should look something like this:

```json
// package.json

{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Don't worry about the default settings above; they won't affect how your app
runs, and you can always adjust them later.

### Installing Packages

Once you've generated a __package.json__, you can start installing NPM packages
such as `react`, `react-dom`, and `react-router-dom` via the command line:

```sh
npm install <package>
```

You can also install multiple packages at once:

```sh
npm install <first_package> <second_package>
```

When you run this command, NPM will look up each package by its name and
automatically download it into a folder in your app called __node_modules__. It
will also add it to your __package.json__ as a dependency.

By default, NPM will install the latest version of a package. If you need a
specific version of a package, just attach `@<version>`. The `<version>` portion
follows NPM's [semantic versioning syntax][semver]:

Go ahead and install the latest versions of `react` and `react-dom`, as well as
the latest `5.x` version of `react-router-dom`:

```sh
npm install react react-dom react-router-dom@5
```

The following will automatically be added to your app's __package.json__ (the
exact version numbers might differ):

```json
// package.json

{
  // ...
  "dependencies": {
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-router-dom": "^5.3.0"  
  },
  // ...
}
```

Now anyone who gets a copy of your app can download all its dependencies in one
fell swoop by running the following from the app's directory (with no
arguments):

```
npm install
```

Since you'll need `webpack` and `webpack-cli` to bundle your code, you'll
install those now as well.

You'll also install [`webpack-dev-server`], which, used with the command
`webpack serve`, provides an alternative to `webpack --watch`. With
`webpack-dev-server`, you'll access your app at `localhost` instead of opening
your __index.html__ file directly. (If this sounds familiar, it's probably
because Create React App uses `webpack-dev-server` under the hood.)

`webpack-dev-server` provides benefits like automatically reloading your app
whenever you change the source code. In addition, it never actually creates
output files for your bundled code, storing that code in memory instead. This
saves time and keeps your project files a bit more tidy.

```sh
npm install webpack webpack-cli webpack-dev-server
```

The following gets added to your app's __package.json__ (the exact version
numbers might differ for you):

```json
// package.json

{
  // ...
  "dependencies": {
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-router-dom": "^5.3.0",
    "webpack": "^5.68.0",
    "webpack-cli": "^4.9.2",
    "webpack-dev-server": "^4.7.4"
  }
  // ...
}
```

### Scripts

In addition to dependencies, your __package.json__ can also include command line
scripts for commands that you frequently use for your project. Suppose you have
the following script:

```json
// package.json

{
  // ...
  "scripts": {
    // ...
    "banana": "echo \"banana banana banana\""
  },
  // ...
}
```

The value of a script is simply a string containing a command you could run in
your terminal. Here, you see `echo "banana banana banana"` is the command (the
quotation marks are escaped). 

The key of a script is just its name. To execute a script, simply run `npm run
<script-name>`. For example:

```sh
$ npm run banana

> test@1.0.0 banana /Users/me/Desktop/test
> echo "banana banana banana"

banana banana banana
```

Scripts are useful for creating shortcuts for commonly run commands. Add the
following `start` script to run your dev server:

```json
"scripts": {
  "start": "webpack serve --mode=development"
}
```

Now you can simply enter `npm run start` instead of `webpack serve
--mode=development`.

> Note: `npm` automatically recognizes scripts named `test`, `start`, `restart`,
> and `stop`. You accordingly do not have to insert `run` when running scripts
> with one of those four names. In other words, `npm start` is equivalent to
> `npm run start`.

#### Version resolution in scripts

First, recall that in Ruby, running `bundle exec some_command` is **not the
same** as running `some_command`. The former runs the `Gemfile`-specified
version of `some_command`, while the latter runs the latest version installed on
your system. Omitting `bundle exec` when running commands can cause frustrating
errors!

Running a command via `npm run` has the same effect as `bundle exec` in this
sense--it will use the local version of executable packages (commands), even if
you have a global install with a later version.

For example, suppose you have version `5.68.0` of `webpack` installed globally,
but you have a __package.json__ that looks like so:

```json
// package.json

{
  // ...
  "scripts": {
    // ...
    "webpack-version": "webpack -v"
  },
  "dependencies": {
    // ...
    "webpack": "^4.46.0",
    "webpack-cli": "^3.3.12",
    // ...
  },
  // ...
}
```

Then if you run `webpack -v`, the global version of `webpack` is used; if you
instead run `webpack -v` via the `webpack-version` script, the local version is
used:

```sh
$ webpack -v
5.68.0

$ npm run webpack-version

> test@1.0.0 webpack-version /Users/me/Desktop/test
> webpack -v

4.46.0
```

#### Life cycle scripts

Finally, there are special script names you can give that will make your script
a *Life Cycle* script. This means your script will get called automatically in
certain contexts. For instance, naming a script `postinstall` will cause it to
execute automatically after you run `npm install`. [Read more in the
docs.][scripts-docs]

### `browserslist`

Tools like Babel reference the `browserslist` property in your __package.json__
to determine which environments to target--i.e., the environments where your
project should be able to run. The `defaults` value is shorthand for `> 0.5%,
last 2 versions, Firefox ESR, not dead`, which is a good go-to. This translates
to: any browser with greater than 0.5% user share, the last 2 versions of any
browser, and Firefox ESR, but excluding from this list any "dead" browsers--i.e,
browsers that haven't been supported for at least 24 months. You can read more
about `browserslist` [here][browserslist].

Add the following to your __package.json__:

```json
// package.json

{
  // ...
  "browserslist": [
    "defaults"
  ],
  // ...
}
```

### `.gitignore`

Before moving on, be aware that `node_modules` can get very large. Since it can
be easily regenerated using `npm install`, there's no reason to include it in
your Git repo. To avoid this, create a `.gitignore` file in your project's root
directory and list the files or directories you want to ignore, one per line.
Use a trailing `/` to indicate a directory to ignore. For example:

```text
node_modules/
main.js
main.js.map
```

You can view a full collection of useful `.gitignore` templates
[here][gitignore].

### Summary

- `npm init --yes`: Initializes an app with NPM by generating a boilerplate
  __package.json__
- `npm install <package_name>`: Installs and lists an NPM package as a dependency
  in a __package.json__
- `npm install`: Downloads all JavaScript dependencies listed in a
  __package.json__
- NPM `scripts` allow you to run common commands using local versions of your
  packages, sometimes automatically
- The `browserslist` option lets you specify the environments in which your
  project is intended to run; it is referenced by tools like Babel
- Don't forget to include a `.gitignore` that ignores your bundle files and
  `node_modules`

## Step 2: Webpack

Now that your project is set up with scripts and dependencies, it's time to
configure your build process so that the React code you write can be
transpiled, bundled, and made ready for the browser. For this, you'll use
Webpack.

### __webpack.config.js__ and __babel.config.json__

Since you are already familiar with Webpack config files, go ahead and create
one with the following boilerplate:

```js
// webpack.config.js

const path = require('path');

module.exports = {
  entry: path.resolve(__dirname, 'src', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  devServer: {
    static: path.resolve(__dirname, 'dist'),
    port: 3000,
    historyApiFallback: true
  },
  devtool: 'source-map',
  module: {
    rules: [
      {
        test: /\.js$/, 
        use: ['babel-loader'],
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
};
```

Note that this particular config is not meant to completely replicate the
functionality of a Create React App setup. Instead, it provides only the basics
you need to get started: it generates a source map, uses Babel to transpile your
JavaScript, and bundles all your imported CSS into a `style` tag.

In addition, the `devServer` key configures your `webpack-dev-server` to run on
`localhost:3000` and to serve assets, such as __index.html__, from your `dist`
folder. By setting `historyApiFallback` to `true`, `webpack-dev-server` will
serve your __index.html__ for every URL path; this means you can use
`BrowserRouter` for your frontend routing. (For further explanation of the need
for `historyApiFallback` with Single Page Applications, see
[here][historyApiFallback].)

> You may be wondering about the value `[name].js` provided to
> `output.filename`. This is most helpful when your project has multiple entry
> files, in which case `entry` will point to an object, where the keys are the
> names of the entry points, and the values are the paths to the entry files.
> For each one, Webpack will output a corresponding bundle file, replacing
> `[name]` with the name of the entry point. If you only specify a single entry
> point, as above, it will have the default name of `main`, and so your bundle
> file will be called __main.js__.

To configure your Babel transpilation, create the following boilerplate
__babel.config.json__:

```json
// babel.config.json

{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage",
        "corejs": "3"
      }
    ]
  ]
}
```

You've already installed `webpack`, `webpack-cli`, and `webpack-dev-server`, but
you'll need to install the packages required for the rest of your Webpack
config. First, install the packages necessary for your Babel transpilation:

```sh
npm install @babel/core @babel/preset-env babel-loader core-js regenerator-runtime
```

Next, install `css-loader` to bundle your imported CSS files and `style-loader`
to put your bundled CSS into a `style` tag (this is faster for development, but
you should consider using [`MiniCssExtractPlugin`] for production):

```sh
npm install css-loader style-loader
```

### Adding React to Webpack

Right now, Babel is using the `env` preset to transpile any cutting edge
JavaScript into code that will run in the browsers specified by `browserslist`.
In addition, you want Babel to transpile your JSX into plain JavaScript. For
that, you'll need to install another preset:

```sh
npm install @babel/preset-react
```

Now modify your __babel.config.json__ to include this new preset:

```json
// babel.config.json

{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage",
        "corejs": "3"
      }
    ],
    "@babel/preset-react"
  ]
}
```

Presets are processed last to first. In other words, Babel will start with
`@babel/preset-react`, transpiling your JSX into plain JavaScript, and then go
to `@babel/preset-env`.

Since the files you want Babel to transform might have a `.jsx` extension, head
back to your __webpack.config.js__ to change the `test` Webpack uses to
determine which files Babel should transform. Add an optional `x` after `.js` in
the regular expression:

```js
{
  test: /\.jsx?$/, // was /\.js$/
  use: ['babel-loader'],
  exclude: /node_modules/
}
```

### Resolving extensions

When you import a file without including an extension: 

```js
import App from './App';
```

Webpack will try to figure out which file you want to import by going through a
list of possible extensions.

The [default list][resolve-extensions] is `['.js', '.json', '.wasm']`. So in the
above example, Webpack would first look for __./App.js__, then __./App.json__,
and then __./App.wasm__. If it still hasn't found a file, it'll throw an error.

You can customize this list by adding an array of extensions at the key of
`extensions` under a top-level key of `resolve`. Go ahead and add the following
to your config file:

```js
// webpack.config.js

module.exports = {
  // ...
  resolve: {
    extensions: ['.jsx', '...']
  }
};
```

Now, Webpack will first look for `.jsx` files when you don't provide an
extension. `...` simply stands for Webpack's default array of extensions.

## Step 3: Write some React!

Time to test that everything works! Go ahead and add a __src/__ folder with
__index.js__, __App.jsx__, and __App.css__ files inside, as well as a __dist/__
folder with __index.html__ inside:

```text
test
├── dist/
│   └── index.html
├── node_modules/
├── src/
│   ├── App.jsx
│   └── index.js
├── babel.config.json
├── package-lock.json
├── package.json
└── webpack.config.js
```

### HTML

Paste the following HTML into __dist/index.html__:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>React from Scratch</title>
  <script src="main.js" defer></script>
</head>

<body>
  <div id="root">React will go here.</div>
</body>

</html>
```

Note that this file includes a `script` tag for loading your `main.js` bundle
file (or virtual file, with `webpack-dev-server`); this tag includes the `defer`
attribute so that your JavaScript runs after the DOM has loaded.

Finally, a `div` with an `id` of `root` is included, within which you will
render your React app.

### JavaScript

Paste the following into __src/index.js__:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

Then, paste the following into __src/App.jsx__:

```jsx
import React from 'react';
import { Route, BrowserRouter, NavLink } from 'react-router-dom';
import './App.css';

export default function App() {
  return (
    <BrowserRouter>
      <div className='app'>
        <header className='site-header'>
          <h1>React from Scratch</h1>
          <nav className='main-nav'>
            <NavLink exact to='/'>
              Home
            </NavLink>
            <NavLink to='/cats'>
              Cats
            </NavLink>
            <NavLink to='/dogs'>
              Dogs
            </NavLink>
          </nav>
        </header>

        <Route exact path='/'>
          <main className='page'>
            <h2>This is the home page</h2>
          </main>
        </Route>

        <Route path='/cats'>
          <Page topic='cats' />
        </Route>

        <Route path='/dogs'>
          <Page topic='dogs' />
        </Route>
      </div >
    </BrowserRouter>
  );
}

function Page({ topic }) {
  return (
    <main className='page'>
      <h2>{topic[0].toUpperCase() + topic.slice(1)}</h2>
      <img src={`https://source.unsplash.com/random?${topic}`} alt={topic} />
    </main>
  );
}
```

### CSS

Finally, add the following CSS to __src/App.css__ (which is imported into __App.jsx__):

```css
/* src/App.css */

.app {
  width: 80%;
  margin: 20px auto;
  font-family: sans-serif;
}

.site-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.main-nav {
  display: flex;
  align-items: center;
}

.main-nav a {
  margin-left: 25px;
  color: black;
  text-decoration: none;
  font-size: 1.2rem;
}

.main-nav a.active, .main-nav a:hover {
  text-decoration: underline;
}

.main-nav a.active {
  font-weight: bold;
}

.page h2 {
  text-align: center;
}

.page img {
  display: block;
  margin: 20px auto;
  width: 50%;
}
```

### Test your setup

Go ahead: run `npm start` and open [localhost:3000]. Congratulations, you've
set up a React app with support for CSS imports, frontend routing, and a
live-reload dev server!

[semver]: https://semver.npmjs.com/
[`webpack-dev-server`]: https://webpack.js.org/configuration/dev-server/
[`MiniCssExtractPlugin`]: https://webpack.js.org/plugins/mini-css-extract-plugin/
[browserslist]: https://github.com/browserslist/browserslist
[scripts-docs]: https://docs.npmjs.com/cli/v8/using-npm/scripts
[resolve-extensions]: https://webpack.js.org/configuration/resolve/#resolveextensions
[gitignore]: https://github.com/github/gitignore
[historyApiFallback]: https://github.com/bripkens/connect-history-api-fallback#Introduction
[localhost:3000]: localhost:3000