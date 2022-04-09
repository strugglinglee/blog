---
title: 如何使用github pages + github Actions + VuePress构建站点
date: 2022-04-08 11:25:27
tags:
- github pages
- github Actions
- VuePress
categories:
- github
banner_img: https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3444618306,2493154184&fm=26&gp=0.jpg
index_img: https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3444618306,2493154184&fm=26&gp=0.jpg
---

# 如何使用github pages + github Actions + VuePress构建站点

最近一直想着将自己零零碎碎的笔记文档归纳一下，刚好之前看到好几个用Vuepress构建的文档都很清晰明了，又想尝试下github自带的CI/CD功能，所以就想使用github pages + github Actions + VuePress构建属于自己学习的站点。

这样既可以满足作为一个程序员习惯使用自己的编辑器记录，可以清晰的看到自己的历史提交记录，又可以通过自己建立的学习站点访问构建好学习文档沉浸式学习，实在是非常好的体验。

以下我会建立一个有关算法的学习站点作为示范。

## 使用VuePress构建项目

VuePress 诞生初衷是为了支持 Vue 及其子项目的文档需求，每一个由 VuePress 生成的页面都带有预渲染好的 HTML，也因此具有非常好的加载性能和搜索引擎优化（SEO）。
> [vuepress官方文档链接](https://vuepress.vuejs.org/zh/guide)

### 初始化

```shell
mkdir algorithm-learn && cd algorithm-learn
npm init
npm install -D vuepress
```

### 创建自己的第一个文档

![](/img/github/1.png)

```shell
mkdir docs && echo '# Hello algorithm' > docs/README.md
```

> 温馨提示： echo写入的方式可能会导致文档乱码，如发生这种情况请手动创建

### 在docs夹子下创建vuepress的配置文件

![](/img/github/2.png)
> 可以自己更换相关字段

```js
module.exports = {
  title: '算法学习',
  description: 'Just playing around',
  base: '/algorithm-learn/',
  configureWebpack: {
    resolve: {
      alias: {
        // "@alias": "path/to/some/dir",
      },
    },
  },
  // theme: 'reco',
  locales: {
    '/': {
      lang: 'zh-CN',
    },
  },
  themeConfig: {
    subSidebar: 'auto',
    nav: [
      { text: '首页', link: '/' },
      {
        text: '了解更多',
        items: [
          { text: 'Github', link: 'https://github.com/strugglinglee' },
          {
            text: '博客',
            link: 'https://blog.strugglinglee.cn/',
          },
        ],
      },
    ],
    sidebar: [
    //   {
    //     title: 'JS基础',
    //     // path: '/JavaScript',
    //     collapsable: true,
    //     children: [],
    //   }
    ],
  },
};
```

### 在package.json文件中增加这些命令

```json
  "scripts": {
    "dev": "vuepress dev docs",
    "build": "vuepress build docs"
  },
```

执行`npm run dev`就可以在本地看到一个初步的文档了

## github Actions + github pages操作流程

### 在github创建自己的仓库

![](/img/github/3.png)
并按照常规顺序把本地代码推到远程（略）

### 创建actions

搜索node,并在github上面的界面提交自动创建的代码，然后将代码拉到本地
![](/img/github/4.png)
![](/img/github/5-1.png)
![](/img/github/5.png)

参考如下代码替换node.js.yml文件呢
这里我们使用到了[JamesIves](https://github.com/JamesIves/github-pages-deploy-action)，注意版本的更新 可以参考github docs。
`JamesIves`也是阮一峰老师曾经在博文中推荐过的，目前star已近3k，还是可以放心使用的。

```yml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Install and Build 🔧 # This example project is built using npm and outputs the result to the 'build' folder. Replace with the commands required to build your project, or remove this step entirely if your site is pre-built.
        run: |
          npm install yarn -g
          yarn install
          yarn run build
      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4.3.0
        with:
          branch: gh-pages # The branch the action should deploy to.
          ACCESS_TOKEN: ${{ secrets.access_token_key }}
          FOLDER: docs/.vuepress/dist
          CLEAN: true

```

### 将本地代码推送到远程

远程将会创建一个gh-pages分支，这个分支上的代码就是我们构建出的最后站点所需要的资源

![](/img/github/6.png)

### 设置github pages

![](/img/github/9.png)

## 完结撒花

通过链接访问[https://strugglinglee.github.io/algorithm-learn/](https://strugglinglee.github.io/algorithm-learn/)

![](/img/github/10.png)

## 源码地址

[https://github.com/strugglinglee/algorithm-learn](https://github.com/strugglinglee/algorithm-learn)
