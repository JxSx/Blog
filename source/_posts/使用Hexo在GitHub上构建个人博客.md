---
title: 使用Hexo在GitHub上构建个人博客
date: 2016-05-05 11:01:51
tags: 
- 博客
- Hexo
categories: 日志  
---
# Hexo介绍
Hexo 是一个简单地、轻量地、基于Node的一个静态博客框架。通过Hexo我们可以快速创建自己的博客。
对于个人用户而言，部署到GitHub上方便，快捷。省去了服务器成本。
[Hexo官网](http://hexo.io/)
# Hexo安装
* 安装Hexo之前建议先安装**Git.exe**和**node.js**； 
> https://git-scm.com/download/win
> https://nodejs.org/en/
* 创建目录,例如D:\Temp\Hexo,打开Git Bash,进入此目录
```    
$ cd D:
$ cd Temp/Hexo/
```
<!-- more -->
* 安装Hexo
```
$ npm install -g hexo
```
* 查看hexo版本
```
$ hexo version
```
* 初始化hexo
```
hexo init
```
* 启动Hexo服务器，提示端口打开
```
$ hexo server 或 hexo s
```
* 在浏览器访问 **http://localhost:4000** 出现默认的网页！至此，基本完成。

# 创建Github Pages
参考如下地址，分分钟完成**github pages**建站
> https://pages.github.com/

# 发布项目到Github
##静态化处理
Hexo是静态博客框架。静态博客，是只包含html, javascript, css文件的网站，没有动态的脚本。虽然我们是用Node进行的开发，但博客的发布后就与Node无关了。在发布之前，我们要通过一条命令，把所有的文章都做静态化处理，就是生成对应的html, javascript, css，使得所有的文章都是由静态文件组成的。

* 静态化命令
```
$ hexo generate 或 hexo g
```
* 配置
编辑Hexo目录下**站点配置文件**:_config.yml，找到deploy的部分，设置项目地址（注意username替换成自己的名字）：
```
deploy:
  type: git
  repo: https://github.com/username/username.github.io.git
  branch: master
```
* 部署命令
```
$ hexo deploy 或 hexo d
```
* 部署过程中遇到问题解决，需安装hexo自动部署git工具，然后在执行部署命令
```
$ npm install hexo-deployer-git --save
$ hexo deploy
```
* 每次部署步骤如下：
```
$ hexo clean
$ hexo generate
$ hexo deploy
```
至此，我们的博客就完美的发布到了Github上，可以访问以下http://username.github.io.git试试了。
当然，默认的效果也许你并不喜欢，你可以任意替换自己想要的主题。
# 配置主题
[**主题网站**](https://hexo.io/themes/)
在 Hexo 中有两份主要的配置文件，其名称都是 _config.yml。 其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置有主题作者提供，主要用于配置主题相关的选项。

为了描述方便，在以下说明中，将前者称为 **站点配置文件**， 后者称为 **主题配置文件**。
以NexT主题为例：
```
$ cd D:\Temp\Hexo
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
### 启动主题
打开 **站点配置文件**， 找到 theme 字段，并将其值更改为 next。
```
theme: next
```
### 修改语言
打开 **站点配置文件**， 找到 language 字段，并将其值更改为 zh-Hans。
```
language: zh-Hans
```
# NexT主题设置
详细的配置查看此网站
> http://theme-next.iissnan.com/

# 报错总结
ERROR Deployer not found: git 或者 ERROR Deployer not found: github
**解决方法**： 
```
npm install hexo-deployer-git --save
```

感觉自己棒棒哒！
