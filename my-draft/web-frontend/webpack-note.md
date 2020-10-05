# Webpack Note

Content

- Getting Started
- Asset Management
- Output Management
- Development
- Code Splitting
- Caching
- Authoring Libraries
- Environment Variables
- Build Performance
- Content Security Policies
- Development - Vagrant
- Dependency Management
- Scaffolding
- Hot Module Replacement
- Tree Shaking
- Production
- Lazy Loading
- Shimming
- Public Path
- Integrations
- Asset Modules



## Getting Started

### Installing Webpack

Installing webpack locally (in the same of `package.json` directory)

```
npm install --save-dev webpack
# or specific version
npm install --save-dev webpack@<version>
```

If you're using webpack v4 or later, you'll also need to install the [CLI](https://webpack.js.org/api/cli/).

```bash
npm install --save-dev webpack-cli
```

Installing locally is what we recommend for most projects. This makes it easier to upgrade projects individually when breaking changes are introduced. 

Typically webpack is run via one or more [npm scripts](https://docs.npmjs.com/misc/scripts) which will look for a webpack installation in your local `node_modules` directory:

```json
"scripts": {
	"build": "webpack --config webpack.config.js"
}
```

> To run the local installation of webpack you can access its binary version as `node_modules/.bin/webpack`. Alternatively, if you are using npm v5.2.0 or greater, you can run 'npx webpack' to do it.

Global Installation

The following NPM installation will make `webpack` available globally:

```bash
npm install --global webpack
```

> Note that this is **not a recommended practice**. Installing globally locks you down to a specific version of webpack and could fail in projects that use a different version.

### Run the webpack build

Run build

```
npx webpack
```

As of version 4, webpack doesn't require any configuration, but most projects will need a more complex setup, which is why webpack supports a [configuration file](https://webpack.js.org/concepts/configuration).

webpack.config.js

```
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

Run bulid

```
npx webpack --config webpack.config.js
```

### NPM Scripts

Given it's not particularly fun to run a local copy of webpack from the CLI, we can set up a little shortcut. Let's adjust our *package.json* by adding an [npm script](https://docs.npmjs.com/misc/scripts):

```
"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"build": "webpack"
},
```

Now the `npm run build` command can be used in place of the `npx webpack` command we used earlier. 

```
npm run build
#or
npm run build -- --colors
```

## Asset Management

### Loading CSS

In order to `import` a CSS file from within a JavaScript module, you need to install and add the [style-loader](https://webpack.js.org/loaders/style-loader) and [css-loader](https://webpack.js.org/loaders/css-loader) to your [`module` configuration](https://webpack.js.org/configuration/module):

```bash
npm install --save-dev style-loader css-loader
```

**webpack.config.js**

```diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
    module: {
     rules: [
       {
         test: /\.css$/,
         use: [
           'style-loader',
           'css-loader',
         ],
       },
     ],
   },
  };
```



### Loading Images

So now we're pulling in our CSS, but what about our images like backgrounds and icons? Using the [file-loader](https://webpack.js.org/loaders/file-loader) we can easily incorporate those in our system as well:

```bash
npm install --save-dev file-loader
```

**webpack.config.js**

```diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ],
        },
+       {
+         test: /\.(png|svg|jpg|gif)$/,
+         use: [
+           'file-loader',
+         ],
+       },
      ],
    },
  };
```

### Loading Fonts

**webpack.config.js**

```diff
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ],
        },
        {
          test: /\.(png|svg|jpg|gif)$/,
          use: [
            'file-loader',
          ],
        },
+       {
+         test: /\.(woff|woff2|eot|ttf|otf)$/,
+         use: [
+           'file-loader',
+         ],
+       },
      ],
    },
  };
```

### Loading Data

Another useful asset that can be loaded is data, like JSON files, CSVs, TSVs, and XML. Support for JSON is actually built-in, similar to NodeJS, meaning `import Data from './data.json'` will work by default. To import CSVs, TSVs, and XML you could use the [csv-loader](https://github.com/theplatapi/csv-loader) and [xml-loader](https://github.com/gisikw/xml-loader). Let's handle loading all three:

```bash
npm install --save-dev csv-loader xml-loader
```

webpack.config.js

```
    module: {
      rules: [
      {
         test: /\.(csv|tsv)$/,
         use: [
           'csv-loader',
         ],
       },
       {
         test: /\.xml$/,
         use: [
           'xml-loader',
         ],
       },
```

### Global Assets

The coolest part of everything mentioned above, is that loading assets this way allows you to group modules and assets in a more intuitive way. Instead of relying on a global `/assets` directory that contains everything, you can group assets with the code that uses them. For example, a structure like this can be useful:

```diff
- |- /assets
+ |– /components
+ |  |– /my-component
+ |  |  |– index.jsx
+ |  |  |– index.css
+ |  |  |– icon.svg
+ |  |  |– img.png
```

## References

[1] [webpack concepts](https://webpack.js.org/concepts/)

[2] [webpack guides](https://webpack.js.org/guides/)