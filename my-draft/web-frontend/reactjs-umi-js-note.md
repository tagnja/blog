# UmiJS Note

**content**

- Getting Started
- Umi Basic
  - Directory Structure
  - Config
  - Runtime Config
  - Routing
  - Convention Routing
  - Plugin
  - Navigate Between Pages
  - HTML Template
  - Mock Data
  - Env Variables
  - Cli Commands
- Styles and Assets
- Umi Advanced



## Getting Started

### Environment setup

First you need to install [node](https://nodejs.org/en/). Make sure the node version is 10.13 or above.

We recommend to use [yarn](https://github.com/yarnpkg/yarn) to manage npm dependencies.

```bash
# install yarn globally
$ npm i yarn -g
# confirm yarn version
$ yarn -v
```

### For Users in China

We recommend to use [tyarn](https://github.com/yiminghe/tyarn) to improve performance:

```bash
# install tyarn globally
$ npm i yarn tyarn -g
# confirm tyarn version
$ tyarn -v

# install yarn and @ali/yarn globally
$ tnpm i yarn @ali/yarn -g
# confirm ali yarn version
$ ayarn -v
```

Or set yarn registry

```
# set registry globally
yarn config set registry https://registry.npm.taobao.org/
# show registry
yarn config get registry
```

###  Scaffold

Create an empty directory for your app

```bash
$ mkdir myapp && cd myapp
```

Create project using `@umijs/umi-app` as the template

```
$ yarn create @umijs/umi-app // if use yarn
$ npx @umijs/create-umi-app // if use npm
```

### Install dependencies

```
$ yarn
```

### Startup project

```
$ yarn start
```

Open http://localhost:8000/ in your browser, you can see the following interface

### Change setting

The default scaffolding has built-in `@umijs/preset-react`, including common functions such as layout, permissions, internationalization, dva, and simple data flow. For example you want ant-design-pro layout, editing `.umirc`.ts configuration layout: `{}`

```diff
import { defineConfig } from 'umi';

export default defineConfig({
+ layout: {},
  routes: [
    { path: '/', component: '@/pages/index' },
  ],
});
```

No need to restart `yarn start`, webpack will compile incrementally behind the scenes, you will see the following interface after a while.

### Deployment release

#### Build

```bash
$ yarn build
```

Build is generated by default in `./dist`. We can the distribution as a tree

```bash
tree ./dist

./dist
├── index.html
├── umi.css
└── umi.js
```

#### Local authentication

Before publishing, you can `serve` do local authentication

```bash
$ yarn global add serve
$ serve ./dist
```

Access [http://localhost:5000](http://localhost:5000/) and under normal circumstances `yarn start` should be executed

### Deploy

After local verification, you can deploy. You will need to deploy the `dist` directory to the server.



## Umi Basic

### Directory structure

A fundamental structure as below:

```unknown
.
├── package.json
├── .umirc.ts
├── .env
├── dist
├── mock
├── public
└── src
    ├── .umi
    ├── layouts/index.tsx
    ├── pages
        ├── index.less
        └── index.tsx
    └── app.ts
```

- `.umirc.ts`
  - Configuration file, consist of configurations for builtin/plugin provided functions.

- `.env`
  - Environments

  - For example：

    ```
    PORT=8888
    COMPRESS=none
    ```

- `dist`
  - Default location of outpput of `umi build`

- `mock`
  - All `.js`、`.ts` files will be registered as `mock`.

- `public`
  - All files in this directory will be copied to `dist`.

- `src/.umi`
  - Auto-generated directory, such as entry point, routes..., will be generated here. **Do not commit `.umi` to your `git` repo, since they will be re-generated as `umi dev` or `umi build` running.**

- `src/layouts/index.tsx`
  - Layout module for convention routing pattern.

- `src/pages`
  - All router entries here.
- `src/app.ts`
  - Runtime configuration module, provide expansibility at runtime. Such as router, render update.
- `config/config.ts`
  - Configuration file. 



### Config

Configuring with es6 syntax is supported in `.umirc.ts` or `config/config.ts`. 

UmiJS source code: `umi/packages/core/src/Config/Config.ts`

```
const CONFIG_FILES = [
  '.umirc.ts',
  '.umirc.js',
  'config/config.ts',
  'config/config.js',
];
```

A common configuration looks as below:

```bash
export default {
  base: '/docs/',
  publicPath: '/static/',
  hash: true,
  history: {
    type: 'hash',
  },
}
```

#### Configuration

If your configurations are not complicated, we recommend configuring your app with `.umirc.ts`; If your configurations are complicated, it is possible to split your configurations into pieces and organize them in `config/config.ts`. For example, if you need to configure routers, it would be a good choice to have it in an individual module called `config/routes.ts` alongside `config/config.ts`.

You have to choose between `.umirc.ts` and `config/config.ts`, `.umirc.ts` has higher priority.

#### local configuration

Create `.umirc.local.ts`, it will be deep merged with `.umirc.ts` while using `umi dev`.

> Note: `.umirc.local.ts` is only available with `umi dev`. It won't work with `umi build` .

For example:

```js
// .umirc.ts or config/config.ts
export default { a: 1, b: 2 };

// .umirc.local.ts or config/config.local.ts
export default { c: 'local' };
```

The final configuration shall be:

```js
{
  a: 1,
  b: 2,
  c: 'local',
}
```

Notice：

- local configuration file of `config/config.ts` is `config/config.local.ts`
- `.local.ts` should only be used for local debugging，**Don't commit it to git repo**
- `.local.ts` has higher priority compare with others specified with `UMI_ENV`

#### Configuration for multiple environments

Environment variable `UMI_ENV` shall be used to identify configurations for different environemtns.

For example:

```js
// .umirc.js or config/config.js
export default { a: 1, b: 2 };

// .umirc.cloud.js or config/config.cloud.js
export default { b: 'cloud', c: 'cloud' };

// .umirc.local.js or config/config.local.js
export default { c: 'local' };
```

If we don't specify `UMI_ENV` , the final configuration we get will be:

```js
{
  a: 1,
  b: 2,
  c: 'local',
}
```

If `UMI_ENV=cloud` is specified, the final configuration we get will be:

```js
{
  a: 1,
  b: 'cloud',
  c: 'local',
}
```



### Runtime Config

The difference between runtime-config and config is execution timing, runtime-config is evaluated while your app running in browser. Therefor, `function`, `jsx`, `import` and other **browser-runnable** snippets can be used.

TODO

...



### Routing

In Umi, applications are [single-page](https://en.wikipedia.org/wiki/Single-page_application)) applications, and the page address jumps are done on the browser side, and the server will not be re-requested for html, and html is only loaded once when the application is initialized. All pages are composed of different components. The switching of pages is actually the switching of different components. You only need to associate different routing paths with corresponding components in the configuration.

Routing configuration is setting in UmiJS configuration file `.umirc.ts` or `config/config.ts`, the `.umirc.ts` has higher priority.

if you need to configure routers, it would be a good choice to have it in an individual module called `config/routes.ts` alongside `config/config.ts`.

#### Configuring Routing

Configure through `routes` in the configuration file, the format is an array of routing information.

Example：

```js
export default {
  ...
  routes: [
    { exact: true, path: '/', component: 'index' },
    { exact: true, path: '/user', component: 'user' },
  ],
  ...
}
```

- `path`: URI path.

- `component`: Configure the React component path for rendering after matching location and path. It can be an absolute path or a relative path. If it is a relative path, it will be found from `src/pages`. If you point to a file in the `src` directory, you can use either `@` or `../`. For example, `component:'@/layouts/basic'`, or `component:'../layouts/basic'`, the former is recommended.

- `exact`: Indicates whether it is a strict match, that is, whether location and path exactly correspond.

  ```
  export default {
    routes: [
      // Matching fails when url is /one/two
      { path: '/one', exact: true },
      
      // Matching is successful when url is /one/two
      { path: '/one' },
      { path: '/one', exact: false },
    ],
  }
  ```

- `routes`: Configure sub-routes, usually used when you need to add layout components to multiple paths. 

  ```
  export default {
    routes: [
      { 
      	path: '/login', 
      	component: 'login' 
      },
      {
        path: '/',
        component: '@/layouts/index',
        routes: [
          { 
          	path: '/list', 
          	component: 'list' 
          },
          { 
          	path: '/admin', 
          	component: 'admin' 
          },
        ],
      }, 
    ],
  }
  ```

- `redirect`: Configure routing jump.

  ```react
  export default {
    routes: [
    	// Visiting / will jump to /list, which will be rendered by the src/pages/list file.
      { exact: true, path: '/', redirect: '/list' },
      { exact: true, path: '/list', component: 'list' },
    ],
  }
  ```

- `wrappers`: wrappers is HOC. For example, you can run authorization check for a specific route:

  ```
  export default {
    routes: [
      { path: '/user', component: 'user',
        wrappers: [
          '@/wrappers/auth',
        ],
      },
      { path: '/login', component: 'login' },
    ]
  }
  ```

  ```
  // src/wrappers/auth
  import { Redirect } from 'umi'
  export default (props) => {
    const { isLogin } = useAuth();
    if (isLogin) {
      return <div>{ props.children }</div>;
    } else {
      return <Redirect to="/login" />;
    }
  }
  ```

  With above configuration, user request of `/user` will be validated via `useAuth`. `src/pages/user` gets rendered or page redirected to `/login`.

- `title`: Configure the route title.

#### Page jump

TODO

#### hash routing

TODO

#### Link component

Example：

```jsx
import { Link } from 'umi';

export default () => (
  <div>
    <Link to="/users">Users Page</Link>
  </div>
);
```

Note: `Link` is only used for internal jumps of single page applications, if it is an external address jump, please use the `<a>` tag

#### Routing component parameters

The routing component can get the following properties through `props`.

```js
export default function(props) {
  console.log(props.route);
  return <div>Home Page</div>;
}
```

#### Passing parameters to sub-routes

TODO

### Convention Routing

In addition to manually defined routing, Umi supports convention-based routing. This is also called file routing. It does not require manually configuring routes. Instead, the file system defines the routing. The routing configuration is derived from directories, files and their naming.

**If there is no manual routing configuration file, Umi will fall back to the conventional routing mode**, analyzing the `src/pages` directory to discover the routing configuration.

Consider the following file structure:

```bash
.
  └── pages
    ├── index.tsx
    └── users.tsx
```

You will get the following routing configuration,

```js
[
  { exact: true, path: '/', component: '@/pages/index' },
  { exact: true, path: '/users', component: '@/pages/users' },
]
```

It should be noted that files that follow any of the following patterns will not be registered as routes:

- Files or directories beginning with `.` or `_`
- Type definition files ending in `d.ts`
- Test files ending with `test.ts`, `spec.ts`, `e2e.ts` (applicable to `.js`, `.jsx` and `.tsx` files)
- `components` and `component` directories
- `utils` and `util` directories
- Files not ending in `.js`, `.jsx`, `.ts` or `.tsx`
- Files not containing JSX elements

#### Dynamic routing

Per convention, file path components enclosed in `[]` will be dynamically routed.

In a more complete example, given the following file structure:

```bash
.
  └── pages
    └── [post]
      ├── index.tsx
      └── comments.tsx
    └── users
      └── [id].tsx
    └── index.tsx
```

convention routing will produce the following routing configuration:

```js
[
  { exact: true, path: '/', component: '@/pages/index' },
  { exact: true, path: '/users/:id', component: '@/pages/users/[id]' },
  { exact: true, path: '/:post/', component: '@/pages/[post]/index' },
  {
    exact: true,
    path: '/:post/comments',
    component: '@/pages/[post]/comments',
  },
];
```

#### 404 routing

Umi will take the contents of `src/pages/404.tsx` for the 404 page. It is expected to return React components.

#### Routing permissions

Routes can be protected by specifying high-level component wrappers.

For example, `src/pages/user` can define a `wrappers` property.

With the above configuration, user request to `/user` will be validated using the `useAuth` function. If successful, `src/pages/user` gets rendered, otherwise, the user will be redirected to `/login`.

#### Extended routing attributes

Umi supports extending routing at the code level by exporting static attributes.

```js
function HomePage() {
  return <h1>Home Page</h1>;
}

HomePage.title = 'Home Page';

export default HomePage;
```

The `title` will be appended to the routing configuration.



### Plugin

View Plugins

```
# view UmiJS plugins
umi plugin list

# view UmiJS plugin used keys
umi plugin list --key
```



### Navigate Between Pages

In umi, there are two ways to navigate between pages: declarative and imperative.

#### Declarative

it is usually used as a React component.

```bash
import { Link } from 'umi';

export default () => (
   <Link to="/list">Go to list page</Link>
);
```

#### Command

Based on `umi/router`, it is usually called in event processing.

```js
import { history } from 'umi';

function goToListPage() {
  history.push('/list');
}
```

You can also get the history directly from the component's properties

```tsx
export default (props) => (
  <Button onClick={()=>props.history.push('/list');}>Go to list page</Button>
);
```



### HTML Template

TODO

### Mock Data

TODO

### Env Variables

TODO

### Cli Commands

umi build

umi dev

umi generate

 umi plugin

 umi help

umi version

umi webpack

## Styles and Assets



## Umi Advanced