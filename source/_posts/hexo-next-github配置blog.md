---
title: hexo + next + github配置blog
date: 2020-01-30 09:20:38
tags: 教程
categories: 博客配置
---

## 安装各种东西

1. node.js：[Node.js官网链接](https://nodejs.org/zh-cn/) 点击下载安装即可。
2. Git： [Git官网链接](https://www.git-scm.com/download/win) 点击下载安装即可。
3. deployer-git： 命令行里输入`npm install hexo-deployer-git --save`。
4. Hexo：
   1. 准备好一个用于存放博客文件的文件夹（比如Github），在该文件夹的根目录下运行刚才装完git以后会出现的git-bash.exe（这里其实直接用cmd进入命令行然后进入博客文件夹也可以）；
   2. 输入命令`npm install -g hexo-cli`

<!--more-->

## 初始化Hexo

1. 创建Hexo工程：接着刚才那个命令输入`hexo init blog`，这里会在这个文件夹下新建一个blog文件夹，如果把blog换成其他名字也是可以的。
2. 新建Post：命令行里进入blog文件夹（`cd blog`），输入`hexo new “HelloWorld”`，这里会在 **/blog/sources/_post/** 目录下生成 **HelloWorld.md** 文件，可以进去打开编辑内容，在之后想写新博客也是通过这个命令生成文件，或者直接把写好的博客文件放到该目录下。
3. 生成静态文件：命令行里输入`hexo generate`，也可以简单地写成`hexo g`。这样就使用 **Hexo** 引擎将 **Markdown** 格式的文件解析成可以使用浏览器查看的 **HTML** 文件，**HTML** 文件存储在 **blog/public** 目录下。
4. 运行Hexo服务器：命令行里输入`hexo server`，也可以简写成`hexo s`。这时命令行会有一些提示，其中有个地址，一般是http://localhost:4000/ ，在浏览器里打开这个地址，就能看到博客网站是什么样的了。不过这个时候还只是一个本地博客。

## 安装主题

**Hexo** 提供了默认主题 **landscape**，主题的位置在 **blog ->themes** 文件夹下。主题根据自己喜好可以在网上找到，通过 **Git** 进行相应的下载。接下来以Next主题为例。

1. 还是在blog文件夹下，命令行里输入`git clone https://github.com/theme-next/hexo-theme-next themes/next`，这里是用`git clone`把[next主题](https://github.com/theme-next/hexo-theme-next)下载并存放到themes -> next 文件夹里。需要注意的是，`git clone`这个命令下载起来特别慢，但浏览器直接去github下载的话速度还行，所以也可以直接下载解压改文件夹的名字并放到对应的地方。
2. 修改 **/blog/config.yml** 文件，将其中的 **theme** 改成 **next**。如果文件夹的名字不是next，或者用了其他主题等，这里的theme改成对应的文件夹名字就行。
3. 接下来可以输入`hexo g` 和`hexo s`来查看新的本地博客。

## 建立Github博客

1. 在[Github官网](https://github.com/)注册账号并登陆，点首页右上角的加号新建一个仓库(Repository)，仓库的名字为用户名.github.io（比如我的是kun-bin.github.io），这里一定要是这个格式，不然之后没法直接访问博客网站。然后其他的都是默认内容，点create repository就可以了。如果仓库名字输入错了，之后可以在github上这个仓库页面的setting选项中修改。

2. 打开 **/blog/config.yml**文件修改deploy 属性(注意“：”之后有空格 ) 否则配置失败。

   ```
   deploy:
     type: git
     repository: https://github.com/kun-bin/kun-bin.github.io.git
     branch: master
   ```

   注意这里repository要替换成自己的仓库地址。

3. 初始化本地仓库：在blog文件夹下的命令行输入`git init`

4. 连接远程仓库：命令行接着输入`git remote add origin https://github.com/kun-bin/kun-bin.github.io.git`，这里记得把地址改成自己的仓库地址，第一次的时候会要求输入github的账号密码。

5. 发布博客页面：命令行里输入`hexo clean && hexo g && hexo d`，这等于一次性执行了清空、刷新、部署三个命令，此时在浏览器里已经可以通过`用户名.github.io`访问博客了，在github的这个仓库里也能看到**/blog/public**文件夹里的内容。

6. 可选：`hexo d`只是把public文件夹中的内容（也就是静态网页）传到了github的仓库里，应该还可以建立分支把博客配置文件整个传到github上，不过我没做这一步。

## 关于next主题的配置

目前在blog文件夹里有一个config.yml文件，我们称之为站点配置文件；而在themes/next文件夹里也有一个config.yml文件，称之为主题配置文件。

1. 基本信息：打开站点配置文件，找到site模块，可以修改博客标题、作者、描述、语言等等信息，

   ```undefined
   title: 标题
   subtitle: 副标题
   description: 描述
   author: 作者
   language: 语言（简体中文是zh-Hans）
   timezone: 网站时区（Hexo 默认使用您电脑的时区，不用写）
   ```

2. 菜单配置：初始网页只有两个菜单：home和archive，可以打开主题配置文件，找到menu模块，把需要的菜单前面的#删了，并注意把||**前面**的空格删了，不然可能会点了菜单以后报错（类似Cannot GET /tags/%20/这样的错）。

   ```cpp
   menu:
     home: /|| home                          //首页
     archives: /archives/|| archive          //归档
     categories: /categories/|| th           //分类
     tags: /tags/|| tags                     //标签
     about: /about/|| user                   //关于
     #schedule: /schedule/|| calendar        //日程表
     #sitemap: /sitemap.xml|| sitemap        //站点地图
     #commonweal: /404/|| heartbeat          //公益404
   ```
   这里||后面的单词是指菜单对应的图标，可以自己修改。此外，日程表那个模块，因为实际上调用的是谷歌日历，国内显然没法用，所以不建议使用。
   
3. 建立菜单对应的页面：还是在blog文件夹下打开命令行，输入`hexo new page "categories"`，然后在~/blog/source 文件夹中即可看到categories文件夹，打开里面的index.md文件，设置如下（注意：后面要加空格）：

   ```
   title: categories
   date: 2020-01-30 08:54:26
   type: "categories"
   ```

   tags页面也是一样的方式来建立。

4. 主题风格：一共有四个风格可以选择，打开主题配置文件 找到`Scheme Settings`，把想用的风格前面的#去掉，比如我这里用了Mist。

   ```bash
   # Schemes
   # scheme: Muse
   scheme: Mist
   # scheme: Pisces
   # scheme: Gemini
   ```

5. 侧栏设置：侧栏设置包括：侧栏位置、侧栏显示与否、文章间距、返回顶部按钮等等，打开主题配置文件 找到`sidebar`，按自己的喜好选择，把想要的那一项的#去掉或者false改成true即可。

6. 头像设置：主题配置文件找到`avatar`，修改avatar后面的路径为自己头像的路径，头像文件要放到`themes/next/source/images`中，比如头像文件名为`header.jpg`，就把那一行写成`avatar: /images/header.jpg`。如果前面有#就去掉#。

7. 添加搜索：

   1. 还是blog文件夹的命令行里，输入`npm install hexo-generator-searchdb --save`。

   2. 打开站点配置文件找到`Extensions`在下面添加

      ```bash
      # 搜索
      search:
        path: search.xml
        field: post
        format: html
        limit: 10000
      ```

   3. 打开主题配置文件找到`Local search`，将`enable`设置为`true`。

8. 还有很多能配置的，比如侧边栏头像下面有github链接，点击页面出现爱心符号，博客背景动态变化等，可自行搜索。

## 如何发布新的博客

1. 在blog文件夹的命令行里输入`hexo new "标题"`，然后进入**/blog/sources/_post/**找到对应的markdown文件就可以开始写了，也可以直接在这个文件夹下新建一个markdown文件，然后添加对应的文件头。写博客的时候根据需求在文件头里填写tags和categories的信息，然后hexo会自动建立好tags和categories的内容并给文章分类。

   文件头信息如下编写：
```
---
title:
date:
tags:
categories:
---
```

2. 写好以后保存，然后还是blog文件夹的命令行，输入`hexo clean && hexo g && hexo d`，就发布成功了。

3. 如果一个文章想设置多个标签，那么在编写文件头信息的时候，要按下面的方式写：

   ```
   tags；
   	- tag1
   	- tag2
   	...
   ```

   这里`tags:` 后面一定要换行，下面的`-`后面注意有空格。

## 参考链接：

https://segmentfault.com/a/1190000018761324

https://www.jianshu.com/p/3a05351a37dc

https://blog.csdn.net/nightmare_dimple/article/details/86661502