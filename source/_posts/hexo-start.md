---
title: Hexo的基本使用方法
categories: 工具
tags: [博客,hexo使用]
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### 写文章
```
hexo n 'article_file_name'
```
在source/_posts下将生成article_file_name.md文件

### 生成post时默认生成categories配置项

在scaffolds/post.md中，添加一行categories:。同理可应用在page.md和photo.md。

### 文章中插入图片

方法一：使用本地路径：在hexo/source目录下新建一个uploads文件夹，将图片放入该文件夹下，插入图片时链接即为/uploads/图片名称。
方法二：使用微博图床，推荐 [<span style="color:blue">七牛</span>](https://portal.qiniu.com/signup)。

### 写不需要模板渲染的静态html页面
假设博客文件名为hexo

方法一：
1，加入要添加static目录，根目录的_config.yml中添加 一行： 
skip_render: [static/**],  
若有多个可在数组内添加，如 [static/**, some.html]
2, 在hexo/source下新建sta目录，然后再写xxx.html
访问 server_name/static/xxx.html

方法二：
在hexo/source下新建sta2目录，然后再写xxx.html
在html最上面加上两行，如下：
<span style="color:red">注意：layout:与false之间要有空格！！！</span>
```
layout: false
---

<!DOCTYPE>
<html>
....

```
访问 server_name/static/xxx.html

### 生成静态文件时奇怪事件处理

比如，写了个静态html文件，改了名，用 hexo g 时，会生出原来名和现在名两个文件
处理方法： 把根目录下的 db.json文件以及.deploy_git文件夹删了