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

5. 这里补充一点：安装的版本很重要，如果hexo和node中某个版本较高，后面配置的时候会冲突，使用`hexo -v`可以查看版本。我目前使用的版本如下：
   
   ```
   hexo: 4.2.0
   hexo-cli: 4.2.0
   os: Windows_NT 10.0.19043 win32 x64
   node: 12.20.0
   v8: 7.8.279.23-node.45
   uv: 1.40.0
   zlib: 1.2.11
   brotli: 1.0.9
   ares: 1.16.1
   modules: 72
   nghttp2: 1.41.0
   napi: 7
   llhttp: 2.1.3
   http_parser: 2.9.3
   openssl: 1.1.1g
   cldr: 37.0
   icu: 67.1
   tz: 2019c
   unicode: 13.0
   ```

<!--more-->

## 初始化Hexo

1. 创建Hexo工程：接着刚才那个命令输入`hexo init blog`，这里会在这个文件夹下新建一个blog文件夹，如果把blog换成其他名字也是可以的。
2. 新建Post：命令行里进入blog文件夹（`cd blog`），输入`hexo new “HelloWorld”`，这里会在 **/blog/sources/_post/** 目录下生成 **HelloWorld.md** 文件，可以进去打开编辑内容，在之后想写新博客也是通过这个命令生成文件，或者直接把写好的博客文件放到该目录下。
3. 生成静态文件：命令行里输入`hexo generate`，也可以简单地写成`hexo g`。这样就使用 **Hexo** 引擎将 **Markdown** 格式的文件解析成可以使用浏览器查看的 **HTML** 文件，**HTML** 文件存储在 **blog/public** 目录下。
4. 运行Hexo服务器：命令行里输入`hexo server`，也可以简写成`hexo s`。这时命令行会有一些提示，其中有个地址，一般是http://localhost:4000/ ，在浏览器里打开这个地址，就能看到博客网站是什么样的了。不过这个时候还只是一个本地博客。

## 安装主题

**Hexo** 提供了默认主题 **landscape**，主题的位置在 **blog ->themes** 文件夹下。主题根据自己喜好可以在网上找到，通过 **Git** 进行相应的下载。接下来以Next主题为例。

1. 还是在blog文件夹下，命令行里输入`git clone https://github.com/theme-next/hexo-theme-next themes/next`，这里是用`git clone`把[next主题](https://github.com/theme-next/hexo-theme-next)下载并存放到themes -> next 文件夹里。需要注意的是，`git clone`这个命令下载起来特别慢，但浏览器直接去github下载的话速度还行，所以也可以直接下载解压改文件夹的名字并放到对应的地方。
2. 修改 **/blog/config.yml** 文件，将其中的 **theme** 对应的内容改成 **next**（比如最初是`theme: landscape`，现在要换成`next`主题，就改成`theme: next`）。如果文件夹的名字不是next，或者用了其他主题等，这里的theme字段改成对应的文件夹名字就行。
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
   "---"(这里的双引号是为了正确显示，实际用的时候没有，最后一行的双引号同理)
   title: categories
   date: 2020-01-30 08:54:26
   type: "categories"
   layout: "categories"   
   "---"
   ```
   
   相应地，其他用到的文件夹里的`index.md`也这样改。

5. 安装好以后进行`hexo clean`等等操作，就可以看到新主题的博客了，不过可能和我的屏幕尺寸有关，在文章页面中，固定的目录栏有些靠右，以至于目录显示不完整，而浮动的目录栏有些靠左了，会挡住正文，当目录很长时，文章几乎没法看了。这时需要打开主题中对目录栏的配置，`\themes\ayer\source-src\css\_partial`文件夹中的`tocbot.styl`文件，第一部分`.tocbot`的`right`属性从`-28rem`改成`-25rem`，这样固定目录栏就相对偏左一些，能显示完整了；第六部分`.is-position-fixed`的`right`属性从`6rem`改成`0`，这样浮动目录栏就会贴着右边，从而不挡左边了；

6. 按上面这个操作改完以后发现网页的显示并没有变化，接着打开`\themes\ayer\source\dist`文件夹中的`main.css`文件，然后直接搜索`-28rem`或者`tocbot`等关键词，找到以后把`-28`和`6`改成合适的数值（`-25`和`0`），重新部署网站就会发现目录栏变合适了。
7. 经过漫长的使用，忽然发现右边那个目录，在白天模式下，背景是半透明的那种效果，会挡住正文的一些内容，而黑夜模式下背景就透明了，什么也挡不住。经过一番研究，发现想要白天模式也透明背景，就需要修改`\themes\ayer\source-src\css\_partial`文件夹中的`tocbot.styl`文件，找`.is-position-fixed`字段中的`background-color`，把它的值修改为`transparent`，接着同样是进`\themes\ayer\source\dist`文件夹中的`main.css`文件，先定位到刚才`tocbot`那里，然后往后很近的位置能看到`is-position-fixed`字段，修改其中的`background`的值为`transparent`，这里不能直接全文检索`is-position-fixed`，因为有好多这个属性，必须精确修改`tocbot`的这个属性。保存后重新部署就可以看到效果了。
8. 又是漫长的使用，发现hexo在渲染公式的时候，会把一些符号识别成html标记，然后就没法正确渲染，网上查到的方法都是针对下划线、斜体等，解决方法是安装mathjax并修改转义符配置，但我出问题的是大于号和小于号，查到的方法都没啥用，最终找到一个方法，是在写公式的时候，不直接用尖括号来表示大于小于，而是用tex符号，即`\lt`表示小于，`\gt`表示大于，这样就不会出问题了，也不影响本地markdown的渲染。

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

| 文件（夹）        | 说明                                  |
|:------------:|:-----------------------------------:|
| scaffolds/   | 博客文章的模版                             |
| source/      | 所有博客文章，以及about、tags、categories等page |
| themes/      | 网站的主题                               |
| .gitignore   | 在push时需要忽略的文件和文件夹                   |
| _config.yml  | 站点配置文件                              |
| package.json | 依赖包的名称和版本号                          |

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

## 新版live2d

前面的内容提到了hexo本身可以安装插件并设置live2d的开关，打开以后加载对应模型就能显示了，但是在实际使用的过程中，我发现它支持的是旧版本的live2d，无法显示新版本的，因此研究一番试图加载新版本的。

首先简单说明一下，目前市面上的live2d模型有3个版本，最古老的是2.0版本，它只能用旧版的live2d cubism生成（官网没有对应的版本了），其次是3.0版本，它能用目前所有版本的[Live2D Cubism](https://www.live2d.com/zh-CHS/)软件生成，最新的是4.0版本，能用最新的live2d cubisim制作。虽然可以用同一个软件制作，但是3.0和4.0是不同的，差别在于sdk版本。它们在live2d的模型配置文件（.model.json）中的version都是3，然而用live2d cubism viewer打开查看moc3文件的信息就能发现sdk版本的差异了，这也导致网页渲染的时候要用不同的东西。

### 3.0版本的处理

在查找资料的过程中，我首先找到的是众多3.0版本的资料，对比一番可以发现虽然大家原理和代码语法都差不多，但[这个](https://weidows.github.io/post/Web/JavaScript/live2d-moc3/README/)是集大成作，它的行数最少，使用起来很简单，只需要找到主题文件夹中`layout`目录下的`layout.ejs`文件（`index.ejs`应该也可以），然后在最后的`</body>`前面添加以下几行代码：

```html
<!-- Live2DCubismCore -->
<script src="https://fastly.jsdelivr.net/gh/Weidows-projects/live2d-moc3/dist/live2dcubismcore.min.js"></script>
<!-- Include Pixi. -->
<script src="https://fastly.jsdelivr.net/gh/Weidows-projects/live2d-moc3/dist/pixi.min.js"></script>
<script src="https://fastly.jsdelivr.net/gh/Weidows-projects/live2d-moc3/dist/live2d.min.js"></script>

<script>addEventListener("DOMContentLoaded",function(){let models=[{left:"0px",bottom:"0px",basePath:"https://fastly.jsdelivr.net/gh/likun1208/Live2d_modules@gh-pages/assets",role:"test05261",background:"",opacity:1,mobile:false,},];new Live2dLoader(models)});</script>
```

代码中前3行是加载live2d相关的js文件，第四行是加载模型文件，这一步需要把自己要用的模型上传到GitHub，或者用别人Github中已有的文件。具体来说，需要在Github上建立一个仓库用来存模型文件，然后把模型文件放到仓库中，模型文件夹的名字与模型的`model3.json`文件的名字一定要保持一致，比如我这里的模型文件夹是`gongzi`，那么里面的文件一定是`gongzi.model3.json`，其他文件名不重要。

最后在`js`代码中，`basePath`设置为如上述代码所示的`"https://fastly.jsdelivr.net/gh/likun1208/Live2d_modules@gh-pages/assets"`，因为我的模型是在仓库的`assets`文件夹中，如果直接放仓库里就不要加`assets`了，前面的`gh-pages`是分支名，没分支可以不加，再往前的`Live2d_modules`是仓库名，`likun1208`是用户名，`https://fastly.jsdelivr.net/gh/`是cdn加速，不详细介绍了。总之把`basePath`按这个方式设置好以后，再把`role`设置成模型文件夹的名字，也就是我这里的`gongzi`，必要的步骤就完成了，其他可设置的一系列参数在前面提到的参考文档中可以看到。

在把这些准备好以后，要记得把hexo本身的那个live2d关掉，不然两个都会出现。最后部署博客页面就能看到模型了。

### 4.0版本的处理

但是很可惜我想用的模型是4.0版本的，前面那段js会报错，在发现自己重做3.0版本模型特别艰难之后，我走上了寻找4.0解决方案的道路，并查到了[这个项目](https://github.com/Konata09/Live2dOnWeb)，它能自动识别模型版本并用对应的方式渲染，哪个版本都兼容，还可以放多个模型来回切换，这非常好。

需要注意的是，如果模型中有expression等字段，它是无法解析的，虽然不影响模型显示，但是对应的表情动作都不会生效。所支持的字段可以查看项目中给的模型例子然后照着写。

具体在hexo博客中的使用，首先需要把项目文件整个都下载好，把其中的`dist`和`model`这两个文件夹复制到主题文件夹中的`source`文件夹下，把其中的`waifu-tips.js`和`waifu-tips.json`这两个文件复制到前述`source`文件夹中的`js`文件夹下，现在的文件目录如下（没提到的文件未列出）：

```
- blog
    -themes
        -ayer(主题文件夹)
            -source
                - dist
                - model
                - js
                    waifu-tips.js
                    waifu-tips.json
```

文件都放好以后，打开`waifu-tips.js`，在前面有一行`'modelUrl': 'model', // 存放模型的文件夹路径，末尾不需要斜杠`，把这一行修改为`'modelUrl': '/model', // 存放模型的文件夹路径，末尾不需要斜杠`（在model前面加`/`，不然网站解析的时候就不是绝对路径了）；打开前面提到过的主题文件夹中`layout`目录下的`layout.ejs`文件，在最后的`</body>`前面加下列内容：

```html
<div id="waifu">
    <div id="waifu-message"></div>
    <div class="waifu-tool">
        <span class="icon-next"></span>
        <span class="icon-home"></span>
        <span class="icon-message"></span>
        <span class="icon-camera"></span>
        <span class="icon-volumeup"></span>
        <span class="icon-volumedown"></span>
        <span class="icon-about"></span>
        <span class="icon-cross"></span>
    </div>
    <canvas id="live2d2" style="top:150px"></canvas>
    <canvas id="live2d4" style="top:150px"></canvas>
</div>
<!--    src 中改为你存放的路径    -->
<script src="/dist/live2d_bundle.js"></script>
<script async type="module" src="/js/waifu-tips.js"></script>
```

关于`waifu-tips.js`中模型列表等各类内容的配置直接参考项目说明即可。

需要注意的是，这里`<canvas id="live2d2" style="top:150px"></canvas>`和它下一行的内容中，`style=top:150px`是我自己加的，正常模型不需要加这个。我加它的原因是要用的达达利亚模型画布比模型本身大一圈，自己没法修改，只能调整模型位置让它贴到页面边缘。不确定如果换个尺寸的显示屏，这里会不会又出问题，总之姑且先这样了。

接下来重新部署网站，就能看到模型了。

### 鼠标穿透问题
在用了很久以后，我发现模型总是会挡住页面的可点击内容，比如目录、文章标题等，估计是因为用的模型尺寸大一圈，有很高一片空白区域。这有些影响使用，于是想试着把模型改成可以鼠标穿透点击的效果。经过各种尝试，目前发现的一个解决方案确实有效，但是会导致无法显示模型的工具栏（有切换模型和回到首页等功能），也就是说只能固定显示一个模型，操作方法如下：打开`waifu-tips.js`，找到如下代码块：
```html
const waifuStyle = `
#waifu {
${live2d_settings.waifuEdgeSide}px;
position:fixed;
bottom:0;
z-index:998;
font-size:0
}
```

在最后一行`font-size:0`末尾加个分号，然后增加一行：`pointer-events: none`，修改后的代码如下：
```html
const waifuStyle = `
#waifu {
${live2d_settings.waifuEdgeSide}px;
position:fixed;
bottom:0;
z-index:998;
font-size:0;
pointer-events: none
}
```

这实际上就是把整个模型改成了不响应鼠标事件的效果，自然就能让鼠标点击穿透到页面的其他链接，但也同时导致工具栏无法响应。目前还没找到其他既能显示工具栏又能鼠标穿透的方法，先这样吧。

## 添加待办清单
今天（2023年10月18日）一时兴起，想在博客里添加个ToDo List，找到一个[参考链接](https://juejin.cn/post/6889975936381845512)，是直接写了个`html`放到对应的位置，并设置不渲染这个`html`。虽然这不是我想要的效果，但也勉强能用，于是记录一下整个操作过程：

1. 首先新建对应的菜单，这一步参照前面的内容，在主题配置文件中的menu字段加一行即可；
2. 其次新建菜单对应的文件夹，也是参照前面内容，文件夹里只放一个`index.html`，其中的内容如下：
	```html
	<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ToDo List</title>
    <script src="https://cdn.bootcss.com/vue/2.6.10/vue.min.js"></script>
    <style>
        body{
            margin:0;background-color:#fafafa;font:14px 'Helvetica Neue',Helvetica,Arial,sans-serif}
        h2{margin:0;font-size:12px}
        a{color:#000;text-decoration:none}
        input{outline:0}
        button{margin:0;padding:0;border:0;background:0;font-size:100%;vertical-align:baseline;font-family:inherit;font-weight:inherit;color:inherit;outline:0}
        ul{padding:0;margin:0;list-style:none}
        .page-top{width:100%;height:40px;background-color:#db4c3f}
        .page-content{width:50%;margin:0 auto}
        .page-content h2{line-height:40px;font-size:18px;color:#fff}
        .main{width:50%;margin:0 auto;box-sizing:border-box}
        .task-input{width:99%;height:30px;outline:0;border:1px solid #ccc}
        .task-count{display:flex;margin:10px 0}
        .task-count li{padding-left:10px;flex:1;color:#dd4b39}
        .task-count li:nth-child(1){padding:5px 0 0 10px}
        .action{text-align:center;display:flex}
        .action a{margin:0 10px;flex:1;padding:5px 0;color:#777}
        .action a:nth-child(3){margin-right:0}
        .active{border-bottom: 2px solid #629A9C}
        .tasks{background-color:#fff}.no-task-tip{padding:10px 0 10px 10px;display:block;border-bottom:1px solid #ededed;color:#777}.big-title{color:#222}.todo-list{margin:0;padding:0;list-style:none}
        .todo-list li{
            position:relative;
            font-size:16px;
            border-left: 5px solid #629A9C;
            box-shadow: 0 1px 2px rgba(0,0,0,0.07);
            margin-bottom: 16px;
        }
        .todo-list li:hover{background-color:#fafafa}
        .todo-list li.editing{border-bottom:0;padding:0;}
        .todo-list li.editing .edit{display:block;padding:13px 17px 12px 17px;margin:0 0 0 43px}
        .todo-list li.editing .view{display:none}
        .toggle{
            text-align:center;
            width:16px;
            height:16px;
            position:absolute;
            top:2px;
            left: 15px;
            bottom:0;
            margin:auto 0;
            cursor: pointer;
        }
        .todo-list li label{
            white-space:pre-line;
            word-break:break-all;
            padding:15px 60px 15px 15px;
            margin-left:45px;
            display:block;
            line-height:1.2;
            transition:color .4s
        }
        .todo-list li.completed label{
            color:#d9d9d9;
            text-decoration:line-through
        }
        .todo-list li .destroy{
            display:none;
            text-align:center;
            width:16px;
            height:16px;
            position:absolute;
            top:0;
            right:15px;
            bottom:10px;
            margin:auto 0;
            cursor: pointer;
            font-size:28px;
            color:#cc9a9a;
            transition:color .2s ease-out
        }
        .todo-list li .destroy:hover{color:#af5b5e}
        .todo-list li .destroy:after{content:'×'}
        .todo-list li:hover .destroy{display:block}
        .todo-list li .edit{display:none}
        .todo-list li.editing:last-child{margin-bottom:-1px}
    </style>
</head>
<body>
<div class="page-top">
    <div class="page-content">
        <h2>任务计划列表</h2>
    </div>
</div>
<div class="main">
    <h3 class="big-title">添加任务：</h3>
    <input
            placeholder="例如：吃饭睡觉打豆豆；  提示：+回车即可添加任务，双击列表标题即可编辑"
            class="task-input"
            type="text"
          v-on:keyup.enter="enterFn"
            v-model="todo"
    />
    <ul class="task-count">
        <li>{{unComplete}}个任务未完成</li>
        <li class="action">
            <a :class="{active:visibility!=='unCompleted'&&visibility!=='completed'}" href="#all">所有任务</a>
            <a :class="{active:visibility==='unCompleted'}" href="#unCompleted">未完成的任务</a>
            <a :class="{active:visibility==='completed'}" href="#completed">完成的任务</a>
        </li>
    </ul>
    <h3 class="big-title">任务列表：</h3>
    <div class="tasks">

        <span class="no-task-tip" v-show="!list.length">还没有添加任何任务</span>
        <ul class="todo-list" v-show="list.length">
            <li class="todo"
                v-for="item in filterData"
                v-bind:class="{completed:item.isComplete,editing:item===edtorTodos}"
            >
                <div class="view">
                    <input class="toggle"
                          type="checkbox"
                          v-model="item.isComplete"
                    />
                    <label @dblclick="edtorTodo(item)">{{item.title}}</label>
                    <button
                            class="destroy"
                            @click="delFn(item)"
                    ></button>
                </div>
                <input
                        class="edit"
                        type="text"
                        v-focus="edtorTodos===item"
                        v-model="item.title"
                        @blur="edtoEnd(item)"
                        @keyup.enter="edtoEnd(item)"
                        @keyup.esc="cancelEdit(item)"
                />
            </li>
        </ul>
    </div>
</div>
<script>
    //存取localStorage中的数据
var store = {
    save(key,value){
        window.localStorage.setItem(key,JSON.stringify(value));
    },
    fetch(key){
    return JSON.parse(window.localStorage.getItem(key))||[];
    }
}
//list取出所有的值
var list = store.fetch("storeData");

var vm = new Vue({
    el:".main",
    data:{
        list,
        todo:'',
        edtorTodos:'',//记录正在编辑的数据,
        beforeTitle:"",//记录正在编辑的数据的title
        visibility:"all"//通过这个属性值的变化对数据进行筛选
    },
    watch:{
        //下面的这种方法是浅监控
      /*list:function(){//监控list这个属性，当这个属性对应的值发生变化就会执行函数
          store.save("storeData",this.list);
      }*/
      //下面的是深度监控
        list:{
            handler:function(){
                store.save("storeData",this.list);
            },
            deep:true
        }

    },
    methods:{
        enterFn(ev){//添加任务
            //向list中添加一项任务
            //事件处理函数中的this指向的是当前这个根实例
            if(this.todo==""){return;}
                this.list.push({
                    title:this.todo,
                    isComplete:false
                });
                this.todo = "";
        },
        delFn(item){//删除任务
            var index = this.list.indexOf(item);
            this.list.splice(index,1)
        },
        edtorTodo(item){//编辑任务
            //编辑任务的时候记录编辑之前的值
            this.beforeTitle = item.title;
            this.edtorTodos = item;
        },
        edtoEnd(item){//编辑完成
            this.edtorTodos="";
            // this.cancelEdit = this.edtorTodos;
        },
        cancelEdit(item){//取消编辑
            item.title = this.beforeTitle;
            this.beforeTitle = '';
            this.edtorTodos='';
        }
    },
    directives:{
        "focus":{
            update(el,binding){
                if(binding.value){
                    el.focus();
                }
            }
        }
    },
    computed:{
        unComplete(){
        return  this.list.filter(item=>{
                return !item.isComplete
            }).length
        },
        filterData(){
            //过滤的时候有三种情况 all completed unCompleted
            var filter = {
                all:function(list){
                    return list;
                },
                completed:function(list){
                    return list.filter(item=>{
                        return item.isComplete;
                    })
                },
                unCompleted:function(list){
                    return list.filter(item=>{
                        return !item.isComplete;
                    })
                }
            }
            //如果找到了过滤函数，就返回过滤后的数据，如果没有找到就返回所有的数据
            return filter[this.visibility]?filter[this.visibility](list):list;
        }

    }
});
function hashFn(){
    var hash = window.location.hash.slice(1);
    vm.visibility = hash;
}
hashFn();
window.addEventListener('hashchange',hashFn);
</script>
</body>
</html>
```
3. 接着在博客配置文件中，找到`skip_render`字段，冒号后面加入这个清单网页的地址，例如`todo\*.html`；
4. 最后clean+g+d就完成了。

这个方法做出来的网页，是把添加的内容浏览器缓存了，也就是说，如果其他人打开我的博客添加了自己的内容，那么也只有添加者能看到，同样我添加的内容别人也看不到，甚至换个浏览器或者清缓存也会丢失内容。而且也没找到方法把它嵌入到我的主题里，这使得它和其他页面相比，非常不统一。此外，如果需要从这个待办页面返回博客，还得在代码里添加跳转博客地址的链接，例如`<h2 bref=链接>返回博客</h2>`。

鉴于这个网页外观和操作方式我都不太满意，也没找到更合适的方法，而且日常大多数时候会用跨平台软件来记录待办事项，最终决定去掉这个功能了。

## 添加相册
1. 输入`hexo new page photos`新建相册的页面，并注意在主题配置文件里添加对应的侧边栏；
2. 进入`photos`文件夹，修改其中的`index.md`如下：
```html
---
title: Gallery

albums: [["imgurl", "imgcaption"], ["imgurl", "imgcaption"]]
---
```
3.  然后把要添加的图片放到`photos`文件夹中，并把`index.md`文件夹中的`imgurl`和`imgcaption`修改成图像的路径和标题，路径要注意带上级文件夹，和后缀名，例如我的路径就是`photos/ff14-伊修加德.png`。
4. 部署后就能看到照片了，之后再加新的照片就也是复制进来，然后打开`index.md`文件，在`albums`的方括号里添加新的链接和标题。

## 评论
目前是在ayer主题下添加的评论，用了[Valine](https://valine.js.org/)，设置流程如下：

1. 注册[leancloud](https://www.leancloud.cn/)，需要填邮箱、密码等信息，注册完之后回首页，进入控制台，创建应用，选免费的开发版；
2. 创建好应用后，进入应用—>设置—>应用凭证，可以看到AppID和AppKey；
3. 打开ayer的主题配置文件，拉到最下面，找到`leancloud`字段，把它下面的`enable`设置为`true`，`app id`和`app key`分别设置成上一步页面中看到的ID和Key；
4. 在`leancloud`下面有`valine`字段，把`enable`设置为`true`，`avatar`和`placeholder`分别表示评论头像和评论区的提示语，可以根据喜好修改，不改也行；
5. 保存后重新生成网站就好了。


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

https://cloud.tencent.com/developer/article/1597223

https://cndrew.cn/2020/03/03/calender/

[👉live2d-moc3-web-集成渲染库 | ⭐️齐下无贰⭐️](https://weidows.github.io/post/Web/JavaScript/live2d-moc3/README/)

[GitHub - LitStronger/live2d-moc3: live2d, 适用于加载新版moc3游戏角色模型(碧蓝航线)。附带使用例子](https://github.com/LitStronger/live2d-moc3)

[GitHub - Konata09/Live2dOnWeb: Add Live2D widget in your website. Support Cubism 2, 3, 4](https://github.com/Konata09/Live2dOnWeb)