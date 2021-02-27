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

## 关于ayer主题的配置

今天（2021.02.26）为了让网页中的公式能稍微好一些，我换了一个新主题[ayer](https://shen-yu.gitee.io/2019/ayer/)。

1. 安装：在博客文件夹的目录下进命令行输入`git clone https://github.com/Shen-Yu/hexo-theme-ayer.git themes/ayer`，如果因为网络原因等问题导致GitHub没法用，可以改用码云：`git clone https://gitee.com/mirrors/ayer.git themes/ayer`；

2. 博客配置文件中的主题改成`ayer`；

3. 进主题配置文件进行自己需要的修改，比如调整logo、友情链接、启用公式等等；

4. 需要注意的是，这个主题的分类（categories）、标签（tags）等文件和next主题稍微不一样，需要改一下，以分类为例，进入`...\source\categories`文件夹，可以看到一个`index.md`文件，打开以后无论里面有什么内容，都彻底改成以下的：

   ```markdown
   ---
   title: categories
   date: 2020-01-30 08:54:26
   type: "categories"
   layout: "categories"
   ---
   ```

   相应地，其他用到的文件夹里的`index.md`也这样改。

5. 安装好以后进行`hexo clean`等等操作，就可以看到新主题的博客了，不过可能和我的屏幕尺寸有关，在文章页面中，固定的目录栏有些靠右，以至于目录显示不完整，而浮动的目录栏有些靠左了，会挡住正文，当目录很长时，文章几乎没法看了。这时需要打开主题中对目录栏的配置，`\themes\ayer\source-src\css\_partial`文件夹中的`tocbot.styl`文件，第一部分`.tocbot`的`right`属性从`-28rem`改成`-25rem`，这样固定目录栏就相对偏左一些，能显示完整了；第六部分`.is-position-fixed`的`right`属性从`6rem`改成`0`，这样浮动目录栏就会贴着右边，从而不挡左边了；

6. 按上面这个操作改完以后发现网页的显示并没有变化，接着打开`\themes\ayer\source\dist`文件夹中的`main.css`文件，然后直接搜索`-28rem`或者`tocbot`等关键词，找到以后把`-28`和`6`改成合适的数值（`-25`和`0`），重新部署网站就会发现目录栏变合适了。

## GitHub风格日历、标签云、分类雷达图的配置

由于next主题的css文件是swig格式，而网上查到的GitHub日历等都是ejs格式的，因此在用next主题时没能成功配置好日历等；改用Ayer主题后，该主题就是ejs格式的，因此现在添加了日历等内容。这一部分所有涉及到的下载链接都是在别人网站里复制的，如果链接失效可以直接在网上搜文件名来下载，也可以在我的GitHub项目中的主题文件里找已经修改和配置好的相关内容。

### 需要下载配置的文件

1. 下载[echarts.min.js](https://pan.miaodrew.workers.dev/Drew/web/js/echarts.min.js?rootId=0AAjrE2SiYnTaUk9PVA)，放到主题文件夹中的`\source\js`文件夹中，

2. 下载[matery.css](https://pan.miaodrew.workers.dev/Drew/web/css/matery.css?rootId=0AAjrE2SiYnTaUk9PVA)，放到主题文件夹中的`\source\css`文件夹中。

3. 打开`\layout\_partial\head.ejs`文件，在`head`标记中添加一行：

   `<link rel="stylesheet" type="text/css" href="/css/matery.css">`

   这一行直接加到`<head>`的下一行就好，在我的文件里是加到了第10行，如下图：

   ![image-20210227182758974](https://i.loli.net/2021/02/27/hAeoFcx8wIYlL2S.png)

### GitHub风格日历

1. 下载[post-calendar.ejs](https://pan.miaodrew.workers.dev/Drew/web/ejs-hexo/post-calendar.ejs?rootId=0AAjrE2SiYnTaUk9PVA)，放到`\layout\_widget`文件夹中，如果没有这个文件夹就自己新建一个。

2. 打开`post-calendar.ejs`文件，看第7行的内容是否为`<script type="text/javascript" src="/js/echarts.min.js"></script>`，如果不是则改成这样。

3. 在打算添加文章日历的地方，输入以下这行代码

   `<%- partial('_widget/post-calendar') %>`

   例如我要加到归档页面（`archive`），就打开`\layout\archive.ejs`文件，进行添加。不过Ayer主题的这个文件里就下面这行内容：

   `<%- partial('_partial/archive', {pagination: config.archive, index: true}) %>`

   如果直接在这一行前面或者后面添加日历代码，显示效果会不太好，而且会被左边的侧栏挡住，因此找到`\layout\_partial\archive.ejs`文件，在添加日历代码的时候可以多尝试几个位置，看哪个合适。我打算把日历放到“归档”这两个字的下面，文章列表的上面，于是整个文件的内容如下：

   ```ejs
   <section class="outer">
     <% if (theme.ads && theme.ads.length != 0){ %>
     <%- partial('ads') %>
     <% } %>
     <% if (theme.broadcast.enable && pagination == 2){ %>
     <%- partial('_partial/broadcast') %>
     <% } %>
     <article class="articles">
       <%
       var title = '';
       if (page.category) title = page.category;
       if (page.tag) title = "#" + "&nbsp" + page.tag;
       if (page.archive) {
         if (page.year) title = page.year + (page.month ? '/' + page.month : '');
         else title = __('archive_a');
       }
       %>
       <% if (pagination !== 2){ %>
       <h1 class="page-type-title"><%- title %></h1>
       <% } %>
       <% if (pagination == 2){ %>
       <% page.posts.each(function(post){ %>
       <%- partial('article', {post: post, index: true}) %>
       <% }) %>
     </article>
     <% } else { %>
     <% var last; %>
     <% page.posts.each(function(post, i){ %>
     <% var year = post.date.year(); %>
     <% if (last != year){ %>
     <% if (last != null){ %>
     </div>
     </div>
     <% } %>
     <% last = year; %>
     <%- partial('_widget/post-calendar') %>
     <div class="archives-wrap">
       <div class="archive-year-wrap">
         <a href="<%- url_for(config.archive_dir + '/' + year) %>" class="archive-year"><%= year %></a>
       </div>
       <div class="archives">
         <% } %>
         <%- partial('archive-post', {post: post, even: i % 2 == 0}) %>
         <% }) %>
         <% if (page.posts.length){ %>
       </div>
     </div>
     <% } %>
     <% } %>
   
     <% if (page.total > 1){ %>
     <nav class="page-nav">
       <%
           var prev_text = theme.nav_text.page_prev;
           var next_text = theme.nav_text.page_next
         %>
       <%- paginator({
           prev_text: prev_text,
           next_text: next_text
         }) %>
     </nav>
     <% } %>
   </section>
   ```

4. 最后呈现的效果如下：

![image-20210227184135159](https://i.loli.net/2021/02/27/sXmQr18FJ6HtNko.png)

### 标签云

1. 下载[tag-cloud.ejs](https://cdn.jsdelivr.net/gh/InfiniteYinux/cloud@2.44/Hexo/themeConfig/tag-cloud.ejs)，放到`\layout\_widget`文件夹中，如果没有这个文件夹就自己新建一个。

2. 打开`tag-cloud.ejs`，和前面那个日历一样，检查是否有`<script type="text/javascript" src="<%- theme.jsDelivr.url %><%- theme.libs.js.echarts %>"></script>`，如果有则替换为`<script type="text/javascript" src="/js/echarts.min.js"></script>`。如果没有就忽略。

3. 在需要添加标签云的地方，输入代码：`<%- partial('_widget/tag-cloud') %>`

4. 例如我要将这个添加到原本的标签页面，则找到`\layout\tags.ejs`文件，添加标签云代码。需要注意的是，Ayer主题本身会在这个页面自动生成标签，因此需要把`tags.ejs`文件中原本的标签内容那行去掉，整个文件改成了以下内容：

   ```ejs
   <section class="outer">
     <% if (site.tags.length){ %>
       <h1 class="page-type-title"><%= __('tags') %></h1>
   	<%- partial('_widget/tag-cloud') %>
     <% } %>
   </section>
   ```

5. 此外，标签云本身的标题和Ayer主题中的标题会重复显示，而我更喜欢Ayer本身那个标题，所以编辑了`tag-cloud.ejs`文件，把第25行的`<i class="fas fa-tags"></i>&nbsp;&nbsp;<%= __('postTagTitle') %>`中的`postTagTitle`改成了空格，之所以没直接去掉这一行，是因为觉得空一行好看一些。

6. 整体效果如图，和Ayer主题本身的标签相比，要好看一些。标签的颜色也可以在`tag-cloud.ejs`文件中自行修改。![image-20210227185130174](https://i.loli.net/2021/02/27/il6UtNjZsCkqozK.png)

### 分类雷达图

1. 下载[category-radar.ejs](https://pan.miaodrew.workers.dev/Drew/web/ejs-hexo/category-radar.ejs?rootId=0AAjrE2SiYnTaUk9PVA)，放到`\layout\_widget`文件夹中，如果没有这个文件夹就自己新建一个。

2. 依旧是检查替换`<script type="text/javascript" src="/js/echarts.min.js"></script>`，如果没有就忽略。

3. 在需要添加分类雷达图的地方，输入代码：`<%- partial('_widget/category-radar') %>`

4. 例如我要添加到原本的分类（`category`）页面，就找到`\layout\categories.ejs`，这里我不打算去掉原本的分类内容，因此不删除原本的代码，直接添加雷达图代码，修改后的文件整体如下：

   ```ejs
   <section class="outer">
     <% if (site.categories.length){ %>
       <h1 class="page-type-title"><%= __('categories') %></h1>
       <div class="categories-box">
         <%- list_categories() %>
       </div>
     <% } %>
     <%- partial('_widget/category-radar') %>
   </section>
   ```

5. 如果觉得这个雷达图和主题格格不入，想给它加个边框，则可以在刚才的`categories.ejs`文件最开头的地方粘贴以下代码：

   ```ejs
   <style type="text/css">
           #contentss {
           position: relative;
           width: 800px;
           height: 1200px;
           max-height: 1200px;
           margin-bottom: 15px;
           margin-top: 15px;
           text-align: center;
           border: 0;
           border-radius: 10px;
           color: rgba(0, 0, 0, .87);
           background: #fff 50%;
           background-size: cover;
           box-shadow: 0 15px 35px rgba(50, 50, 93, .1), 0 5px 15px rgba(0, 0, 0, .07);
           margin:0 auto;
           }
   </style>
   ```

   然后下面的雷达图代码修改为：

   ```ejs
   <div id="contentss">
   <%- partial('_widget/category-radar') %></div>`或者`<%- partial('_widget/post-calendar') %>
   </div>
   ```

   边框的大小样式颜色等等可以自行修改。不过Ayer主题不加边框就挺合适了，因此我没加。

6. 整体效果如下（这个截图是一开始没注意，把雷达图加到了标签页面，但是不影响雷达图的展示；此外，很显然分类名字过长就会显示不完整，这应该和雷达图的大小有关，懒得改了）：

![image-20210227190056808](https://i.loli.net/2021/02/27/4TRVLa5sFIzktBX.png)

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
   tags:
   	- tag1
   	- tag2
   	...
   ```

   这里`tags:` 后面一定要换行，下面的`-`后面注意有空格。

## 一些后续问题

今天（2020.03.16）突然发现当博客内容多起来以后，首页会有翻页，而翻页图标显示是乱的，查了一下发现要改`/Blog/themes/next/layout/_partials/pagination.swig`这个文件，内容整体替换成下面的：

```html
{%- if page.prev or page.next %}
 <nav class="pagination">
   {{
     paginator({
       prev_text: '<i class="fa fa-angle-left" aria-label="'+__('accessibility.prev_page')+'"></i>',
       next_text: '<i class="fa fa-angle-right" aria-label="'+__('accessibility.next_page')+'"></i>',
       mid_size: 1,
       escape: false
     })
   }}
 </nav>
{%- endif %}
```

## SSH登录配置

每次`hexo d`的时候都要输入用户名和密码会很麻烦，所以今天做了一下这个。

1. 首先进入博客目录比如`C:\Users\LiKun\Documents\GitHub\blog`，在这个目录下右键然后打开git bash，如果右键没这个选项那就去开始菜单之类的地方打开git bash然后一点点切目录；

2. 输入`ls -al ~/.ssh`检查目前是否有已经生成过的密钥文件，这个命令会输出`.ssh`里的文件，如果其中有`id_rsa.pub`或者`id_dsa.pub`，那就直接看第五步，如果这俩都没有就继续第三步；

3. 输入`ssh-keygen -t rsa -C "your_email@example.com"`，这里注意把自己的邮箱替换进去，看到提示`Enter file in which to save the key (/c/Users/you/.ssh/id_rsa):`时，直接按回车；

4. 然后会要求输入密码，这个密码要记住，在第一次使用该ssh密钥时系统会要求输入它，也就是说后面会用，**不过这里有个坑**；

5. 输入`clip < ~/.ssh/id_rsa.pub`，即可把密钥内容复制到剪贴板，或者自己进`.ssh`找到密钥文件复制也行；

6. 浏览器打开Github页面，右上角头像那里的下拉菜单有个Settings，点开以后左边找到`SSH and GPG keys`，点开以后右边的界面会列出目前所有的密钥，如果之前没做过那这里是空的；

7. SSH Keys那里点`New SSH Key`，名字随便设置，密钥部分粘贴第五步复制的那个，然后保存；

8. 接下来回博客目录，找到`_config.yml`，也就是站点配置文件，把最下面`deploy`属性中的`repository: `内容修改成`git@github.com:你的name/你的username.github.io.git`的样子，比如：

   ```html
   deploy:
     type: git
     repository: git@github.com:likun1208/likun1208.github.io.git
     branch: master
   ```

   这里有的地方也把`repository:`写成`repo:`，应该都行。

9. 然后命令行里`hexo d`，会要求输入之前第四步里的那个密码；

10. 我本来以为，第四步那个密码是输入一次之后就不用再输入的，然而尝试几次以后发现是每次都要输入，查了很多网页，有说配置ssh的config文件，有说修改host，总之都不太对，最终才发现，这一切都是因为第四步设置了密码，如果第四步不设置密码，一路按回车，就没有任何问题了！ssh似乎没提供修改密码的方式，所以就需要重新输入`ssh-keygen -t rsa -C "your_email@example.com"`，生成新的密钥文件并复制，然后去github的settings那里删旧密钥加新密钥，其他配置不用改动。

## 分支同步

今天研究了一下把博客所有文件上传到github仓库的一个分支，这样可以在多个电脑上同步写博客而不需要用U盘来回复制。首先，如果前面没有自己定义分支的名字，那么我们的网站内容是存放在`master`分支的，在这一步，我们会新建一个`hexo`分支，来存放博客文件（指主题文件和博客markdown文件等）。具体来说，当我们需要在多个电脑之间迁移博客时，会受影响的文件如下：

|  文件（夹）  |                      说明                       |
| :----------: | :---------------------------------------------: |
|  scaffolds/  |                 博客文章的模版                  |
|   source/    | 所有博客文章，以及about、tags、categories等page |
|   themes/    |                   网站的主题                    |
|  .gitignore  |         在push时需要忽略的文件和文件夹          |
| _config.yml  |                  站点配置文件                   |
| package.json |              依赖包的名称和版本号               |

1. 在github网站上找到自己的博客项目，新建`hexo`分支，并设置为主分支；

2. 在本地博客文件夹里打开git bash，输入`git clone git@github.com:用户名/用户名.github.io.git`，把`hexo`分支复制到本地，这里的用户名请自行替换；

3. 在博客文件夹里找到`.gitignore`文件，如果没有这个文件就自己新建一下，注意这个文件开头就是`.`，整个名字就是`.gitignore`，然后打开它里面写入以下内容，表示同步的时候忽略这些文件：

   ```
   .DS_Store
   Thumbs.db
   db.json
   *.log
   node_modules/
   public/
   .deploy*/
   ```

4. 注意，如果你之前克隆过theme中的主题文件，那么应该把主题文件中的`.git`文件夹删掉，因为git不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了了；

5. 然后在git bash里输入以下命令，即可完成`hexo`分支的上传：

   ```
   git add .
   git commit –m "add branch"
   git push
   ```

   这里的add branch可以改成其他内容，表示这次上传的说明注释；

6. 到此就完成了上传，这样换电脑以后，按照前面的内容把git和hexo以及其他相关东西都安装配置好以后，通过git把这个项目文件下载到本地，就又可以编辑博客了；

7. 如果多个电脑同时在写博客，时不时需要同步博客内容，就不需要`git clone`下载整个文件，只需要`git pull`把远程文件同步到本地即可；

8. 总结一下就是，每次写博客之前，首先`git pull`把github上的博客内容下载到本地；写完新的内容，首先进行`hexo clean, hexo g, hexo d`把内容渲染成网页并发布，接着`git add ., git commit -m "description", git push`把新增和修改的内容也上传到github，这样就可以实现白天在实验室写，晚上回宿舍写的操作了；

9. 以上操作也可以通过github的桌面版程序来完成，还挺方便。

## 参考链接：

https://segmentfault.com/a/1190000018761324

https://www.jianshu.com/p/3a05351a37dc

https://blog.csdn.net/nightmare_dimple/article/details/86661502

https://blog.csdn.net/lihangll/article/details/103335246

https://www.cnblogs.com/xinxiandong/p/3867505.html

https://blog.csdn.net/hhgggggg/article/details/77853665

https://blog.csdn.net/xiaomengzi_16/article/details/98847298

https://www.zhihu.com/question/21193762

https://blog.csdn.net/White_Idiot/article/details/80685990