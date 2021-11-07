## 脚手架核心价值

脚手架的核心目标是：**提升前端研发效能**

将研发过程：

- 自动化：项目重复代码拷贝/git操作/发布上线操作
- 标准化：项目创建/git flow/发布流程/回滚流程
- 数据化：研发过程系统化、数据化，使得研发过程可量化

大厂研发架构图：

![第二周大厂研发架构图](/Users/yuqingbo/Code/__MD__/my-note/前端/assets/第二周大厂研发架构图.png)

## 和自动化构建工具的区别

问题：Jenkins、Travis 等自动化构建工具已经比较成熟了，为什么还需要自研脚手架？

- 不满足需求：Jenkins、Travis 通常在git hooks中触发，需要在服务端执行（仅仅只能覆盖云构建端的操作），无法覆盖研发人员本地的功能，如：创建项目自动化，本地git操作自动化等
- 定制复杂：Jenkins、Travis 定制过程需要开发插件，其过程较为复杂，需要使用 Java 语言，对前端同学不够友好

## 从使用角度理解什么是脚手架

### 脚手架简介

```zsh
## vue 命令帮助信息
Usage: vue <command> [options]

Options:
  -V, --version                              output the version number
  -h, --help                                 output usage information

Commands:
  create [options] <app-name>                create a new project powered by vue-cli-service
  add [options] <plugin> [pluginOptions]     install a plugin and invoke its generator in an already created project
  invoke [options] <plugin> [pluginOptions]  invoke the generator of a plugin in an already created project
  inspect [options] [paths...]               inspect the webpack config in a project with vue-cli-service
  serve [options] [entry]                    serve a .js or .vue file in development mode with zero config
  build [options] [entry]                    build a .js or .vue file in production mode with zero config
  ui [options]                               start and open the vue-cli ui
  init [options] <template> <app-name>       generate a project from a remote template (legacy API, requires @vue/cli-init)
  config [options] [value]                   inspect and modify the config
  outdated [options]                         (experimental) check for outdated vue cli service / plugins
  upgrade [options] [plugin-name]            (experimental) upgrade vue cli service / plugins
  migrate [options] [plugin-name]            (experimental) run migrator for an already-installed cli plugin
  info                                       print debugging information about your environment

  Run vue <command> --help for detailed usage of given command.
```

脚手架本质是一个操作系统的客户端，它通过命令执行，比如：

```bash
# 创建一个名为`vue-test-app`的 vue 项目，覆盖当前目录下的文件（如果已有重名），使用淘宝源安装
vue create vue-test-app --force -r https://registry.npm.taobao.org
```

命令由 4 个部分组成：

- **主命令**：`vue`
- **command**：`create`
- command 的 **params**：`vue-test-app`
- command 的 **option**：`--force`、`-r`

### *脚手架安装原理

```bash
npm install -g @vue/cli
```

先把`@vue/cli`下载到`/usr/local/lib/node_modules`中，然后 tnpm 脚本（应该是）根据`@vue/cli`下的`package.json`的`bin`中的“命名”与“脚本入口文件”，去`/usr/local/bin/`下创建`@vue/cli`的软连接`vue`。

```javascript
// 在`/usr/local/lib/node_modules/@vue/cli`文件夹下的`package.json`
"bin":{
  "vue": "bin/vue.js" // 软连接名为 vue，脚本入口文件在 bin/vue.js
}
```

### *脚手架执行原理

执行`vue`命令，终端实则执行`/usr/local/bin/vue`（可以通过`which vue`查看），而这个`/usr/local/bin/vue`是`../lib/node_modules/vue-cli/bin/vue`（脚本）（真实执行）的软连接。

### JS 文件直接执行原理

问：`/usr/local/bin/vue`（JS 文件）为什么可以被直接执行？（而不需要使用`node /usr/local/bin/vue`）

因为在这个`js`文件的开头一行，指定脚本的解释程序`#!/usr/bin/env node`，执行 env 脚本，确定 node 位置，并执行。（如果不行，`chmod 777 vue`）

#### `#! /usr/bin/env`在脚本中的作用

脚本用 env 启动的原因，是因为脚本解释器在 linux 中可能被安装于不同的目录，**env 可以在系统的 PATH 目录中查找**。同时，env 还规定一些系统环境量。 而如果直接将解释器路径写死在脚本里，可能在某些系统就会存在找不到解释器的兼容性问题。

这种写法**主要是为了让你的程序在不同的系统上都能适用**。

## 脚手架本地 link 标准流程

### 官网说明

[官网链接](https://docs.npmjs.com/cli/v7/commands/npm-link)

```zsh
npm link (in package dir)
npm link [<@scope>/]<pkg>[@<version>]
alias: npm ln
```

- `npm link` : will create a symlink in the global folder `usr/local/lib/node_modules/<package>`;
- `npm link package-name`:  in some other location, `npm link package-name` will create a symbolic link from globally-installed `package-name` to `node_modules/` of the current folder;

### 我的测试

#### 本地库文件 package.json

```json
{
  "main": "./lib/index.js",
}
```

#### 本地脚手架 package.json

```json
{
  "main": "index.js",
  "bin": {
    "my-cli": "./bin/index.js"
  }
}
```

#### 链接“本地库文件”到“全局”

```zsh
cd my-lib
npm link
```

此时就会在 `/usr/local/lib/node_modules` 文件夹下创建一个软连接：`my-lib -> /Users/kiyonami/Code/__TEST__/my-lib`。

就相当于命令 `npm install -g my-lib`，同样是下载到这里。

如果要使用的话，引用需要全路径，`require("/usr/local/lib/node_modules/my-lib")`。

#### 链接“全局 node_modules”到“本地脚手架”

```zsh
cd my-cli
npm link my-lib
# npm unlink my-lib 可取消链接（删除软链接
```

如上，即可在本地`my-cli/node_modules` 创建 my-lib 软链接，链接到全局的 my-lib（全局的 my-lib 又链接到本地的 my-lib）。

**该操作的优点**：简化引用路径，更贴近本地开发——引用路径只需要 `require("my-lib")`， 就不需要 `require("/usr/local/lib/node_modules/my-lib")`。

这个方法同样适用于全局的任何库文件（例如之前全局安装的`lodash`）；也同样使用与没有安装的库文件，npm link 会先下载该库文件到全局，然后创建软链接。

#### 链接“本地脚手架”到“全局”

```zsh
cd my-cli
npm link
```

同样在 `/usr/local/lib/node_modules` 文件夹下创建一个软连接：`my-cli -> /Users/kiyonami/Code/__TEST__/my-cli`。

不同的是，还在 `/usr/local/bin` 中创建一个命令软链接：`my-cli -> ../lib/node_modules/my-cli/bin/index.js`。

### 小结

理解`npm link`：

- `npm link my-lib`：将`node`全局`node_modules`下的库文件`my-lib`，链接到当前项目中的`node_modules`下；
- `npm link`：将当前项目链接到`node`全局`node_modules`中作为一个库文件，并解析`bin`配置创建可执行文件（一样是软链接）；

理解`npm unlink`：

- `npm unlink`：将当前项目从`node`全局`node_modules`中移除（好像没有这个命令？我是一直失败）（查了一下 unlink 是 uninstall 的别名）（npm unlink -h）（npm uninstall -h）；
- `npm unlink your-lib`：将当前项目中的库文件依赖移除；

## 脚手架开发难点

- 分包：将复杂的系统拆分成若干个模块

- 命令注册：例 `vue create`、`vue add`、`vue invoke`

- 参数解析：

  ```bash
  vue command [options] <params>
  ```

  - options全称：`--version`、`--help`
  - options简写：`-V`、`-h`
  - 带params的options：`--path /Users/caimengxin/Desktop/vue-test`
  - 帮助文档（一堆文档显示）

- 还有很多，比如：命令行交互、日志打印、命令行文字变色、网络通信、文件处理......

## 原生脚手架开发痛点

原生，即为不使用 Lerna 等管理工具进行开发“脚手架”。

“脚手架开发难点”意在：脚手架的功能上的难点；“原生脚手架开发痛点”意在：在开发过程中会遇到的问题。

- 痛点一：重复操作
  - 多 package 本地link；
  - 多 package 依赖安装；
  - 多 package 单元测试；
  - 多 package 代码提交；
  - 多 package 代码发布；
- 痛点二：版本一致性
  - 发布时版本一致性；
  - 发布后相互依赖版本升级；

**package越多，管理复杂度越高**，所以需要 Lerna！

## Lerna 简介

> [官网](https://lerna.js.org/): Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm.

Lerna 是一个优化基于 git + npm 的多 package 项目的管理工具。

### 优势

- 大幅减少重复操作；
- 提升操作的标准化；

> Lerna是架构优化的产物，它揭示了一个架构真理：项目复杂度提升后，就需要对项目进行架构优化。架构优化的主要目标往往都是以效能为核心。

### 案例

使用Lerna管理的大型项目：

- babel：https://github.com/babel/babel
- vue-cli：https://github.com/vuejs/vue-cli
- create-react-app：https://github.com/facebook/create-react-app

### Lerna 开发脚手架流程

<img src="/Users/yuqingbo/Code/__MD__/my-note/前端/assets/第二周lerna开发脚手架流程.png" alt="第二周lerna开发脚手架流程" style="zoom:50%;" />

### Lern使用细节

具体可以参考官网的 Document [命令说明](https://github.com/lerna/lerna)。

- `lerna init`:

  会自动完成`git`初始化，但不会创建`.gitignore`文件，这个必须要手动添加，否则会将`node_modules`目录都上传到`git`，如果`node_modules`已经加入到`git stage`，可使用：

  ```bash
  git reset HEAD <file>
  ```

  执行`unstage`操作，如果文件已经被`git`监听到变更，可使用：

  ```bash
  git checkout -- <filename>
  ```

  将变更作废，记得在执行操作之前将文件加入：`.gitignore`

- `lerna add`：

  - 第一个参数：添加npm包名
  - 第二个参数：本地package的路径
  - 选项：
    - `--dev`：将依赖安装到`devDependencies`，不加时安装到`dependencies`

  ```bash
  lerna add <package> [loc] --dev
  ```

- `lerna link`：

  如果未发布上线，需要手动将依赖添加到`package.json`，再执行`npm link`

- `lerna clean`：

  只会删除`node_modules`，不会删除`package.json`中的依赖

- `lerna exec`和`lerna run`：

  `--scope`属性后添加的是包名，而不是`package`的路径，这点和`lerna add`用法不同

- `lerna publish`

  - 发布时会自动执行：`git add package-lock.json`，所以package-lock.json不要加入`.gitignore`文件
  - 先创建远程仓库，并且同步一次master分支
  - 执行`lerna publish`前先完成`npm login`
  - 如果发布的npm包名为：`@xxx/yyy`的格式，需要先在npm注册名为：xxx的organization，否则可能会提交不成功
  - 发布到`npm group`时默认为private，所以我们需要手动在`package.json`中添加如下设置

```javascript
"publishConfig":{
  "access":"public"
}
```

## Lerna 浅显原理

- 通过 import-local 来优先调用本地 lerna 命令；
- 通过 Yargs 初始化脚手架，然后注册全局属性，再注册命令，最后通过 parse 方法解析参数；
- lerna 命令注册时需要传入 build 和 handler 两个函数，build 用来注册命令专属的 options，handler 用来处理命令的业务逻辑；
- lerna 通过配置 npm 本地依赖的方式进行本地开发，具体写法是在 package.json 中写入：file:your-locale-module-path,在lerna publish的时候会自动替换路径；

## [Yargs](http://yargs.js.org/) 使用

```javascript
#! /usr/bin/env node

const yargs = require('yargs')
const dedent = require('dedent')
const pkg = require('../package.json')

const cli = yargs()
const argv = process.argv.slice(2)
const context = {
  imoocVersion: pkg.version
}
cli
    // 配置 第一行的使用提示
  .usage('Usage: imooc-cli-cai [command] <options>')
    // 配置 提示用户脚手架最少要接收一个命令
  .demandCommand(1,'A command is required. Pass --help to see all available commands and options.')
    // 配置 命令输入错误或者没有此命令的时候可以根据输入推荐合适的命令
  .recommendCommands()
    // 配置 命令错误时执行的方法
  .fail((err,msg)=>{
    console.log(err)
  })
     // 配置 help和version的别名
  .alias('v','version')
  .alias('h','help')
    // 配置 终端宽度
  .wrap(cli.terminalWidth())
    // 配置 尾部的提示文字
  .epilogue(dedent`
      when a command fails,all logs are written to lerna-debug.log in the current working directory.

      For more information,find our manual at https://github.com/lerna/lerna
    `)
    // 配置 debug命令
    .options({
      debug:{
        type: "boolean",
        describe: "Bootstrap debug mode",
        alias: "d"
      }
    })
    // 配置 registry命令
    .option('registry',{
      type: "string",
      describe: "Define global registry",
      alias: 'r'
    })
    // 配置 分组
    .group(['debug'],'Dev Options')
    .group(['registry'],'Extra Options')
    // 配置 命令，当执行 init [name] 命令的时候一系列的行为
    .command('init [name]', 'Do init a project',(yargs)=>{
      yargs
        .option('name',{
          type: 'string',
          describe: 'Name of a project',
          alias: 'n'
        })
    },(argv)=>{
      console.log(argv)
    })
    // 配置 命令的第二种方法
    .command({
      command: 'list',
      aliases: ['ls','ll','la'],
      describe: 'List local packages',
      builder:(yargs)=>{},
      handler: (argv)=>{
        console.log(argv)
      }
    })
    // 配置 严格模式，最后一行提示命令错误，如：无法识别的选项：lis
  .strict()
     // 解析参数
  .parse(argv,context)
```
