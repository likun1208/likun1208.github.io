---
title: hexo+reveal实现slide
date: 2021-03-02 18:43:38
categories: slides
tags: 博客配置
slidehtml: true
---

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

7. 如果写latex公式，就放到`<section></section>`中间，例如：

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

8. 在搞了各种配置之后公式如果还没渲染，就等一等，可能和缓存有关。

9. 对于需要制作成slide的markdown文件，只需要在原先写标题日期分类和tags等内容的那个地方增加一个字段：`slidehtml: true`，这个也可以写进`hexo new`的模板，这样方便一些。markdown文件中要有一行`<!-- Slide Start -->  `，整个文件内容会以此分为两部分，在这一行前面的，就会展示在博客内容中，而这一行后面的，则会出现在对应的slide中。例如本篇博客的内容就到此结束了，而如果在网址后面添加`slide.html`，则会看到我复制别人的slide内容。

## Reference

1. https://github.com/jackey8616/hexo-generator-slidehtml
2. https://revealjs.com/math/

<!-- Slide Start -->  

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

This page should be slide first.  

`$$x+3=x_3$$`

Not in post.  



---

## Quick Start

--

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

--

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

--

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

--

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)
