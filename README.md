# 来聊一聊神秘的`monorepo`

## 1. monorepo管理

- `Monorepo` 是管理项目代码的一个方式，指在一个项目仓库(`repo`)中管理多个依赖/模块/包(package)
- `Monorepo` 最主要的好处是统一的工作流和代码共享
- 对于很多用 `Monorepo` 的公司，他们的 `Git` 仓库中不止有自己的代码，还包括了很多的依赖
- `Monorepo` 的核心观点是所有的项目在一个代码仓库中。这并不是说代码没有组织都放在 `./src` 文件夹里面
- [Lerna](https://github.com/lerna/lerna)是一个管理多个 `npm` 模块的工具,优化维护多包的工作流，解决多个包互相依赖，且发布需要手动维护多个包的问题
- [yarn](https://classic.yarnpkg.com/en/docs/cli/)

### 1.1 MultiRepo

#### 1.1.1 优点

- 各模块的管理自由度较高，可以自行选择构建工具、依赖管理、单元测试等配套设施
- 各模块的体积也不会太大

#### 1.1.2 缺点

- 仓库分散不好找，分支管理混乱
- 版本更新繁琐，如果公共模块发生了变化，需要对所有的模块进行依赖更新
- `CHANGELOG`不好梳理，无法自动关联各个模块的变动

### 1.2 MonoRepo

#### 1.2.1 优点

- 一个仓库维护多个模块，方便好找
- 方便版本管理和依赖管理，模块之间的引用调试都比较方便
- 方便统一生成`CHANGELOG`

#### 1.2.2 缺点

- 统一构建工具, 需要构建工具能构建所有的模块
- 仓库体积变大

### 1.3 使用lerna

#### 1.3.1 安装lerna

```js
npm i lerna -g
```

#### 1.3.2 初始化项目

```js
mkdir lerna-project
cd lerna-project

lerna init
lerna notice cli v4.0.0
lerna info Initializing Git repository
lerna info Creating package.json
lerna info Creating lerna.json
lerna info Creating packages directory
lerna success Initialized Lerna files
```

![lerna-init](https://img.zhufengpeixun.com/1609568698164)

#### 1.3.3 package.json

package.json

```json
{
  "name": "root",
  "private": true, // 表示私有的,用来管理整个项目,不会被发布到npm
  "devDependencies": {
    "lerna": "^4.0.0"
  }
}
```

#### 1.3.4 lerna.json

lerna.json

```json
{
  "packages": [
    "packages/*"
  ],
  "version": "0.0.0"
}
```

### 1.4 yarn workspace

- `yarn workspace`允许我们使用 `monorepo` 的形式来管理项目
- 在安装 `node_modules` 的时候它不会安装到每个子项目的 `node_modules` 里面，而是直接安装到根目录下面，这样每个子项目都可以读取到根目录的 `node_modules`
- 整个项目只有根目录下面会有一份 `yarn.lock` 文件。子项目也会被 `link` 到 `node_modules` 里面，这样就允许我们就可以直接用 `import` 来导入对应的项目
- `yarn.lock`文件是自动生成的,也完全由`Yarn`来处理，`yarn.lock`锁定你安装的每个依赖项的版本，这样就可以确保不会意外获得不良的依赖包

#### 1.4.1 开启workspace

package.json

```diff
{
  "name": "root",
  "private": true, 
+  "workspaces": [
+    "packages/*"
+  ],
  "devDependencies": {
    "lerna": "^3.22.1"
  }
}
```

#### 1.3.2 创建子项目

- react是React核心，包含了`React.createElement`等代码
- shared 存放各个模块公用的全局变量和方法
- scheduler 实现了优先级调度功能
- react-reconciler 提供了协调器的功能
- react-dom 提供了渲染到DOM的功能

```js
lerna create react
lerna create shared
lerna create scheduler
lerna create react-reconciler
lerna create react-dom
```

```sh
lerna notice cli v4.0.0
lerna WARN ENOREMOTE No git remote found, skipping repository property
package name: (react) react
version: (0.0.0) 
description: react
keywords: react
homepage: 
license: (ISC) 
entry point: (lib/react.js) 
git repository: https://github.com/facebook/react
About to write to /code/workspace/lerna-project/packages/react/package.json:

{
  "name": "react",
  "version": "0.0.0",
  "description": "react",
  "keywords": [
    "react"
  ],
  "author": "1204788939@qq.com",
  "homepage": "https://github.com/facebook/react#readme",
  "license": "ISC",
  "main": "lib/react.js",
  "directories": {
    "lib": "lib",
    "test": "__tests__"
  },
  "files": [
    "lib"
  ],
  "publishConfig": {
    "registry": "https://registry.npm.taobao.org/"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/facebook/react.git"
  },
  "scripts": {
    "test": "echo \"Error: run tests from root\" && exit 1"
  },
  "bugs": {
    "url": "https://github.com/facebook/react/issues"
  }
}


Is this OK? (yes) yes
lerna success create New package react created at ./packages/react
```



#### 1.3.3 添加依赖

- [yarnpkg](https://classic.yarnpkg.com/en/docs/cli)
- [lerna](https://github.com/lerna/lerna#readme)

##### 1.3.3.1 设置加速镜像

```js
yarn config get registry
yarn config set registry http://registry.npm.taobao.org/
yarn config set registry http://registry.npmjs.org/
```

#### 1.3.4 常用命令

##### 1.3.4.1 根空间添加依赖

```js
yarn add chalk --ignore-workspace-root-check
```

```shell
yarn add v1.22.10
info No lockfile found.
[1/4] 🔍  Resolving packages...
warning lerna > @lerna/add > pacote > @npmcli/run-script > node-gyp > request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
warning lerna > @lerna/bootstrap > @lerna/run-lifecycle > npm-lifecycle > node-gyp > request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
warning lerna > @lerna/add > pacote > @npmcli/run-script > node-gyp > request > har-validator@5.1.5: this library is no longer supported
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
success Saved 379 new dependencies.
info Direct dependencies
└─ chalk@4.1.0
info All dependencies
├─ @babel/code-frame@7.12.13
├─ @babel/helper-validator-identifier@7.12.11
...
```

##### 1.3.4.2 给某个项目添加依赖

```js
yarn workspace react add object-assign
```

```shell
yarn workspace v1.22.10
yarn add v1.22.10
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
warning Pattern ["object-assign@^4.1.1"] is trying to unpack in the same destination "/Users/zhangyaohuang/Library/Caches/Yarn/v6/npm-object-assign-4.1.1-2109adc7965887cfc05cbbd442cac8bfbb360863-integrity/node_modules/object-assign" as pattern ["object-assign@^4.1.0","object-assign@^4.0.1"]. This could result in non-deterministic behavior, skipping.
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
info All dependencies
└─ object-assign@4.1.1
✨  Done in 1.76s.
✨  Done in 2.07s.
```

##### 1.3.4.3 安装和link

```js
yarn install
lerna bootstrap --npm-client yarn --use-workspaces
```

```bash
yarn install v1.22.10
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...
✨  Done in 1.48s.
info cli using local version of lerna
lerna notice cli v4.0.0
lerna info bootstrap root only
yarn install v1.22.10
[1/4] 🔍  Resolving packages...
success Already up-to-date.
✨  Done in 0.33s.
```

##### 1.3.4.4 其它命令

| 作用                        | 命令                                       |
| :-------------------------- | :----------------------------------------- |
| 查看工作空间信息            | yarn workspaces info                       |
| 删除所有的 node_modules     | lerna clean 等于 yarn workspaces run clean |
| 重新获取所有的 node_modules | yarn install --force                       |
| 查看缓存目录                | yarn cache dir                             |
| 清除本地缓存                | yarn cache clean                           |