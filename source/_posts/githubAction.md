title: github action实践
date: 2019-09-25 21:48:34
tags:
---
看了阮一峰老师的github action入门，希望能实践一下。

<!-- more-->

hexo部署文章，需要先

``` bash
hexo -n new blog name
```
新建文章

```bash
hexo g
```
生成静态文件

```bash
hexo d
```
根据_config.yml部署到github page上。然而hexo的源码文件我也希望能够上传到github仓库里，有了action脚本后，

```bash
hexo g hexo d
```
这一系列动作就可以由脚本完成：在每次把hexo源码上传至仓库时，就会触发action动作，自动部署到github page对应仓库上，简化部署博客步骤。


关键操作就是在自己的根目录下新建.github/workflows/xxx.yml文件。github就会自动根据yml里的操作执行一系列actions。也可以在github的action目录下根据指引从网站界面上新建一个yml文件，网站中的第一个例子是echo hello world。很好理解。

下面是自动部署hexo的action执行指令。

.github/workflows/deploy.yml

```bash
name: auto set hexo githubpage
on: push
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [10.x]
    steps:
      - uses: actions/checkout@v1
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
      - name: Configuration environment
        env:
          ID_RSA: ${{secrets.id_rsa}}
        run: |
          mkdir -p ~/.ssh/
          echo "$ID_RSA" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name 'kenziD'
          git config --global user.email '1369811517@qq.com'
      - name: Install dependencies
        run: |
          npm i -g hexo-cli
          npm i
      - name: Deploy hexo
        run: |
          rm -rf .deploy_git
          hexo g
          hexo d
```
查看运行日志
![Screen Shot 2019-09-25 at 10.32.30 PM.png](http://ww1.sinaimg.cn/large/70665107gy1g7c57022slj21vk0wuq9e.jpg)


### 参考
1.[GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)