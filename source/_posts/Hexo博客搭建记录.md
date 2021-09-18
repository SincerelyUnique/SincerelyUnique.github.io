---
title: Hexo博客搭建记录
date: 2021-09-18 08:45:40
tags: blog
categories: 
---

# 简单搭建Hexo博客站点

## Hexo基本安装和使用

1. install git & nodejs
2. 执行下面命令：
```bash
$ npm install -g hexo-cli
$ hexo init myBlog
$ cd myBlog
$ npm install
$ hexo s
```
3. 本地测试：visit: http://localhost:4000/
4. 新建一个post（即新建一篇博客文章，markdown格式）: 
```bash
$ hexo new "My New Post"
```
5. 运行服务器: 
```bash
$ hexo server  # 或者hexo s
```
6. 生成静态文件Generate static files
```bash
$ hexo generate
```
7. 部署到远程：Deploy to remote sites
```bash
$ hexo deploy
```

## 安装主题

主题以https://github.com/amehime/hexo-theme-shoka为例
1. 克隆主题到上面生成好的theme文件夹内
> git clone https://github.com/amehime/hexo-theme-shoka.git ./themes/shoka
2. 替换自己生成的_config.yml内容为作者[example](https://github.com/amehime/hexo-theme-shoka/tree/master/example)里面的_config.yml内容
3. 拷贝_config.shoka.yml到自己博客根目录，同时拷贝[source](https://github.com/amehime/hexo-theme-shoka/tree/master/example/source)目录下内容到自己上面生成的source目录
4. 启动服务：$hexo s

## 遇到的一些报错信息

1. FATAL YAMLException: bad indentation of a mapping entry (73:11)

yaml格式问题，未对齐
参考：https://github.com/amehime/hexo-theme-shoka/issues/24

2. Function yaml.safeLoad is removed in js-yaml 4. Use yaml.load instead, which is n

替换yaml.safeLoad为yaml.load
参考：https://blog.csdn.net/weixin_45149481/article/details/116609116

3. Prism's Diff Highlight plugin requires the Diff language definition (prism-diff.js).

Make sure the language definition is loaded or use Prism's Autoloader plugin.


## 绑域名
向你购买的域名的 DNS 配置中添加 3 条记录，同时在source目录添加CNAME文件，文件里写上域名
```
@          A             192.30.252.153
@          A             192.30.252.154
www      CNAME         username.github.io.
```

## 设置评论

设置valine评论，获取APP ID 和 APP Key
https://valine.js.org/quickstart.html
这个参考文档进去注册，支付宝实名认证后即可

## 设置algolia搜索

https://www.algolia.com/
从上面官网菜单找到indices建个索引（根据提示操作4步）
找到API Keys菜单，在All API Keys新建API Key绑定上面的索引即可

```bash
algolia:
 appId: xxx
 apiKey: xxx
 adminApiKey: xxx
 chunkSize: 5000
 indexName: xxx
 fields:
   - title #必须配置
   - path #必须配置
   - categories #推荐配置
   - content:strip:truncate,0,4000
   - gallery
   - photos
   - tags

algolia_search:
  enable: true
  hits:
    per_page: 10
  labels:
    input_placeholder: Search for Posts
    hits_empty: "我们没有找到任何搜索结果: ${query}"
    hits_stats: "找到${hits}条结果（用时${time} ms）"
```

## CD自动化部署

博客文章提交到github上source分支触发 github actions 脚本，自动发布文章

```bash
name: Hexo Deploy

# 只监听 source 分支的改动
on:
  push:
    branches:
      - source

# 自定义环境变量
env:
  POST_ASSET_IMAGE_CDN: true

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      # 获取博客源码和主题
      - name: Checkout
        uses: actions/checkout@v2

#      - name: Checkout theme repo
#        uses: actions/checkout@v2
#        with:
#          repository: printempw/hexo-theme-murasaki
#          ref: master
#          path: themes/murasaki

      # 这里用的是 Node.js 14.x
      - name: Set up Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '14'

      # 设置 yarn 缓存，npm 的话可以看 actions/cache@v2 的文档示例
      - name: Get yarn cache directory path
        id: yarn-cache-dir-path
        run: echo "::set-output name=dir::$(yarn cache dir)"

      - name: Use yarn cache
        uses: actions/cache@v2
        id: yarn-cache
        with:
          path: ${{ steps.yarn-cache-dir-path.outputs.dir }}
          key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
          restore-keys: |
            ${{ runner.os }}-yarn-

      # 安装依赖
      - name: Install dependencies
        run: |
          yarn install --prefer-offline

      # 从之前设置的 secret 获取部署私钥
      - name: Set up environment
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh
          echo "$DEPLOY_KEY" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts

      # 生成并部署
      - name: Deploy
        run: |
          npx hexo deploy --generate
```

## 参考

https://github.com/oncletom/hexo-algolia
https://blog.csdn.net/qq_35479468/article/details/107335663
https://segmentfault.com/a/1190000017986794

## 附录

记录下git基本操作

```bash
echo "# SincerelyUnique.github.io" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/SincerelyUnique/SincerelyUnique.github.io.git
git push -u origin main


git remote add origin https://github.com/SincerelyUnique/SincerelyUnique.github.io.git
git branch -M main
git push -u origin main
```
