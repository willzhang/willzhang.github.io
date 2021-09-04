---
title:      "在 Win10 中安装 Vue-Cli"
subtitle:   "在 Win10 中安装 Vue-Cli"
description: "本文演示如何在 Window 10 中安装 Vue 命令行工具 (Vue-Cli)。"
author:     "will"
date:       2021-09-05
tags:
    - vpn
categories: [ frontend ]
image: "img/Statue-of-Liberty.jpeg"
---

## 在 Win10 中安装 Vue-Cli

本文演示如何在 Window 10 中安装 Vue 命令行工具 (Vue-Cli)。



本文假设你已经在你的 Win10 中已经安装好了 node.js。 如果还没有安装，可以参考 [使用nvm切换Node的不同版本(Windows)](https://www.mls-tech.info/node/node-nvm-switch-version/), 安装好 node 和 yarn。 接下来，我们用 yarn 来安装 Vue-Cli。

## 用 Yarn 安装

首先打开命令行或 Windows Terminal, 执行:

Copy

```
yarn global add @vue/cli
```

依赖于网络的速度，最终安装好后，系统出现类似信息:

Copy

```
yarn global v1.22.4
[1/4] Resolving packages...
warning @vue/cli > @vue/cli-shared-utils > request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
warning @vue/cli > @vue/cli-ui > vue-cli-plugin-apollo > nodemon > chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
warning @vue/cli > jscodeshift > micromatch > snapdragon > source-map-resolve > resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
warning @vue/cli > jscodeshift > micromatch > snapdragon > source-map-resolve > urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
warning @vue/cli > @vue/cli-ui > vue-cli-plugin-apollo > nodemon > chokidar > fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
[2/4] Fetching packages...
info fsevents@1.2.13: The platform "win32" is incompatible with this module.
info "fsevents@1.2.13" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
warning "@vue/cli > jscodeshift@0.9.0" has unmet peer dependency "@babel/preset-env@^7.1.6".
[4/4] Building fresh packages...
success Installed "@vue/cli@4.4.1" with binaries:
      - vue
Done in 59.71s.
```

## 配置环境变量

执行:

Copy

```
vue
```

你会发现系统提示 vue 不是一个内部或外部命令。显然，我们还需要在环境变量 Path 中指定 vue 所在的路径（目录）。

执行:

Copy

```
yarn global list
```

查看当前在 yarn 中全局安装的组件, 系统显示:

Copy

```
yarn global v1.22.4
info "@vue/cli@4.4.1" has binaries:
   - vue
Done in 2.80s.
```

也就是说 vue-cli 已经被安装了。问题肯定是出现在 path 环境变量上。

那怎么找到 Yarn 组件的全局安装路径呢？

执行:

Copy

```
yarn global bin
```

系统显示（这是在我的环境中的路径，你的可能会不一样）:

Copy

```
C:\Users\stu\AppData\Local\Yarn\bin
```

打开这个目录，可以看到 vue 脚本命令，现在只需要在环境变量 path 中增加该目录即可。

增加之后，重新打开一个 cmd 或 Windows Terminal, 就可以执行 vue 了 。比如执行:

Copy

```
vue --version
```

系统显示:

Copy

```
@vue/cli 4.4.1
```

接下来，就可以愉快的使用 vue-cli 了。