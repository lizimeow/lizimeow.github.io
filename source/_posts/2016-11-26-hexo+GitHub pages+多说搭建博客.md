---
title: Hexo＋GitHub pages＋多说搭建博客
date: 2016/11/26 12:46:25
tags:
     - 教程
---

### 我的搭建环境

> OS: Windows_NT 6.3.9600 win32 x64
>
> hexo-cli: 1.0.2
>
> hexo：3.2.2
>
> hexo theme：material
>
> git：2.9.0.windows.1


## Step 1 安装 Node.js

[nodejs官网](https://nodejs.org/en/)

## Step 2 安装Hexo
`npm install -g hexo`

## Step 3 搭建本地博客
- **新建一个目录,如blog**

- **在该目录下执行**
  `hexo init`
  `npm install`
  此时默认主题默认配置的博客就搭建完成了

- **安装关于启动服务器的插件**
  `npm install hexo-server --save`

- **启动服务器 默认4000端口**
  `hexo s`
 
- 5\.打开[localhost:4000](localhost:4000)

## Step 4 主题和配置
在 [https://hexo.io/themes/](https://hexo.io/themes/)选择某个喜欢的主题，以[Material](https://github.com/viosey/hexo-theme-material)为例，假设当前目录为 “blog”。

前往material主题发布页面下载主题的压缩包

解压并将目录更名为**material**

 将**material**目录 移动至 **blog/themes/** 目录下

将 blog 目录下的 **_config.yml** 文件中的 **theme** 属性值改为 **material**


此时主题更换成功，可启动 server 验证效果


对于**_config.yml** 中的其他属性可根据情况自行修改

## Step 5 将博客部署到Github
- **新建仓库名为 `github用户名.github.io`**
  比如我的就是 [lizimeowww.github.io](lizimeowww.github.io)
  然后就可以访问了

- **安装 hexo 关于git 的组件**
  `npm install hexo-deployer-git --save`

- **在_config.yml 中为 git 添加配置**

  ```
  deploy:
      type: git
      repository: 你的仓库地址(git@github.com/用户名/用户名.github.io.git)
      branch: master
  ```

- **执行** 
	<每次修改都要执行这些命令才能在github pages看到效果>

  ```
  hexo generate
  hexo deploy
  ```

部署完成之后访问你的如 `lizimeowww.github.io` 的网址,即可看到和本地一样的效果。

## Step 6 删写博客

- **新建博客**
  `hexo new "文章名"`
  或者直接写一个.md文件丢进`source/_post`这个目录下就可以
  记得要加上Front-matter 
  {% asset_img 1.png image %}

- **删除文章**
  直接删掉 source/_post 下对应文章的文件，然后重新生成+部署即可。有时可能要多刷新几次才能看到效果。

## Step 7 评论系统

- 登录 [http://duoshuo.com/](http://duoshuo.com/)点击**我要安装**，设置自己的网站信息，获得以下通用代码

  ```html
  <!-- 多说评论框 start -->
  	<div class="ds-thread" data-thread-key="请将此处替换成文章在你的站点中的ID" data-title="请替换成文章的标题" data-url="请替换成文章的网址"></div>
  <!-- 多说评论框 end -->
  <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
  <script type="text/javascript">
  var duoshuoQuery = {short_name:"lizimeowww"};
  	(function() {
  		var ds = document.createElement('script');
  		ds.type = 'text/javascript';ds.async = true;
  		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
  		ds.charset = 'UTF-8';
  		(document.getElementsByTagName('head')[0] 
  		 || document.getElementsByTagName('body')[0]).appendChild(ds);
  	})();
  	</script>
  <!-- 多说公共JS代码 end -->
  ```

- 在`_config.yml`中添加多说的配置：

  ```
  duoshuo: 
    #on: true
    domain: http://lizimeowww.duoshuo.com
    # 是否开启多说评论，http://duoshuo.com/create-site/
    # 使用上面网址登陆你的多说，然后创建站点，在 domain 中填入你设定的域名
    # http://你的多说域名.duoshuo.com
  ```

- 打开`themes\landscape\layout\_partial\comment.ejs`，全选，替换为

  ```ejs
  <% if (page.comments){ %>  
  <section id="comment">  
    这里要放置多说提供的通用代码  
  </section>  
  <% } %>  
  ```

- 修改模板

  将data-thread-key="请将此处替换成文章在你的站点中的ID" 替换成data-thread-key="<%= page.path %>"
  将data-title=”请替换成文章的标题” 替换成 data-title="<%= page.title %>"
  将data-url=”请替换成文章的网址” 替换成 data-url="<%= page.permalink %>"

  ​

## 总结 
看教程不如看官方文档！

[Hexo的官方文档](https://hexo.io/zh-cn/docs/)

[material主题的官方文档](https://material.vss.im/start/)

~~多说好丑~~

原本接了Travis CI 自动部署 不知道哪里搞错...博客就被我玩坏了😂

 等我考完试再把多说改成Disqus..&重新弄一下自动部署好了.....

