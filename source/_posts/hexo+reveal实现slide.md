---
title: hexo+reveal实现slide
author: Kun
date: 2021-03-02 18:43:38
categories: slides
tags: 博客配置
slidehtml: true
---
# hexo+reveal

## 安装和配置

今天终于搞成了用`reveal.js`在博客里显示ppt的效果。感谢做模块的大佬，以至于安装过程还挺简单的。步骤如下：

1. 安装：命令行进入博客目录，输入`npm i hexo-generator-slidehtml`

2. 安装好以后，一个按格式（格式见后文）写好的markdown文件就会被渲染成slide格式的html网页了，需要访问的时候，在博客的链接后面加`slide.html`即可，比如某个博客的链接是`http://www.clo5de.info/hexo-generator-slidehtml/2019/04/03/nonTitleMerge/`，那么它对应的slide链接就是`http://www.clo5de.info/hexo-generator-slidehtml/2019/04/03/nonTitleMerge/slide.html`。

3. 但是这样安装好的没法渲染公式，因此要进入`\blog\node_modules\hexo-generator-slidehtml\layout`目录，打开`post-slide.ejs`文件，添加`<script src="https://cdn.bootcss.com/reveal.js/3.4.1/plugin/math/math.js"></script>`（和其他script放一起就行），并在`Reveal.initialize` 代码块中添加一行`plugins: [ RevealMath ],`注意这一行最后有个逗号不要忘了。

4. 关于math的设置也还有其他可选项，可以参考[官网](https://revealjs.com/math/)

5. 此外我在官网还下载了`reveal.js`的完整内容，并把其中的`plugin`文件夹整个复制到了`\blog\node_modules\hexo-generator-slidehtml\layout`目录下；而刚才那个`post-slide.ejs`的`Reveal.initialize` 代码块中的`dependencies`也增加了以下两行：

   `{ src: 'https://cdn.bootcss.com/reveal.js/3.4.1/plugin/math/math.esm.js' },`

   `{ src: 'https://cdn.bootcss.com/reveal.js/3.4.1/plugin/math/math.js' },`

   不过这一步的操作按理说是不需要的，只是我一开始怎么也没法刷新出公式，猜测可能和网页缓存等有关。

6. 按官网的说法，如果要写markdown行内公式，就需要把内容放到如下代码所示的符号中间：

   ```
   `$$公式内容$$`
   ```

   > If you want to include math inside of a presentation written in Markdown you need to wrap the formula in backticks. This prevents syntax conflicts between LaTeX and Markdown

7. 不过根据我自己的使用，markdown语法的公式就可以正常显示了，不需要按上面的格式来写。

8. 如果写latex公式，就放到`<section></section>`中间，例如：

   ```
   <section>
     <h2>The Lorenz Equations</h2>
     \[\begin{aligned}
     \dot{x} &amp; = \sigma(y-x) \\
     \dot{y} &amp; = \rho x - y - xz \\
     \dot{z} &amp; = -\beta z + xy
     \end{aligned} \]
   </section>
   ```

   这个公式的展示效果在官网上可以看到。

9. 在搞了各种配置之后公式如果还没渲染，就等一等，可能和缓存有关。

10. 对于需要制作成slide的markdown文件，只需要在原先写标题日期分类和tags等内容的那个地方增加一个字段：`slidehtml: true`，这个也可以写进`hexo new`的模板，这样方便一些。markdown文件中要有一行`<!-- #Slide Start# -->  `（去掉#，这里加#是为了避免接下来直接渲染slide），整个文件内容会以此分为两部分，在这一行前面的，就会展示在博客内容中，而这一行后面的，则会出现在对应的slide中。例如本篇博客的内容就到此结束了，而如果在网址后面添加`slide.html`，则会看到我复制别人的slide内容。

## Reference

1. https://github.com/jackey8616/hexo-generator-slidehtml
2. https://revealjs.com/math/
3. https://www.lfhacks.com/assets/revealjs.html
4. http://vishalgupta.me/md-slides/

<!-- Slide Start --> 

在`<!--slide start -->`下一行开始的内容就是slide的第一页，slide本身的标题会自动显示出来，其他信息例如作者时间等要自己输入。

如果不希望自动出现标题，就去修改`post-slide.ejs`文件。

---

## 翻页

`reveal.js`在markdown中的翻页方式和本身的不太一样。

原本是使用section标签来控制横向和纵向翻页，而在markdown文本中则使用`---`表示横向翻页。使用`--`表示纵向翻页。

--

例如这就是一个纵向页面。

---

## 片段

有时候不希望页面内容一次性全部显示

<span class="fragment">需要逐条目,</span> 
<span class="fragment">逐项</span>
<span class="fragment">地出现</span>

<p class="fragment">只要为页面元素添加一个class="fragment"</p>

<p class="fragment">就可以做到</p>

<pre class = "fragment"><code data-trim data-noescape>
    &lt;p class="fragment">需要逐条目,&lt;/p>
	&lt;p class="fragment">逐项&lt;/p>
	&lt;p class="fragment">地出现&lt;/p>
</code></pre>


--

这里先插入一下html常用标签的介绍：

**行标签：**

<span style="font-size:25px">a标签：主要用来链接一个其他的网页；</span>

<span style="font-size:25px">span标签：主要用来对行内的文字进行一些样式以及其他的操作；</span>

<span style="font-size:25px">em标签：一般用来对文字进行强调，使用斜体体现出来；</span>

<span style="font-size:25px">strong标签：一般用来对文字进行强调，使用加粗字体体现出来；</span>

<span style="font-size:25px">img标签：图片引用标签，其中 src属性中写入图片的地址；</span>

<span style="font-size:25px">var标签：JavaScript中命名变量的标签。</span>

--

**块标签：**

<span style="font-size:25px">p标签：段落标签，段落文字使用，默认格式：段尾进行换行；</span>

<span style="font-size:25px">div标签：划分块的主要使用标签；</span>

<span style="font-size:25px">ul标签：无序列表的主标签，后面的标号为圆点（黑色）；</span>

<span style="font-size:25px">ol标签：有序列表的主标签，后面一般跟有序的1,2,3,4,5...；</span>

<span style="font-size:25px">li标签：列表中的主体使用标签</span>

<span style="font-size:25px">dl标签：自定义标签的主标签；</span><span style="font-size:25px">dt标签：自定义标签的表头；</span>

<span style="font-size:25px">dd标签：自定义标签的表头的解释（描述）信息；</span>

<span style="font-size:25px">h1~h6：6级标题标签、字体的大小依次变小。</span>

--

此外还有其他标签，比如pre标签可以放代码块等。代码块想要语法高亮还需要在pre标签里再添加`<code data-trim data-noescape></code>`

每个标签也有各自可以设置的东西，例如span标签可以通过`style="font-size:25px"`来设置字体大小等。

--

### 片段样式

还有更多样式，只需要在class后面添加样式名称：

<p class="fragment grow">&lt;p class="fragment grow">增大&lt;/p></p>

<p class="fragment shrink">&lt;p class="fragment shrink">缩小&lt;/p></p>

<p class="fragment fade-out">&lt;p class="fragment fade-out">阅后消失&lt;/p></p>

<p class="fragment fade-up">&lt;p class="fragment fade-up">向上出现&lt;/p></p>

<p class="fragment fade-right">&lt;p class="fragment fade-right">向右出现&lt;/p></p>

<p class="fragment current-visible">&lt;p class="fragment current-visible">仅显示当前条目&lt;/p></p>

--

<p class="fragment highlight-current-blue">&lt;p class="highlight-current-blue"&gt;仅当前条目为蓝色&lt;/p&gt;</p>

--

### 片段样式组合

将样式嵌套起来, 它们就依次出现，消失...

利用data-fragment-index属性可以决定每个片段的出现顺序：

1. <p class="fragment" data-fragment-index="3">&lt;p class="fragment" data-fragment-index="3">再次&lt;/p></p>

2. <p class="fragment" data-fragment-index="2">&lt;p class="fragment" data-fragment-index="2">其次&lt;/p></p>

3. <p class="fragment" data-fragment-index="1">&lt;p class="fragment" data-fragment-index="1">首先&lt;/p></p>

---

## 页面切换方式

利用data-transition定义&lt;section>切换方式

利用data-transition-speed属性定义切换速度

但是在markdown中并没有section，所以要用`<!-- .slide: data-transition="concave" -->`这样的形式来定义切换方式和切换速度

--

<!-- .slide: data-transition="none" -->

例如这是none的切换方式

--

<!-- .slide: data-transition="fade" -->

例如这是fade的切换方式

--

<!-- .slide: data-transition="slide" -->

例如这是slide的切换方式

--

<!-- .slide: data-transition="convex" -->

例如这是convex的切换方式

--

<!-- .slide: data-transition="concave" -->

例如这是concave的切换方式

--

<!-- .slide: data-transition="zoom" -->

例如这是zoom的切换方式，zoom似乎对横向的有效果，纵向切换的时候不太能看出来

--

<!-- .slide: data-transition-speed="fast" -->

例如这是快速的切换速度

--

<!-- .slide: data-transition-speed="slow" -->

例如这是慢速的切换速度

---

## 页面锚点(绝对引用)

<a href="#/2/1">&lt;a href="#/2/1">点击到达第3页的第2个子页面&lt;/a></a>

<a href="#/some">&lt;a href="#/some">点击到达id是some的&lt;section>&lt;/a></a>

在我这里不知道为啥所有序号都得-1，比如第一页的第一个子页面对应的是0/0；但是网上看别人的例子就不是这样的。

---

<!--.slide: data-transition="zoom"-->

## 页面锚点(相对引用)

<a href="#" class="navigate-left">&lt;a href="#" class="navigate-left">点击向左&lt;/a></a>

<a href="#" class="navigate-right">&lt;a href="#" class="navigate-right">点击向右&lt;/a></a>

<a href="#" class="navigate-up">&lt;a href="#" class="navigate-up">点击向上&lt;/a></a>

<a href="#" class="navigate-down">&lt;a href="#" class="navigate-down">点击向下&lt;/a></a>

<a href="#" class="navigate-prev">&lt;a href="#" class="navigate-prev">点击至上一页&lt;/a></a>

<a href="#" class="navigate-next">&lt;a href="#" class="navigate-next">点击至下一页&lt;/a></a>

---

## 快捷键

- 全屏 f , 退出全屏 Esc
- 上一页 p, 下一页 n/空格
- 首页 Home, 末页 End
- 缩略图 Esc 或 o
- 黑屏 b
- 演讲提示模式 s
- vi导航键: h, j, k, l
- 帮助页面: ?

---

## 测试公式

行内公式：$x+3=x_3$

latex公式块，注意用于对齐的`&`符号要写成`&amp;`，用于换行的两个斜杠`\\`要写成三个斜杠`\\\`，总之是要转义。
$$
\begin{aligned}
  \dot{x} &amp; = \sigma(y-x) \\\
  \dot{y} &amp; = \rho x - y - xz \\\
  \dot{z} &amp; = -\beta z + xy \\\
\end{aligned}
$$

---

## 主题说明

进入`post-slide.ejs`可以修改主题，有如下几种主题：

1. black：黑色背景，白色文字，蓝色链接
2. white：白色背景，黑色文字，蓝色链接
3. league：灰色背景，白色文字，蓝色链接
4. beige：浅褐色背景，深色文字，棕色链接
5. sky：蓝色背景，灰色文字（我看着就是黑色），蓝色链接（比背景深一些的蓝色）

--

1. night：黑色背景，白色文字，橘黄色链接
2. serif：卡布奇诺背景，灰色文字，棕色链接
3. simple：白色背景，黑色文字，蓝色链接
4. solarized：奶油色背景，深绿色文字，蓝色链接
5. blood：深色背景，白色文字，红色链接
6. moon：深蓝色背景，浅灰色文字，蓝色链接

---

<h2 style="color:darkorange">高级用法</h2>

---

`post-slide.ejs`文件中的`Reveal.initialize`代码块涉及到整体环境的初始化，下面列出常用的参数和含义:

--

冒号后面表示缺省值

- controls: true  在每页右下角显示浏览方向按钮
- progress: true  在页面底部显示浏览进度条
- slideNumber: false  当前页码
- history: false  将每页记slide录在浏览记录里
- keyboard: true  启用键盘快捷键
- overview: true  启用缩略图模式(按o/Esc键)
- center: true  垂直方向居中(false:顶部)

--

- touch: true  支持触摸屏
- loop: false  循环播放
- rtl: false  文字显示和播放顺序改为从右到左(right to left)
- shuffle: false  随机加载下一页
- fragments: true  全局关闭片段功能
- embedded: false  表示当前幻灯片嵌在另一个网页中
- help: true  按问号(?)键显示帮助信息

--

- showNotes: false  演讲者笔记对听众可见
- autoSlide: 0  等待n毫秒后自动前进到下一页, 0表示禁用
- autoSlideStoppable: true  允许禁用自动播放
- autoSlideMethod: Reveal.navigateNext  自动播放所用
- mouseWheel: false  支持鼠标滚轮浏览
- hideAddressBar: true  在移动设备上自动隐藏地址栏
- previewLinks: false  将超链接显示在一个浮层里

--

- transition: 'default'  全局设置页面切换方式
- transitionSpeed: 'default'  全局设置页面切换速度
- backgroundTransition: 'default'  全局设置背景切换方式
- viewDistance: 3  距离当前页的可见范围
- parallaxBackgroundImage: ''  视差背景图地址
- parallaxBackgroundSize: ''  视差背景图尺寸
- parallaxBackgroundHorizontal: null  背景水平移动速度
- parallaxBackgroundVertical: null  背景垂直移动速度

---

## 参考

https://www.lfhacks.com/assets/revealjs.html

http://vishalgupta.me/md-slides/