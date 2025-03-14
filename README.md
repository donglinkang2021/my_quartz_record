# Quartz + Obisidian + gh-pages

> 全流程个人免费快速建站教程

在配置环境之前先明确两个概念：

- 笔记库：存放你的笔记文档的仓库，包含`.obsidian`文件夹
- 编译库：我们把`clone`下来的`quartz`仓库称为编译库

在本教程中我们希望通过维护两个库的形式，笔记库就是平时写笔记就可以了，当然也可以维护一个单独的github仓库，我们是通过`npx quartz create`选择`Copy an exsiting folder`来实现迁移仓库。

参考

- [Setting up your GitHub repository](https://quartz.jzhao.xyz/setting-up-your-GitHub-repository)
- [博客发布流程](https://8cat.life/others/%E5%8D%9A%E5%AE%A2%E5%8F%91%E5%B8%83%E6%B5%81%E7%A8%8B)
  - 这个教程仅供参考，觉得有点太复杂了
- [How to publish your notes for free with quartz](https://www.youtube.com/watch?v=6s6DT1yN4dw&t=227s&ab_channel=NicolevanderHoeven)

## 本地编译库配置

先远程先建好对应的编译库repo，比如我的是`donglinkang2021/RL-Note`

本地配置好如下选项

```bash
git clone https://github.com/jackyzha0/quartz.git
git clone git@github.com:jackyzha0/quartz.git
cd quartz
npm i
npx quartz create
# select `Copy an exsiting folder` 
# and then enter the absolute path of your vault folder
# such as `C:\Users\dongl\Documents\Note\ReinforcementLearning` 
```

接着配置好`quartz\quartz.config.ts`中的为`baseUrl: "yourname.github.io"`

```bash
git remote set-url origin REMOTE-URL
git remote add upstream https://github.com/jackyzha0/quartz.git
```

接着执行下面命令看一下

```bash
(base) PS C:\Users\dongl\Documents\Note\quartz> git remote -v
origin  git@github.com:donglinkang2021/RL-Note.git (fetch)
origin  git@github.com:donglinkang2021/RL-Note.git (push)
upstream        https://github.com/jackyzha0/quartz.git (fetch)
upstream        https://github.com/jackyzha0/quartz.git (push)
```

执行下面命令自动上传github，不用再执行push(甚至也可以不用commit)

```bash
npx quartz sync --no-pull
```

## 配置远程Host

先去github repo中的`Settings`->`Pages`->`Build and deployment`->`Source`改成`Github Actions`

最后配置`quartz/.github/workflows/deploy.yml`

```yml
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

直接执行

```bash
npx quartz sync
```

## 之后的快速操作

```bash
# 更新content文件夹
cd quartz
rm -r content/* -ErrorAction SilentlyContinue
cp -r C:\Users\dongl\Documents\Note\ReinforcementLearning\* content -Exclude .obsidian,.git
# cp -r C:\Users\linc\Documents\Note\ReinforcementLearning\* content -Exclude .obsidian,.git

# 本地编译查看效果
npx quartz build --serve

# 上传github发布
npx quartz sync
```

## 定制化

- 修改`quartz\styles\custom.scss`部分来使得markdown中的图片

```scss
@use "./base.scss";

// put your custom CSS here!
img {
    display: block;
    margin-left: auto;
    margin-right: auto;
}
```
