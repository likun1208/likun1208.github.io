---
title: Echarts的配置
date: 2020-10-31 20:11:23
tags: 教程
categories: 博客配置
---

今天发现了一个可以用在markdown的前端画图js，[ECharts](https://echarts.apache.org/zh/tutorial.html#5%20%E5%88%86%E9%92%9F%E4%B8%8A%E6%89%8B%20ECharts)。在hexo的next主题中使用它需要进行一些配置。

<!--more-->

首先需要说明的是，hexo的网页渲染方式有ejs和swig两种。

对于ejs方式的主题，直接在主题目录下的`layout\_partial\head.ejs`中加入：

```js
<script src="http://echarts.baidu.com/dist/echarts.common.min.js"></script>
```

在博客站点文件的命令行中执行`npm install hexo-tag-echarts --save`即可。

而对于swig方式的主题要复杂一些。

1. 下载ECharts的js文件：[下载链接](https://echarts.apache.org/zh/download.html)

2. 然后在`next\layout\`文件夹下，找到`_layout.swig`文件，并用文本查看器打开，在下面代码：

   ```js
   <main id="main" class="main">
     <div class="main-inner">
       <div class="content-wrap">
         <div id="content" class="content">
           {% block content %}{% endblock %}
         </div>
         {% include '_third-party/duoshuo-hot-articles.swig' %}
         {% include '_partials/comments.swig' %}
       </div>
       {% if theme.sidebar.display !== 'remove' %}
         {% block sidebar %}{% endblock %}
       {% endif %}
     </div>
   </main>
   ```

   的**前面**添加：

   ```js
   <!-- echarts -->
   <script type="text/javascript" src="/js/src/echarts.common.min.js"></script>
   ```

   保存退出。

3. 在博客站点文件的命令行中执行`npm install hexo-tag-echarts --save`。

到此为止就可以了，新建一个markdown文件，把下列代码放进去就可以看到本页最下面的echarts示例了。（直接复制粘贴，**不要**放代码块里）

```
{% echarts 400 '85%' %}
{
    title: {
        text: "某站点用户访问来源",
        subtext: "ECharts示例",
        x: "center"
    },
    tooltip: {
        trigger: "item",
        formatter: "{a} <br/>{b} : {c} ({d}%)"
    },
    legend: {
        orient: "vertical",
        x: "left",
        data: ["直接访问", "邮件营销", "联盟广告", "视频广告", "搜索引擎"]
    },
    toolbox: {
        show: true,
        feature: {
            mark: {
                show: true
            },
            dataView: {
                show: true,
                readOnly: true
            },
            restore: {
                show: true
            },
            saveAsImage: {
                show: true
            }
        }
    },
    calculable: true,
    series: [
        {
            name: "访问来源",
            type: "pie",
            radius: "55%",
            center: ["50%", "60%"],
            data: [
                {
                    value: 335,
                    name: "直接访问"
                },
                {
                    value: 310,
                    name: "邮件营销"
                },
                {
                    value: 234,
                    name: "联盟广告"
                },
                {
                    value: 135,
                    name: "视频广告"
                },
                {
                    value: 1548,
                    name: "搜索引擎"
                }
            ]
        }
    ]
}
{% endecharts %}
```



{% echarts 400 '85%' %}
{
    title: {
        text: "某站点用户访问来源",
        subtext: "ECharts示例",
        x: "center"
    },
    tooltip: {
        trigger: "item",
        formatter: "{a} <br/>{b} : {c} ({d}%)"
    },
    legend: {
        orient: "vertical",
        x: "left",
        data: ["直接访问", "邮件营销", "联盟广告", "视频广告", "搜索引擎"]
    },
    toolbox: {
        show: true,
        feature: {
            mark: {
                show: true
            },
            dataView: {
                show: true,
                readOnly: true
            },
            restore: {
                show: true
            },
            saveAsImage: {
                show: true
            }
        }
    },
    calculable: true,
    series: [
        {
            name: "访问来源",
            type: "pie",
            radius: "55%",
            center: ["50%", "60%"],
            data: [
                {
                    value: 335,
                    name: "直接访问"
                },
                {
                    value: 310,
                    name: "邮件营销"
                },
                {
                    value: 234,
                    name: "联盟广告"
                },
                {
                    value: 135,
                    name: "视频广告"
                },
                {
                    value: 1548,
                    name: "搜索引擎"
                }
            ]
        }
    ]
}
{% endecharts %}