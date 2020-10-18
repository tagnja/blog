# Yarn Note

Basic

```
# init
yarn init

# add dependencies
yarn add <package>
yarn add [package]@[version]
yarn global add <package>

# remove dependencies
yarn remove <package>
yarn global remove <package>

# outdated
yarn outdated
yarn upgrade

# list all dependencies
yarn list
yarn list --depth=0

# install all dependencies
yarn install

# run
yarn run
```

Registry

```
# install package use specified registry
yarn save <package> --registry https://registry.npm.taobao.org/

# set registry globally
yarn config set registry https://registry.npm.taobao.org/
yarn config set registry https://registry.yarnpkg.com

# show registry
yarn config get registry
```

Cache

```
# set cache path, or setting in ${user}/.yarnrc file
yarn config set cache-folder <cache_path>
yarn config set cache-folder C:\Users\Taogen\AppData\Local\Yarn\Cache
yarn config set cache-folder D:\$UserData\AppData\Local\Yarn\Cache

# list cache location
# default on windows: C:\Users\Taogen\AppData\Local\Yarn\Cache\v6
yarn cache dir
yarn cache ls

# clean cache
yarn cache clean
```



Command CLI For Users in China

```
# install tyarn globally
$ npm i yarn tyarn -g
# confirm tyarn version
$ tyarn -v

# install yarn and @ali/yarn globally
$ tnpm i yarn @ali/yarn -g
# confirm ali yarn version
$ ayarn -v
```

## References

[Yarn Getting Started](https://classic.yarnpkg.com/en/docs/getting-started)

[Yarn CLI](https://classic.yarnpkg.com/en/docs/cli/)