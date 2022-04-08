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


## 使用VuePress构建项目

> [vuepress官方文档链接](https://vuepress.vuejs.org/zh/guide)

比如我现在想做一个有关算法的学习文档
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

搜索node,并提交自动创建的代码，然后将代码拉到本地
![](/img/github/4.png)
![](/img/github/5-1.png)
![](/img/github/5.png)

参考如下代码替换node.js.yml
这里我们使用到了[JamesIves](https://github.com/JamesIves/github-pages-deploy-action)，注意版本的更新 可以参考github docs

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


### 创建密钥

![](/img/github/7.png)
![](/img/github/8.png)

### 将本地代码推送到远程
远程将会创建一个gh-pages分支，这个分支上的代码就是我们构建出的最后站点所需要的资源

![](/img/github/6.png)

### 设置github pages

![](/img/github/9.png)

## 完结撒花

通过链接访问

![](/img/github/10.png)