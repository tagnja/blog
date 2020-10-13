# Yarn Note

```
# install package use specified registry
yarn save <package> --registry https://registry.npm.taobao.org/

# set registry globally
yarn config set registry https://registry.npm.taobao.org/
yarn config set registry https://registry.yarnpkg.com

# show registry
yarn config get registry
```

For Users in China

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