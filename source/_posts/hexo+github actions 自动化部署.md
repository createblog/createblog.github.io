---
title: hexo+github actions 自动化部署
date: 2021-02-18 22:16:57
tags:
---

## Hexo + actions 自动化部署blog

### 0x01 Hexo所需的环境下载

`node.js`：https://nodejs.org/zh-cn/

`git`：https://git-scm.com/

配置`npm` 为淘宝的源，提升下载速度

```
npm config set registry https://registry.npm.taobao.org
```

### 0x02 Hexo安装并设置主题

`hexo` 下载

```
npm install hexo-cli -g
```

`hexo`初始化

```
hexo init blog
```

`hexo` 启动本地服务，查看是否部署成功

```
hexo s
```

开始设置主题了，这里选择的是 `Cactus`，fq的可以使用下面的命令

```
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```

也可以使用的`github代理下载`  网站如下：https://gh.api.99988866.xyz/ ，使用该方式下载后需要解压到`blog\themes` 目录下并且重命名为`cactus`

设置`_config.yml` 配置文件

```
theme: cactus
```

### 0x03 Github/Git设置

创建`repositories` 就不说了，名字是这个：`creatblog.github.io`

{% asset_img image-20210218224600331.png %}

初始化git

```
git init 
```

添加远程版本库：git remote add origin git@github.com:`username`/`username`.github.io.git

```
git remote add origin git@github.com:createblog/createblog.github.io.git
```

更改配置文件`_config.yml`，值得注意的是现在github中创建blog的时候分支名默认是`main` ，我将其修改为`master`了需要与其对应。

```
deploy:
  type: git
  repo: https://github.com/createblog/createblog.github.io
  branch: master
```

{% asset_img image-20210214172123602.png %}

`hexo` 下载一键部署插件

```
npm install hexo-deployer-git --save
```

配置github的ssh的密钥，并将公钥保存到github中 https://github.com/settings/keys

```
ssh-keygen -t rsa -C "1@qq.com"
```

设置git的username和email

```
git config --global user.name "1"
git config --global user.email "1@qq.com"
```

hexo打包部署，会出现一个登录框点击之后打开会打开默认的浏览器，它需要登陆你的github账号

```
hexo d
```

{% asset_img image-20210214173233222.png %}

在创建一个分支用来上传 hexo的源文件

```
git checkout -b myblog
```

上传源文件

```
git add .
git commit -m "init"
git push --set-upstream origin myblog
```

### 0x04 配置github actions 自动化部署blog

在 hexo源代码的文件夹blog中创建`.github`文件夹，在创建一个子文件夹为`workflows`，在该文件夹下创建一个配置文件`deploy.yml`,目录结构是这样的：`C:\Users\test\Desktop\blog\.github\workflows` ，`deploy.yml`内容如下：

```
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v2 # If you're using actions/checkout@v2 you must set persist-credentials to false in most cases for the deployment to work correctly.
        with:
          persist-credentials: false

      - name: Install and Build 🔧 # This example project is built using npm and outputs the result to the 'build' folder. Replace with the commands required to build your project, or remove this step entirely if your site is pre-built.
        run: |
          npm install
          npm run build
        env:
          CI: false

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@releases/v3
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          BRANCH: master # The branch the action should deploy to.
          FOLDER: public # The folder the action should deploy.
```

再次上传github

```
git add .
git commit -m "init"
git push --set-upstream origin myblog
```

在 `myblog` 分支中，注意是否有一个 黄色的圆点  点进去。

{% asset_img image-20210214180608229.png %}

可以看到检测都通过了没有问题，返回上面的页面中的 小黄点 也会变成一个绿色的对号。

{% asset_img image-20210214180717158.png %}

我们测试一下直接在线编辑 `myblog`中的 md文件在标题后添加 !!!! ，提交后查看是否可以自动部署

{% asset_img image-20210214180924680.png %}

可以直接通过`https://createblog.github.io` 地址访问但是由于缓存的问题，没办法很快的查看更新后的效果。

我们可以直接在`master` 分支中进行查看 index.html 中的源代码 ，

{% asset_img image-20210214181516946.png %}

### 参考视频

https://www.bilibili.com/video/BV1dt4y1Q7UE?p=2

