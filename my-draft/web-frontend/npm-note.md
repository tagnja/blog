# NPM Note

**Content**

- 1.About npm
  - What is it
    - Pros and Cons
    - Application Scenarios
  - Why do We Need it
  - How does xxx Work
  - How to Use it
- 2.Getting Started
- 3.Packages and Modules
- 4.Integrations
- 5.Orgs
- 6.npm Enterprise
- 7.CLI documentation
- Appendixes
- References

## 1. About npm

### What is it

> npm is a **package manager** for the JavaScript programming language. It is the default package manager for the JavaScript runtime environment Node.js. It consists of a command line client, also called npm, and an [online database](https://en.wikipedia.org/wiki/Online_database) of public and paid-for private packages, called the npm registry. The registry is accessed via the client, and the available packages can be browsed and searched via the npm website. The package manager and the registry are managed by npm, Inc.



Packages (or modules): shared others reusable code.

package.json: metadata of packages.

npm consist of: 

- public registry: a big database of information about packages. Shared codes are stored in public registry. Someone upload codes to registry others download codes from registry and use the code.

- npm client: to publish code up to the registry and install packages from the registry.

- npm website: Use the [*website*](https://npmjs.com/) to discover packages, set up profiles, and manage other aspects of your npm experience.



### Pros and cons

### Application Scenarios

### Why do We Need it

### How does xxx Work

### How to Use it

## 2. Getting Started

Sharing packages

- If you choose to share your packages publicly, there is no cost. To use and share private packages, you need to upgrade your account.

## 3. Packages and Modules

### 3.1 Introduction to packages and modules

#### About packages and modules

**About packages**

A **package** is a file or directory that is described by a `package.json` file. A package must contain a `package.json` file in order to be published to the npm registry. For more information on creating a `package.json` file, see “[Creating a package.json file](https://docs.npmjs.com/creating-a-package-json-file)”.

Packages can be unscoped or scoped to a user or Org, and scoped packages can be private or public.

**About modules**

A **module** is any file or directory in the `node_modules` directory that can be loaded by the Node.js `require()` function.

To be loaded by the Node.js `require()` function, a module must be one of the following:

- A folder with a `package.json` file containing a `"main"` field.
- A folder with an `index.js` file in it.
- A JavaScript file.

> **Note:** Since modules are not required to have a `package.json` file, not all modules are packages. Only modules that have a `package.json` file are also packages.

In the context of a Node program, the `module` is also the thing that was loaded *from* a file. For example, in the following program:

```
var req = require('request')
```

we might say that “The variable `req` refers to the `request` module”.

#### About scopes

> **Note:** You must be using npm version 2 or greater to use scopes. To upgrade to the latest version of npm, on the command line, run `npm install npm@latest -g`

When you sign up for an npm user account or create an Org, you are granted a scope that matches your user or Org name. You can use this scope as a namespace for related packages.

A scope allows you to create a package with the same name as a package created by another user or Org without conflict.

When listed as a dependent in a `package.json` file, scoped packages are preceded by their scope name. The scope name is everything between the `@` and the slash:

- “npm” scope:

  ```
  @npm/package-name
  ```

- “npmcorp” scope:

  ```
  @npmcorp/package-name
  ```

Scopes and package visibility

- Unscoped packages are always public.
- [Private packages](https://docs.npmjs.com/about-private-packages) are always scoped.
- Scoped packages are private by default; you must pass a command-line flag when publishing to make them public.



### 3.2 Contributing package to the registry

#### Creating a package.json file

You can add a `package.json` file to your package to make it easy for others to manage and install. Packages published to the registry must contain a `package.json` file.

A `package.json` file:

- lists the packages your project depends on
- specifies versions of a package that your project can use using [semantic versioning rules](https://docs.npmjs.com/about-semantic-versioning)
- makes your build reproducible, and therefore easier to share with other developers

**package.json fields**

Required name and version fields

A `package.json` file must contain `"name"` and `"version"` fields.

- The `"name"` field contains your package’s name, and must be lowercase and one word, and may contain hyphens and underscores.

- The `"version"` field must be in the form `x.x.x` and follow the [semantic versioning guidelines](https://docs.npmjs.com/about-semantic-versioning).

Example

```
{
  "name": "my-awesome-package",
  "version": "1.0.0",
  ...
}
```

**Creating a new package.json file**

You can create a `package.json` file by running a CLI questionnaire or creating a default `package.json` file.

Running a CLI questionnaire: 

```
cd /path/to/package
npm init
```

Creating a default package.json file

```
cd /path/to/package
npm init --yes
```

Default values:

- `name`: the current directory name
- `version`: always `1.0.0`
- `description`: info from the README, or an empty string `""`
- `main`: always `index.js`
- `scripts`: by default creates an empty `test` script
- `keywords`: empty
- `author`: empty
- `license`: [`ISC`](https://opensource.org/licenses/ISC)
- `bugs`: information from the current directory, if present
- `homepage`: information from the current directory, if present

Setting config options for init command

You can set default config options for the init command. For example, to set the default author email, author name, and license, on the command line, run the following commands:

```
> npm set init.author.email "example-user@example.com"
> npm set init.author.name "example_user"
> npm set init.license "MIT"
```

#### Creating Node.js modules

TODO

#### Specifying dependencies and devDependencies in a package.json file

To specify the packages your project depends on, you must list them as `"dependencies"` or `"devDependencies"` in your package’s [`package.json`](https://docs.npmjs.com/creating-a-packge-json-file) file. When you (or another user) run `npm install`, npm will download dependencies and devDependencies that are listed in `package.json` that meet the [semantic version](https://docs.npmjs.com/about-semantic-versioning) requirements listed for each. To see which versions of a package will be installed, use the [semver calculator](https://semver.npmjs.com/).

- `"dependencies"`: Packages required by your application in production.
- `"devDependencies"`: Packages that are only needed for local development and testing.

**Adding dependencies to a package.json file**

You can add dependencies to a `package.json` file from the command line or by manually editing the `package.json` file.

**Adding dependencies to a package.json file from the command line**

To add dependencies and devDependencies to a `package.json` file from the command line, you can install them in the root directory of your package using the `--save-prod` flag for dependencies (the default behavior of `npm install`) or the `--save-dev` flag for devDependencies.

To add an entry to the `"dependencies"` attribute of a `package.json` file, on the command line, run the following command:

```
npm install <package-name> [--save-prod]
```

To add an entry to the `"devDependencies"` attribute of a `package.json` file, on the command line, run the following command:

```
npm install <package-name> --save-dev
```

#### About semantic versioning

To keep the JavaScript ecosystem healthy, reliable, and secure, every time you make significant updates to an npm package you own, we recommend publishing a new version of the package with an updated version number in the [`package.json` file](https://docs.npmjs.com/creating-a-package-json-file) that follows the [semantic versioning spec](http://semver.org/).

> **Note:** If you introduce a change that breaks a package dependency, we strongly recommend incrementing the version **major number**; see below for details.

**Using semantic versioning to specify update types your package can accept**

You can specify which update types your package can accept from dependencies in your package’s `package.json` file.

For example, to specify acceptable version ranges up to 1.0.4, use the following syntax:

- Patch releases: `1.0` or `1.0.x` or `~1.0.4`
- Minor releases: `1` or `1.x` or `^1.0.4`
- Major releases: `*` or `x`

```
"dependencies": {
  "my_dep": "^1.0.0",
  "another_dep": "~2.2.0"
},
```




### 3.3 Updating and managing your publish packages

### 3.4 Getting packages from the registry

#### Searching for and choosing packages to download

- Search in npmjs.com
- Package search rank criteria
  - Popularity
  - Quality
  - Maintenance
  - Optimal. Optimal combines the other three criteria (popularity, quality, maintenance) into one score in a meaningful way.

#### Downloading and installing packages locally

To install a public package, on the command line, run

```
npm install <package_name>
```

This will create the `node_modules` directory in your current directory (if one doesn’t exist yet) and will download the package to that directory.

> **Note:** If there is no `package.json` file in the local directory, the latest version of the package is installed. If there is a `package.json` file, npm installs the latest version that satisfies the [semver rule](https://docs.npmjs.com/about-semantic-versioning) declared in `package.json`.

#### Downloading and installing packages globally

> **Tip:** If you are using npm 5.2 or higher, we recommend using `npx` to run packages globally.

[Installing](https://docs.npmjs.com/cli/install) a package globally allows you to use the code in the package as a set of tools on your local computer.

To download and install packages globally, on the command line, run the following command:

```
npm install -g <package_name>
```

#### Updating packages downloaded from the registry

Updating local packages

```
# update package
npm update
# to test the update
npm outdated
```

Updating globally-installed packages

```
npm outdated -g --depth=0
npm update -g <package_name>
npm update -g
```



#### Using npm packages in your projects

If you are creating a Node.js module, you can use a package in your module by passing it as an argument to the `require` function.

For example, to use the lodash package in a Node.js module, in the root directory of the module, create a file named `index.js` with the following contents:

```
// index.js
var lodash = require('lodash');

var output = lodash.without([1, 2, 3], 1);
console.log(output);
```

Run the code using `node index.js`. It should output `[2, 3]`.

**Resolving “Cannot find module” errors**

If you have not properly installed a package, you will receive an error when you try to use it in your code. For example, if you reference the `lodash` package without installing it, you would see the following error:

```
module.js:340
    throw err;
          ^
Error: Cannot find module 'lodash'
```

To resolve “cannot find module” errors, run the appropriate `install` command in the same directory as your project’s `index.js` file:

- For scoped packages, run `npm install <@scope/package_name>`
- For unscoped packages, run `npm install <package_name>`



#### Uninstalling packages and dependencies

If you no longer need to use a package in your code, we recommend uninstalling it and removing it from your project’s dependencies.

**Removing a local package from your node_modules directory**

```
npm uninstall <package_name>
npm uninstall <@scope/package_name>
```

for example

```
npm uninstall lodash
```

**Removing a local package from the package.json dependencies**

```
npm uninstall --save <package_name>
npm uninstall --save <@scope/package_name>
```

for example

```
npm uninstall --save lodash
```

> **Note:** If you installed a package as a "devDependency" (i.e. with `--save-dev`), use `--save-dev` to uninstall it: `npm uninstall --save-dev package_name`

**Uninstalling global packages**

```
npm uninstall -g <package_name>
npm uninstall -g <@scope/package_name>
```

For example

```
npm uninstall -g jshint
```



### 3.5 Securing your code

TODO



## 4. Integrations

TODO

## 5. Orgs

TODO

## 6. npm Enterprise

TODO



## 7. CLI documentation



## Appendixes

### npm commands

``` 
# display npm version
npm -v

# init
npm init
npm init --yes

# login npm registry
npm login

# display Non-global installed packages
npm ls (or npm list)
# to display only main packages not its sub-packages
npm ls --depth=0
# global libraries are installed and where they're located
npm ls -g
# showing just the path of global libraries
npm ls -g | head -1

# display installation directory of your npm packages
npm root
npm root -g

# show outdated packages
npm outdated
npm outdated -g --depth=0

# update packages
npm update
npm update -g <package_name>
npm update -g

# installs dependencies into the node_modules/ directory
# install all package in package.json
npm install
npm install <package_name>
npm intsall <package_name> -g
npm install <package-name> --save-dev
npm install <package-name> --save-prod

npm uninstall <package_name>
npm uninstall <@scope/package_name>
npm uninstall --save <package_name>
npm uninstall --save <@scope/package_name>
npm uninstall -g <package_name>
npm uninstall -g <@scope/package_name>

# runs an arbitrary command from a package’s "scripts" object.
npm run <script_name> or npm run-script <script_name>
# runs the build field from the package.json scripts field. Publish directory /dist
npm run build
```

npm config

```
# set taobao mirror
npm config set registry https://registry.npm.taobao.org
npm config set registry https://registry.npmjs.org/
# verify
npm config get registry
```



## References

[1] [npm - Wikipedia](https://en.wikipedia.org/wiki/Npm_(software))

[2] [npm Documentation](https://docs.npmjs.com/)

[3] [npm CLI Documentation](https://docs.npmjs.com/cli-documentation/)