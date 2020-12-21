# hexo-theme-sakuraplus

- 示例站：[cungudafa.top](https://cungudafa.top)   

- 预览视频： [bilibili](https://www.bilibili.com/video/BV1C7411N762)  

- 源码地址：[hexo-theme-sakuraplus](https://gitee.com/cungudafa/hexo-theme-sakuraplus)

> Hexo主题Sakura由[Hojun大佬](https://www.hojun.cn/)修改自WordPress主题[Sakura](https://github.com/mashirozx/Sakura/)，感谢原作者[Mashiro](https://2heng.xin/)

由于sakura最近没有再更新了，我对之前的[魔改笔记](https://blog.csdn.net/cungudafa/category_9714183.html)进行了调整和整理，开源[hexo-theme-sakuraplus](https://gitee.com/cungudafa/hexo-theme-sakuraplus)版本。

## 特性

- 简单漂亮，文章内容美观易读
- 响应式设计，博客在桌面端、平板、手机等设备上均能很好的展现
- 首页轮播文章及自动切换 `Banner` 图片
- 背景壁纸切换（增加）
- 时间轴式的归档页
- 支持pjax刷新
- 友情链接页面 （增加了config可选设置提示语和本网站信息）
- 支持在首页的音乐播放和视频播放功能
- 支持文章置顶和文章打赏（增加了提示语）
- 支持 `MathJax`
- `TOC` 目录
- 缓加载动画
- 增加了相册页面（一个快速查找文章的接口，后面可能会美化）
- 增加了豆瓣读书、豆瓣电影页面
- 增加了[个性说说在线发布](https://artitalk.js.org)页面（增加了）
- 优化了文章底部作者信息不填写时显示为空的问题
- 优化了[Valine](https://valine.js.org/)评论模块（最新可定制表情等）
- 可设置复制文章内容时追加版权信息（可设置是否追加版权信息）
- 增加了 [DaoVoice](http://www.daovoice.io/)在线聊天功能
- 增加了可设置代码块相关（是否显示名称、是否可复制，是否可收缩，是否可折行）
- 增加了ICP备案信息显示（可选）
- 增加了标签云页面（如果需要更多功能，后面可补充）
- 增加了看板娘动画
- 增加了可设置首页音乐提示
- 增加了[不蒜子统计](http://busuanzi.ibruce.info/)、谷歌分析（`Google Analytics`）和文章字数统计等功能
- 优化了[自我介绍](https://www.bootcdn.cn/botui/)页面
- 增加了自定义logo的接口
- 增加了fancybox查看大图功能（可选）
- 增加了外链转知乎卡片形式
- 增加了谷歌分析和[百度分析](https://tongji.baidu.com/)
- 增加了[widgetpack星星评分](https://widgetpack.com)
- 多种背景和点击动画（可选设置）
- 优化css和js在config可配置
- 优化文章图片阴影显示和引用样式（阅读butter）
- 增加播放器隐藏选项
- 新增了分析页面（echarts图）
- 归档页新增[文章日历](https://echarts.apache.org/zh/option.html#calendar)（echarts图）
- 新增文章描述为空时，自动获取文章前50字
- 新增404页面
- 新增左侧悬浮框（回到顶部，打开左侧目录，跳转评论，黑夜模式，背景音乐播放`在config设置`）



## 下载

当你看到这里的时候，应该已经有一个自己的 [Hexo](https://hexo.io/zh-cn/) 博客了。如果还没有的话，不妨使用 Hexo 和 [Markdown](https://www.appinn.com/markdown/) 来写博客和文章。

点击 [这里](https://gitee.com/cungudafa/hexo-theme-sakuraplus/repository/archive/master.zip) 下载 `master` 分支的最新稳定版的代码，解压缩后，将 `hexo-theme-sakuraplus` 的文件夹复制到你 Hexo 的 `themes` 文件夹中即可。

当然你也可以在你的 `themes` 文件夹下使用 `Git clone` 命令来下载:

```bash
git clone https://gitee.com/cungudafa/hexo-theme-sakuraplus.git
```


## 配置

### 切换主题

修改 Hexo 根目录下的 `_config.yml` 的  `theme` 的值：`theme: hexo-theme-sakruaplus`

#### `_config.yml` 文件的其它修改建议:

- 请修改 `_config.yml` 的 `url` 的值为你的网站主 `URL`（如：`http://xxx.github.io`）。
- 如果你是中文用户，则建议修改 `language` 的值为 `zh-CN`。
- 建议修改permalink，可修改为[短链接](https://blog.csdn.net/cungudafa/article/details/104312892)。
安装插件：

	```bash
	npm install hexo-abbrlink --save
	```
	主配置文件修改和添加：
	
	```bash
	#permalink: :year/:month/:day/:title/ #主题默认文章链接配置
	permalink: post/:abbrlink.html #修改
	
	## 启用算法生成不重复文件编号，添加
	abbrlink:
		  alg: crc16   #算法： crc16(default) and crc32
		  rep: hex   #进制： dec(default) and hex: dec #输出进制：十进制和十六进制，默认为10进制。丨dec为十进制，hex为十六进制
	
	```

### 新建分类 categories 页

`categories` 页是用来展示所有分类的页面，如果在你的博客 `source` 目录下还没有 `categories/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "categories"
```

编辑你刚刚新建的页面文件 `/source/categories/index.md`，至少需要以下内容：

```yaml
---
title: 分类
date: 2020-04-21 00:00:00
type: "categories"
photos: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/banner/donate.jpg
comments: false
layout: "categories"
---
```

### 新建标签 tags 页

`tags` 页是用来展示所有标签的页面，如果在你的博客 `source` 目录下还没有 `tags/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "tags"
```

编辑你刚刚新建的页面文件 `/source/tags/index.md`，至少需要以下内容：

```yaml
---
title: tags
type: tags
date: 2020-02-13 20:24:16
photos: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/banner/donate.jpg
comments: false
layout: "tags"
---
```

### 新建关于我 about 页

`about` 页是用来展示**关于我和我的博客**信息的页面，如果在你的博客 `source` 目录下还没有 `about/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "about"
```

编辑你刚刚新建的页面文件 `/source/about/index.md`，至少需要以下内容：
>`Tips:` 这里用了[botui](https://www.bootcdn.cn/botui/)，还需要单独配置（/js/third-party/botui.js）

```yaml
---
title: about
date: 2020-2-12 22:14:36
keywords: 关于
description: 
layout: "about"
comments: false
photos: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/banner/about.jpg
---
<!-- https://www.bootcdn.cn/botui/ -->
<link href="https://cdn.bootcss.com/botui/0.3.9/botui-theme-default.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/botui/0.3.9/botui.min.css" rel="stylesheet">

{% raw %}
<!-- 因为vue和botui更新导至bug,现将对话移至js下的botui中配置 -->
<div class="entry-content">
  <div class="moe-cungudafa" style="text-align:center; font-size: 50px; margin-bottom: 20px;">姑的小屋</div>
  <div id="hello-cungudafa" class="popcontainer" style="min-height: 300px; padding: 2px 6px 4px; background-color: rgb(36, 200, 255); border-radius: 10px;">
    <center>
    <p>
    </p>
    <h4>
    与&nbsp;<ruby>
    cungduafa&nbsp;<rp>
    （</rp>
    <rt>
    真（ま）白（しろ）</rt>
    <rp>
    ）</rp>
    </ruby>
    对话中...</h4>
    <p>
    </p>
    </center>
    <bot-ui></botui>
  </div>
</div>
<script src="/js/botui.js"></script>
<script>
 bot_ui_ini()
</script>
{% endraw %}

```

### 新建留言板 comment 页（可选的）

`conmment` 页是用来展示**留言板**信息的页面，如果在你的博客 `source` 目录下还没有 `conmment/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "conmment"
```

编辑你刚刚新建的页面文件 `/source/contact/index.md`，至少需要以下内容：

```yaml
---
title: 留言板
type: comment
layout: "comment"
date: 2020-04-21 20:17:58
photos: https://gitee.com/cungudafa/source/raw/master/img/bg/hyo/3.jpg
---

<h2 align="center">有什么想说的?</h2>

<h2 align="center">有什么想问的?</h2>

```

> **注**：本留言板功能依赖于第三方评论系统，请**激活**你的评论系统才有效果。

### 新建友情链接 links 页（可选的）

`links` 页是用来展示**友情链接**信息的页面，如果在你的博客 `source` 目录下还没有 `links/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "links"
```

编辑你刚刚新建的页面文件 `/source/links/index.md`，至少需要以下内容：

```yaml
---
layout: links
title: links
date: 2020-02-13 23:11:06
keywords: 友人帐
description: 
comments: true
photos: https://cdn.jsdelivr.net/gh/cungudafa/img/images/pic01.jpg
links:
  - group: 多站快速门
    desc: 
    items:
    - url: https://blog.csdn.net/cungudafa
      img: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/custom/cungudafa.jpg
      name: CSDN
      desc: 我的技术栈
  - group: 友情链接
    desc: 
    items:
    - url: https://blog.csdn.net/cungudafa
      img: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/custom/cungudafa.jpg
      name: CSDN
      desc: 我的技术栈
    - name: 总站导航
      url: https://cungudafa.js.org/
      img: https://cungudafa.top/img/avatar.png
      desc: cungudafa.js.org
---
```
>`Tips：`注意前面的空格哦，很重要！

增加了友链要求提示项`tips`（支持html标签格式），在主题配置文件，`name、desc、url、img`可以直接写入你的网站信息。

```yaml
# 友链页信息,tips支持html语言
mylinkinfo:
  enable: true
  tips: '<p><strong>Tips:</strong>网站不要求https强制加锁，但头像图片尽量用安全链接哦！</p> <p style="font-size: 13px;color: #ff9999;"> 1. 请先 <font color=#ff1234 size=5>添加本站链接</font>，在本站留言，待站长审核通过即添加友链。<br>2. 拒绝广告站，技术博客优先~<br>3. 对于取消本站链接或死链，站长会定期移除。</p>'
  name: cungudafa
  desc: 一个学习记录者
  url: https://cungudafa.top
  img: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/custom/cungudafa.jpg
```




### 菜单导航配置

#### 配置基本菜单导航的名称、路径url和图标icon.

1.菜单导航名称可以是中文也可以是英文(如：`Index`或`主页`) 
2.图标icon 可以在[Font Awesome v4](http://www.fontawesome.com.cn/faicons/) 中查找   

```yaml
menus:
  首页: { path: /, fa: fa-fort-awesome faa-shake }
  归档: { path: /archives, fa: fa-archive faa-shake, submenus: { 
    统计: {path: /analytics/ , fa: fa-line-chart },
    标签: {path: /tags, fa: fa-tags }, 
    技术: {path: /categories/技术/, fa: fa-code }, 
    生活: {path: /categories/生活/, fa: fa-file-text-o } #, 
    #资源: {path: /categories/资源/, fa: fa-cloud-download }, 
    #随想: {path: /categories/随想/, fa: fa-commenting-o },
    #转载: {path: /categories/转载/, fa: fa-book }
  } }
  清单: { path: javascript:;, fa: fa-list-ul faa-vertical, submenus: { 
    说说: {path: /artitalk/, fa: fa-commenting-o fa-commenting }, 
    相册: {path: /photos/, fa: fa-photo },
    悦读: {path: /books/, fa: fa-book },
    追剧: {path: /movies/, fa: fa-film faa-vertical }
  } }
  留言板: { path: /comment/, fa: fa-pencil-square-o faa-tada }
  友人帐: { path: /links/, fa: fa-link faa-shake }
  赞赏: { path: /donate/, fa: fa-heart faa-pulse }
  关于: { path: /, fa: fa-leaf faa-wrench , submenus: { 
    我？: {path: /about/, fa: fa-meetup}, 
    主题: {path: /theme-sakura/, fa: iconfont icon-sakura },
    # Lab: {path: /lab/, fa: fa-cogs },
    # valine: {path: /valine/, fa: fa-cogs }
  } }
  项目: { path: /client/, fa: fa-android faa-vertical }
  RSS: { path: /atom.xml, fa: fa-rss faa-pulse }

#social  url, img PC端配置
social:
  gitee: {url: https://gitee.com/cungudafa, img: /img/social/gitee.png}
  github: {url: http://github.com/cungudafa, img: /img/social/github.png}
  csdn: {url: https://blog.csdn.net/cungudafa, img: /img/social/csdn.png}
  zhihu: {url: https://www.zhihu.com/people/cungudafa, img: /img/social/zhihu.png}
  #jianshu: {url: /https://www.jianshu.com/u/9b726a534140, img: /img/social/jianshu.png}
  # sina: {url: http://weibo.com/3829198906/info, img: /img/social/sina.png}
  bilibili: {url: https://space.bilibili.com/430247358, img: /img/social/bilibili.png}
  #wangyiyun: {url: https://music.163.com/#/user/home?id=337394988, img: /img/social/wangyiyun.png}
  wechat: {url: /#, qrcode: /img/custom/cungudafa.png, img: /img/social/wechat.png}

#social  url, img 移动端配置
msocial:
  github: {url: http://github.com/cungudafa, fa: fa-github, color: 333}
  weibo: {url: http://weibo.com/3829198906/info, fa: fa-weibo, color: dd4b39}
  #qq: {url: https://wpa.qq.com/msgrd?v=3&uin=2627039020&site=qq&menu=yes, fa: fa-qq, color: 25c6fe}
  csdn: {url: https://blog.csdn.net/cungudafa, fa: fa-star, color: ff9900}

```
>`Tips:`注意二级菜单和图标的写法

### 代码高亮

由于 Hexo 自带的代码高亮主题显示不好看，所以主题中使用到了 [hexo-prism-plugin](https://github.com/ele828/hexo-prism-plugin) 的 Hexo 插件来做代码高亮，安装命令如下：

```bash
npm i -S hexo-prism-plugin
```

然后，修改 Hexo 根目录下 `_config.yml` 文件中 `highlight.enable` 的值为 `false`，并新增 `prism` 插件相关的配置，主要配置如下：

```yaml
highlight:
  enable: false

prism_plugin:
  mode: 'preprocess'    # realtime/preprocess
  theme: 'tomorrow'
  line_number: false    # default false
  custom_css:
```

### 搜索

本主题中还使用到了 [hexo-generator-json-content](https://www.npmjs.com/package/hexo-generator-json-content) 的 Hexo 插件来做内容搜索，安装命令如下：

```bash
npm install hexo-generator-json-content --save
```

### 文章字数统计插件（建议安装）

如果你想要在文章中显示文章字数、阅读时长信息，可以安装 [hexo-wordcount](https://github.com/willin/hexo-wordcount)插件。

安装命令如下：

```bash
npm i --save hexo-wordcount
```


### 添加emoji表情支持（可选的）

本主题新增了对`emoji`表情的支持，使用到了 [hexo-filter-github-emojis](https://npm.taobao.org/package/hexo-filter-github-emojis) 的 Hexo 插件来支持 `emoji`表情的生成，把对应的`markdown emoji`语法（`::`,例如：`:smile:`）转变成会跳跃的`emoji`表情，安装命令如下：

```bash
npm install hexo-filter-github-emojis --save
```

在 Hexo **根目录**下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
githubEmojis:
  enable: true
  className: github-emoji
  inject: true
  styles:
  customEmojis:
```

### 添加 RSS 订阅支持（可选的）

本主题中还使用到了 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) 的 Hexo 插件来做 `RSS`，安装命令如下：

```bash
npm install hexo-generator-feed --save
```

在 Hexo **根目录**下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
```

执行 `hexo clean && hexo g` 重新生成博客文件，然后在 `public` 文件夹中即可看到 `atom.xml` 文件，说明你已经安装成功了。

### 添加 [DaoVoice](http://www.daovoice.io/) 在线聊天功能（可选的）

前往 [DaoVoice](http://www.daovoice.io/) 官网注册并且获取 `app_id`，并将 `app_id` 填入主题的 `_config.yml` 文件中。
```yaml
# Online contact ##在线聊天 https://www.daocloud.io/
daovoice: 
  enable: false
  app_id: f6b2a0b1 ##这里替换成你DaoVoice上的appid
```

### 在线发布说说页面
根据官网教程配置[artitalk.js.org](https://artitalk.js.org)，本主题融合了自定义配置背景的功能，具体可以参考[个性定制教程](https://cungudafa.blog.csdn.net/article/details/106224223)。

```yaml
# 动态说说页面
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://artitalk.js.org
artitalk:
  appID: CiSXX5nyVSt0RIztkC1oLL9P-MdYXbMMI
  appKEY: vrfkqkuHou88MuRKfF3OeExc
  per: 7 #每页显示说说的数量
  username: cungudafa # Leancloud中设置的用户名
  placeholder1: 没有密码，不能评论！ # 密码框提示语
  lazy: 1 # 加载动画1:加载,0:取消加载
  img: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/custom/cungudafa.jpg #用户默认头像
  bgimg: https://gitee.com/cungudafa/source/raw/master/img/gif/Sitich/Sitich16.gif # 输入框小动画
  # 极简黑白主题minimalist、渐变主题gradient、壁纸wallpaper 
  # 参考 https://blog.csdn.net/cungudafa/article/details/106224223
  style: 
    enable: true #个性定制
    wallpaper: https://ae01.alicdn.com/kf/H21b5f6b8496141a1979a33666e1074d9x.jpg #https://bing.rthe.net/wallpaper  # 壁纸
    color: white   #字体颜色
    linear_gradient: rgba(255, 165, 150, 0.5) 5%, rgba(0, 228, 255, 0.35) 95% # 渐变规律颜色+百分比 rgb(109, 208, 242) 15%, rgb(245, 154, 190) 85%
    border_right_color: '#FFC0BE' #三角颜色
    animation: false #true打开动画渐变渲染(壁纸和动画渐变渲染建议只开一个)
```
>根据你的爱好修改字体颜色，背景，动画等。
### 修改页脚

页脚信息可能需要做定制化修改，而且它不便于做成配置信息，所以可能需要你自己去再修改和加工。修改的地方在主题文件的 `/layout/_partial/footer.ejs` 文件中，包括站点、使用的主题、访问量等。

### 修改打赏的二维码图片
在主题config里面可以修改路径，你可以替换成你的的微信和支付宝的打赏二维码图片。
```yaml
donate:
  title: 谢谢饲主了喵~
  message: 我能想到最浪漫的事，就是我喝奶茶你付钱~
  paypal: #https://www.paypal.me/hojuncn
  alipay: /img/custom/donate/AliPay.jpg #大图
  alipayQR: /img/custom/donate/AliPayQR.jpg
  wechat: /img/custom/donate/WeChatPay.png #大图
  wechatQR: /img/custom/donate/WeChanQR.jpg
  wechatSQ: /img/custom/donate/WeChanSQ.jpg
```

`donates` 页是单独的打赏页面（可选），如果在你的博客 `source` 目录下还没有 `donate/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "donate"
```

编辑你刚刚新建的页面文件 `/source/donate/index.md`，至少需要以下内容：

```yaml
---
layout: donate
title: donate
date: 2020-02-13 23:13:05
keywords: 谢谢饲主了喵~
description: 
comments: false
photos: https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/banner/donate.jpg
---
```

在主题文件的 `themes\sakura\layout\donate.ejs` 文件中，可以增加更多的打赏方式。


### 配置视频、音乐播放器（可选的）

要支持音乐播放，在主题的 `_config.yml` 配置文件中激活music配置即可：

```yaml
# 视频地址为https://cdn.jsdelivr.net/gh/honjun/hojun@1.2/Unbroken.mp4，配置如下
movies:
  enable: false
  url: https://cdn.jsdelivr.net/gh/honjun/hojun@1.2
  # 多个视频用逗号隔开，随机获取。支持的格式目前已知MP4,Flv。其他的可以试下，不保证有用
  name: Unbroken.mp4

aplayer:  ##媒体播放引擎，移动端会禁止自动播放
  enable: false
  id: 2660651585
  server: netease
  type: playlist
  fixed: true ##开启吸底模式，固定到底部
  autoplay: false ##音频自动播放
  loop: all  ##音频循环播放, 可选值: 'all', 'one', 'none'
  order: random ##音频循环顺序, 可选值: 'list', 'random'
  preload: auto ##预加载，可选值: 'none', 'metadata', 'auto'
  volume: 0.7 ##默认音量，请注意播放器会记忆用户设置，用户手动设置音量后默认音量即失效
  mutex: true ##互斥，阻止多个播放器同时播放
```
### 评论
根据官网的配置教程，严格配置：[valine.js.org](https://valine.js.org)
```yaml
# Valine ##极简留言功能
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://valine.js.org
valine:
  enable: true ##打开valine评论功能true
  appId: cBzr9TtJ0xY0s6f3H8bpmB3M-gzGzoHsz
  appKey: 3opMyv2Vyx3mHa0IWitRGSoi
  notify: false #true自带评论mail回复提醒，第三方邮件提醒就设置为false
  verify: true #验证码
  visitor: false #统计阅读量
  comment_count: true #统计评论数
  avatar: monsterid # Gravatar style : mp/identicon/monsterid/wavatar/retro/hide
  guest_info: nick #,mail,link # custom comment header
  pageSize: 10
  palaceholder: 你是我一生只会遇见一次的惊喜 # Comment Box placeholder
  requiredFields: ['nick','mail'] #设置必填项
  background: https://gitee.com/cungudafa/source/raw/master/img/gif/Sitich/Sitich2.gif
```

### 文章分析

日历页面（在归档页面下方，可选择性开启），文章分析页面（选择性开启，若关闭，请在菜单栏中删除即可）

```yaml
# Whether to display post-calender in the `archive` page
# 设置在归档页面achive中是否显示'文章日历'控件
postCalendar: true 
```

## 文章 Front-matter 介绍

### Front-matter 选项详解

`Front-matter` 选项中的所有内容均为**非必填**的。但我仍然建议至少填写 `title` 和 `date` 的值。

| 配置选项   | 默认值                      | 描述                                                         |
| ---------- | --------------------------- | ------------------------------------------------------------ |
| title      | `Markdown` 的文件标题        | 文章标题，强烈建议填写此选项                                 |
| date       | 文件创建时的日期时间          | 发布时间，强烈建议填写此选项，且最好保证全局唯一             |
| author     | 根 `_config.yml` 中的 `author` | 文章作者 ，也可以修改                                                    |
authorLink|无| 链接
avatar|无| 头像
authorAbout| 无| 简介（非文章页设置为一言）
| photos        | `bg` 中的某个值   | 文章特征图，推荐使用图床(腾讯云、七牛云、又拍云等)来做图片的路径.如: `http://xxx.com/xxx.jpg` |
| top        | `true`                      | 推荐文章（文章是否置顶），如果 `top` 值为 `true`，则会作为首页推荐文章 |
| mathjax    | `false`                     | 是否开启数学公式支持 ，本文章是否开启 `mathjax`，且需要在主题的 `_config.yml` 文件中也需要开启才行 |
| categories | 无                          | 文章分类，本主题的分类表示宏观上大的分类，只建议一篇文章一个分类 |
| tags       | 无                          | 文章标签，一篇文章可以多个标签                              |
description| 默认取文章前40个字 | 一句话描述摘要
| keywords   | 文章标题                     | 文章关键字，SEO 时需要                              |
| reprintPolicy | cc_by                    | 文章转载规则， 可以是 cc_by, cc_by_nd, cc_by_sa, cc_by_nc, cc_by_nc_nd, cc_by_nc_sa, cc0, noreprint 或 pay 中的一个 |

> **注意**:
> 1. 如果 `photos` 属性不填写的话，首页会在主题配置文件的bg下，随机选择一张图片，同时进入文章内部不显示顶部图像。
> 2. `date` 的值尽量保证每篇文章是唯一的。
> 3. 建议文章标签和文章分类填写上。
> 4. 您可以在文章md文件的 front-matter 中指定 reprintPolicy 来给单个文章配置转载规则

以下为文章的 `Front-matter` 示例。

### 最简示例

```yaml
---
title: 主题介绍
date: 2020-05-20 09:25:00
---
```

### 最全示例

```yaml
---
title: 阿里云部署hexo完整教程
author: cungudafa
authorLink: 'https://cungudafa.top'
avatar: 'https://cdn.jsdelivr.net/gh/cungudafa/cdn/img/custom/cungudafa.jpg'
authorAbout: 姑，一个学习记录者
tags:
  - nginx
  - https
  - SSL
categories: 技术
comment: false
photos: https://test.png
description: 描述一句话摘要
abbrlink: ccbf
date: 2020-03-02 13:59:10
---
```
### 文章统计
在本主题的 `_config.yml` 中可以修改部分自定义信息，有以下几个部分：

代码块是否可复制、图片大图查看功能、知乎外链样式、文章字数统计等。

```yaml
# Whether to activate the copyright information of the blog and author when copying the post content.
# minCharNumber: Approve copyright information by copying at least how many characters.
# 是否激活复制文章时追加博客和作者的版权信息.
copyright:
  enable: false
  minCharNumber: 120 # 至少复制多少个字符就追加版权信息.
  description: 本文章著作权归作者所有，任何形式的转载都请注明出处。

# 代码块相关
code:
  lang: true # 代码块是否显示名称
  copy: true # 代码块是否可复制
  shrink: true # 代码块是否可以收缩
  break: false # 代码是否折行

# 查看大图
fancybox:
  enable: true

# 外链转知乎卡片形式 
linkcard:
  enable: true 

# 是否开启字数统计 (可以在formatton设置no_word_count: true)
#不需要使用，直接设置值为false，或注释掉
word_count: true

# busuanzi(http://busuanzi.ibruce.info/) website statistics
# 不蒜子(http://busuanzi.ibruce.info/) 网站统计
busuanziStatistics:
  enable: true
  totalTraffic: true # 总访问量
  totalNumberOfvisitors: true # 总人次
```
### 五星评分
需要在 [widgetpack.com](https://widgetpack.com)注册申请，
```yaml
# widgetpack 星星评分
# To get your ID visit https://widgetpack.com
widgetpack:
  enable: true #false
  id: 23889 #<app_id>
  color: fc6423
```
### 天气
需要在官网[weatherdt.com](http://weatherdt.com/)注册配置，具体[天气教程](https://blog.csdn.net/cungudafa/article/details/104312892)。

```yaml
# 天气显示 http://weatherdt.com/
weather:
  enable: false
  app_id: yqRrzxwtvs ##这里替换成你的appid
```
>更多：
>1. 豆瓣页面：[豆瓣读书和电影](https://cungudafa.blog.csdn.net/article/details/105773983)
>2. 访问者地图：[访问者地图](https://cungudafa.blog.csdn.net/article/details/105925710)
### 文章分析
包括[百度分析](https://tongji.baidu.com/)和谷歌分析，都需要去注册申请。（可选操作）
```yaml
# Add google analytics configuration
# 添加 Google Analytics 配置
googleAnalytics:
  enable: false
  id:

# Add baidu analytics configuration
# 添加 baidu Analytics 配置 https://tongji.baidu.com/
baiduAnalytics:
  enable: true # 百度分析
  id: b05d6287bdeab72f3886bffe3cff054a
```

## 效果预览

示例站：[cungudafa.top](https://cungudafa.top)

视频：[bilibili](https://www.bilibili.com/video/BV1C7411N762)


## 自定制修改
背景和一些常用的优化，在主题配置文件中都以可选配置了（防止动画冲突，建议背景和点击效果只打开一个），
如果更多有新的样式，可以留言或者pr，酌情添加。
```yaml
# 建议一项只开一个
js:
  hititle: false #浏览器搞笑标题
  KeyBlock: false #按键屏蔽,不能F12调试和查看源代码
  bg:
    sakura_petals: false  #仅在首页花瓣飘落（大内存）
    snow_petals: false # 雪花飘落（大内存）
    piao: false #飘动彩带
    live2d_widget: false #看板娘 需jq（大内存）
    bgchange: true # 是否开启壁纸切换
  clink:
    word: false #点击出现字效
    love: false #点击出现爱心
    cursor: false # 星星跟随坠落(大内存)
```
>给出一系列魔改的笔记：
>1. [logo](https://blog.csdn.net/cungudafa/article/details/104330959)
>2. [花里胡哨教程](https://blog.csdn.net/cungudafa/article/details/104295156)
>3. [今日诗词、一言接口](https://blog.csdn.net/cungudafa/article/details/104651901)


## 参与贡献
- @[cungudafa](https://gitee.com/cungudafa)




## 更新日志
- 6.9

补充粘贴复制（[教程](https://blog.csdn.net/cungudafa/article/details/106647255)）

- 6.2

修复代码块样式（删掉`npm uninstall hexo-prism-plugin`插件）
新增左侧悬浮框（回到顶部，打开左侧目录`小屏幕生效，否则只显示顶部目录`，跳转评论，黑夜模式，背景音乐播放`在config设置bg_music`）

- 5.28

修复了[css](https://cdn.jsdelivr.net/gh/cungudafa/cdn/css/style.css )（首页bug）
修改了post文章引用样式（以前的不明显）
增加了post文章图片样式（增加阴影）、段落h12345增加阿拉伯编号
添加了tags页面多种词云效果

- 5.25 

修复了没有设置文章时首页显示404图片，（bg中随机选择）
新增了分析页面（echarts图），来源于drew整理matery主题的效果
归档页新增文章日历（echarts图）
新增标题下显示浏览数
新增自动获取前50字为文章描述

- 5.23 

计划修改文章页面展现效果（对代码块和表格图片展现更加优化）

- 5.22 

重铸


