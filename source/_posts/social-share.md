---
title: pc或h5（不是内嵌在app里的）网页分享到社交应用
description: "web页面分享到社交应用的js、html总结，包QQ,微信,QQ空间,微博"
categories: 前端
tags: [前端,h5,分享]
---

## 最近有做pc或h5（不是内嵌在app里的）网页分享到社交应用的功能，几经探索做些总结记录

### 说是微信不能从pc/h5这样的web页面分享呢
### QQ
```
	分享链接 http://connect.qq.com/widget/shareqq/index.html
	参数：
    	url: 要被分享出去的页面链接
    	title: 分享到QQ后显示的链接块的右上标题
    	summay: 分享到QQ后显示的链接块的右下介绍
    	pics: 分享到QQ后显示的链接块的左边图片
    	desc: 分享到QQ后紧随显示的链接块下的单独消息语句
```

<a class="share_draw" target="_blank" href="http://connect.qq.com/widget/shareqq/index.html?url=https%3A%2F%2Fhttp://www.littlemay.cn%2F&title=来自暖阳的博客&desc=技术博客等你来看哦&summary=来看看吧&pics=http%3A%2F%2Fcdn.66173.cn%2Fwwwv2%2Fcss%2Fimg%2Flogo.jpg" title="分享到http://www.littlemay.cn">扣扣QQtiny</a>
<a class="share_draw" target="_blank" href="http://connect.qq.com/widget/shareqq/index.html?url=http://admin1.66173.com&title=来自暖阳的博客o&desc=技术博客等你来看哦&summary=来看看吧&pics=http%3A%2F%2Fcdn.66173.cn%2Fwwwv2%2Fcss%2Fimg%2Flogo.jpg" title="分享到http://www.littlemay.cn">扣扣QQlocal</a>
<a class="share_draw" target="_blank" href="http://connect.qq.com/widget/shareqq/index.html?url=http%3A%2F%2Fm.66173.cn%2F%2Factivity.php%3Fact%3Dindex%23box_game2%230-sqq-1-61392-9737f6f9e09dfaf5d3fd14d775bfee85&title=来自暖阳的博客&desc=技术博客等你来看哦&summary=来看看吧&pics=http%3A%2F%2Fcdn.66173.cn%2Fwwwv2%2Fcss%2Fimg%2Flogo.jpg" title="分享到http://www.littlemay.cn">扣扣QQniuniu</a>