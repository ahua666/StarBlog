---
title: 使用 npm 作为图床
date: 2023-11-25 20:47:44
tags:
  - github
  - github actions
  - npm
categories:
    - github
    - github actions
    - npm
    - picgo
top_img: https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125213602.png
cover: https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125213602.png
---

## 大小限制

* npm 上传的文件大小限制为 100M，如果超过 100M 会报错。
* GitHub 图床仓库大小不能超过 1G。因为 GitHub 原则上是反对仓库图床化的，当仓库超过 1G 后会有人工审核仓库内容，如果发现用来做图床，轻则删库重则封号。需注意。
* jsDelivr 加速的单文件大小为 50M。这也就限制了单张图片大小上限。
* jsDelivr+npm 有 100MB 包大小限制
* npm 饿了么节点没有大小限制
* 目前我使用的是[cdn.cbd.int](https://cdn.cbd.int)

## 访问

```
jsd出品，网宿国内节点
https://cdn.jsdelivr.net/npm/:package@:version/:file
unpkg 自建
https://cdn.cbd.int/:package@:version/:file
```

## npm流程

1. 需要注册 npm 账号，可以去 npm 官网创建下，然后邮箱验证即可。
2. 在头像下拉表点击 Account，在 Two-Factor Authentication 处启用 2FA
   双重认证，记住，这部分十分重要！会关联到登录账号和发布验证的部分。官方文档：[2FA 双重认证](https://docs.npmjs.com/configuring-two-factor-authentication)
   ，我使用的是移动设备方式，安装 Google Authenticator 即可，后续扫描二维码添加账号成功。
3. 仓库克隆到本地，如果是第一次创建仓库可以克隆下，本地已有项目可以忽略这一步。

```shell
git clone git@自己仓库地址
```

4. 修改npm为原生源

```shell
npm config set registry https://registry.npmjs.org/
```

5. 添加本地 npm 用户设置:

```shell
# 仅第一次使用需要添加用户，之后会提示你输入你的npm账号密码以及注册邮箱
npm adduser
# 非第一次使用直接登录即可，之后会提示你输入你的npm账号密码以及注册邮箱
npm login
```

6. 运行 npm 初始化指令，把整个图床仓库打包，按照指示进行配置，注意需要事先确认你的包名没有和别人已发布的包重复，可以在 npm
   官网搜索相应包名，搜不到就说明还没被占用。

```shell
npm init
```

7. 然后输入发布指令，我们就可以把包发布到 npm 上了。

```shell
npm publish
```

8. npm 命令

```shell
npm init # 初始化
npm version patch # 更新版本号
npm publish # 发布
npm unpublish # 撤销发布
npm deprecate # 废弃包
npm unpublish --force # 强制撤销发布
npm search # 搜索包
npm info # 查看包的信息
npm whoami # 查看当前登录的用户
npm logout # 退出登录
npm config ls # 查看 npm 配置信息
```

9. 每次发布都需要更新版本号，否则会报错，可以使用以下命令更新版本号

```shell
npm version patch
```

## GitHub Actions

为了方便，我们使用 GitHub Actions 来自动化发布，这样我们只需要把图片上传到仓库，然后 GitHub Actions 就会自动把图片发布到
npm 上

创建一个 GitHub Actions 文件，文件名随意，我这里叫做 npm.yml，放在 .github/workflows 目录下，内容如下：

```yaml
name: Node.js Package
# 监测图床分支，2020年10月后github新建仓库默认分支改为main，记得更改
on:
  push:
    branches:
      - main

jobs:
  publish-npm:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Configure Git
        run: |
          git config --global user.name '你的用户名'
          git config --global user.email '你的电子邮箱'
      - uses: actions/setup-node@v4
        with:
          node-version: 18
          registry-url: https://registry.npmjs.org/
      - name: Bump version and push tag
        run: npm version patch -m "Bump version to %s"
        env:
          GITHUB_TOKEN: ${{ secrets.TOKEN }}
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.npm_token}}

      - name: Push Changes
        run: git push origin main
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

* 在npm 官网->头像->Access Tokens->Generate New Token,勾选 Automation 选项，Token只会显示这一次，之后如果忘记了就只能重新生成重新配置了。
  ![](https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125211417.png "创建token")
  ![](https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125211236.png "创建成功")
* 将 配置中的`你的用户名` 和 `你的电子邮箱` 替换成你的真实用户名和邮箱，然后在仓库的 Settings -> Secrets 中添加一个名为
  npm_token 的 secret，值为 npm 的 token
  ![](https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125211820.png)

* `重要` 请开启Workflow permissions的读写权限，不然会报错
  ![](https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125212340.png)

这样子每次使用PicGo上传图片到仓库，GitHub Actions 就会自动更新版本后然后把图片发布到 npm 上了。
![](https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20231125212507.png)
