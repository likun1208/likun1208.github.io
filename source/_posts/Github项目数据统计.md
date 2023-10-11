---
title: Github项目数据统计
date: 2021-04-30 10:06:21
tags: 
  - 学习笔记
  - python
  - Echarts
  - Github
  - 统计
categories: 无分类项
description: 使用Github接口获取相关数据并进行分析统计
---
# Github项目数据统计

​      由于需要统计Dapp项目的发展历程数据，在查资料时看到了[以太坊 Dapp 调研](https://zhuanlan.zhihu.com/p/32759663)，这份统计只包含了2017年12月20日及之前的数据，而现在已经是2021年了，因此我模仿这个进行了新的统计。

<!--more-->

## 数据获取

经过一系列学习，我终于掌握了GitHub api的基础使用。简单来说，我们可以通过访问api接口进行查询并获取数据。通过关键字dapp进行查询的api是`https://api.github.com/search/repositories?q=dapp`，用浏览器直接打开这个链接可以看到如下图的内容：

![image-20210430103142883](https://i.loli.net/2021/04/30/nXQsavbDH98PJxc.png)

第一行的`total_count`是所有结果的总数，接下来有30个`items`，每一个都是对应的项目，点开第一个可以看到其数据格式为：

![image-20210430103308097](https://i.loli.net/2021/04/30/qNL3xuYcrlByVbW.png)

图中显示的内容并不完整，还有很多信息比如stars数量等都可以直接获取。

但是总共有19930个项目，却只获取到30个，这显然是不够的，想获取更多项目就需要修改api的内容，改成`https://api.github.com/search/repositories?q=dapp&page=1&per_page=100`，修改后就可以获取到100条项目信息了，这是每页项目数的上限，将api中的`page=1`改成`page=2`即可得到新的100条信息，然而由于api限制，通过这样的方法最多可以获取1000条（即`page=10`），超过1000条后会有报错信息。为了能获取到更多项目，我们需要对访问查询作出限制，使得每次查询结果都不超过1000条，然后将多次查询结果合并起来得到完整结果，例如仅查找创建于2021年1月至2月的项目数，这样就可以得到1000条以内的结果，再逐页访问即可。限制时间的api为：`https://api.github.com/search/repositories?q=dapp+created:2021-04-01T00:00:00..2021-05-01&page=1&per_page=100`，这个api可以获取创建时间在2021年4月1日至5月1日的所有项目数据，如果要获取其他时间范围，对api中的时间进行修改即可，可以精确到秒；如果时间限制是早于或晚于某个时间点，则写成`created:>=2021-04-01T00:00:00`，大于等于符号可以改成小于等于或者大于或者小于。如果想限制其他字段，比如更新时间，则把created修改为updated即可。

具体到写python代码时，代码如下：

```python
from github3 import login, GitHub
import getpass
import re
import requests
import json
import csv

def main():
    result = requests.get("https://api.github.com/search/repositories?q=dapp+created:2021-04-01T00:00:00..2021-05-01&page=6&per_page=100")
    response_dict = result.json()
    repo_dicts = response_dict['items']
    print("Toal repositories:", response_dict['total_count'])
    print("Repositories returned:", len(repo_dicts))
    keys = {'id','full_name','html_url','commits_url','language','description','forks_count','stargazers_count','watchers_count','created_at','pushed_at','updated_at'}
    save_dicts = []
    for i in repo_dicts:
        j = {key: value for key, value in i.items() if key in keys}
        save_dicts.append(j)
    with open('test.csv', 'a+', newline='',encoding='utf-8') as f: 
        fieldnames = ['id','full_name','html_url','commits_url','language','description','forks_count','stargazers_count','watchers_count','created_at','pushed_at','updated_at']
        writer = csv.DictWriter(f,fieldnames=fieldnames)
        #writer.writeheader()
        writer.writerows(save_dicts)

if __name__ == "__main__":
    main()
```

<!--这段代码`import`的一些包其实没有用到，是最初学着写的时候都引入了，之后没删。-->

代码第9行使用`requests.get()`获取api的数据，接着使用`.json()`将它转为字典。这个字典的`total_count`项就是前面图中显示的项目总数，这里我们没有用到；字典的`items`项则是我们需要的项目列表，将它单独存到`repo_dicts`，显然获取的项目列表数并不等于项目总数。

接下来把这次数据处理所需要的字段写到`keys`中，从`repo_dicts`中提取这些列的内容存到新列表`save_dicts`中，再将这些数据保存到csv文件里。这个提取数据并保存的过程应该有更简练的代码写法，我懒得改了就这样吧。

最终得到的csv文件如下图所示：

![image-20210430105338111](https://i.loli.net/2021/04/30/YIcsFzMrN1Cb93l.png)

这个数据总量比api那里的总量要多一些，显然是有重复的，因此要进行去重，我们选择把相同id的条目删了，只保留一个。excel本身就有去重的功能，在python里用pandas也可以，代码如下：

```python
import csv
import pandas as pd

def main():
    frame=pd.read_csv('test.csv',engine='python')
    data = frame.drop_duplicates(subset=['id'], keep='first', inplace=False)
    data.to_csv('newtest.csv', encoding='utf8')

if __name__ == "__main__":
    main()
```

这样就得到了一份到目前为止所有和dapp相关的项目表。

## 数据处理

接下来要对所有数据进行分析统计。我们要做的图表如下：

1. 每月新Dapp项目数量分布
2. 项目创建及更新时间分布
3. 项目语言分布

第3个最简单，我们先看它。

### 项目语言分布

从csv文件读入数据后，按`language`列进行数量统计即可，代码如下：

```
import csv
import pandas as pd

pd.set_option('display.max_rows',None)
pd.set_option('display.max_columns', None)
pd.set_option('display.width', 1000)
pd.set_option('display.max_colwidth', 1000)

def main():
    frame=pd.read_csv('newtest.csv',engine='python')
    df1 = frame.language.value_counts()
    print(df1)

if __name__ == "__main__":
    main()
```

输出结果如下：

```python
JavaScript           10018
C#                    2087
HTML                  1019
TypeScript             806
CSS                    376
Solidity               336
Vue                    335
Java                   295
Python                 256
Shell                  139
Go                     133
C++                    120
Dockerfile              72
PHP                     65
Kotlin                  54
Ruby                    52
Swift                   49
Dart                    37
Rust                    34
Objective-C             30
Jupyter Notebook        30
C                       25
Makefile                24
WebAssembly             22
TSQL                    19
SCSS                    17
PowerShell              10
F#                      10
TeX                      9
Perl                     8
Clojure                  8
Haskell                  7
R                        7
Svelte                   6
CoffeeScript             6
Scala                    6
Nix                      5
Visual Basic             5
Visual Basic .NET        5
D                        4
Elm                      4
Smarty                   3
Elixir                   3
GLSL                     3
ASP                      3
HCL                      3
PLpgSQL                  2
ShaderLab                2
OCaml                    2
Logos                    2
Blade                    2
Roff                     2
LiveScript               2
Hack                     2
PLSQL                    2
Sass                     1
ECL                      1
GDScript                 1
NSIS                     1
Stata                    1
CMake                    1
MATLAB                   1
EJS                      1
Twig                     1
QML                      1
XML                      1
Batchfile                1
Crystal                  1
Apex                     1
Emacs Lisp               1
Lua                      1
Racket                   1
VimL                     1
Handlebars               1
4D                       1
PureScript               1
Pascal                   1
Io                       1
Assembly                 1
Name: language, dtype: int64
```

### 每月新增Dapp项目数量分布

从csv文件读入数据后，访问`created_at`这一列并截取其前7位，即可得到每个项目的创建年月，统计每个月创建的项目数量即可得到数据结果，代码如下：

```python
import csv
import pandas as pd

pd.set_option('display.max_rows',None)
pd.set_option('display.max_columns', None)
pd.set_option('display.width', 1000)
pd.set_option('display.max_colwidth', 1000)

def main():
    frame=pd.read_csv('newtest.csv',engine='python')
    frame['created'] = frame['created_at'].str[0:7]
    df1 = frame.created.value_counts()
    print(df1)

if __name__ == "__main__":
    main()
```

这份数据内容很多，因此就不列出了。

### 项目创建及更新时间分布

由于数据很多，因此这里并不逐月统计，对于创建时间，统计每半年的项目数；对于更新时间，统计每一年的项目数，例如创建于2009年10月至2010年4月且最后一次更新于2014年4月至2015年4月的项目有1个，则这一对时间所对应的值就是1。以此类推，可以得到一份完整表格。

我们首先读入csv文件，截取创建和更新的年月数据，并依次分组并统计每组的项目数，这样就可以得到每组年月对所对应的项目数，代码如下：

```python
import csv
import pandas as pd

pd.set_option('display.max_rows',None)
pd.set_option('display.max_columns', None)
pd.set_option('display.width', 1000)
pd.set_option('display.max_colwidth', 1000)

def main():
    frame=pd.read_csv('newtest.csv',engine='python')
    frame['created'] = frame['created_at'].str[0:7]
    frame['updated'] = frame['updated_at'].str[0:7]
    group = frame.groupby(['created','updated'])
    dict_tmp = dict(group.size())
    print(dict_tmp)

if __name__ == "__main__":
    main()
```

得到的结果是`... ('2021-03', '2021-03'): 473, ('2021-03', '2021-04'): 151,...`这样的格式，将这些放到excel里并进行分列、去除无效数据等处理，最终可以得到如下图所示的表格：

![image-20210430122233349](https://i.loli.net/2021/04/30/tS9wECnmoiONprB.png)

接下来筛选出created列中2014-11~2015-04之间的数据，将筛选后的updated和num这两列复制到新区域（比如sheet4），按升序排列，如下图，选中2015-04之前的数据，就可以在右下角看到这个时间段内的项目数总和。

![image-20210430122713555](https://i.loli.net/2021/04/30/Eq83h4BrRxLd5z6.png)

用类似的方法进行多次筛选、排序、求和，最终可以得到如下数据表：

![image-20210430124738534](https://i.loli.net/2021/04/30/1Qok6Z9U2r5RtvO.png)

行表示创建时间，列表示更新时间。

## 图像生成

使用`Echarts`生成图表，因为没学过前端，所以就直接用了[以太坊 Dapp 调研](https://zhuanlan.zhihu.com/p/32759663)给出的js代码，链接为 [Github](https://github.com/heeeeeng/my_docs/tree/master/ethereum_dapp/data_viewer) 。

所有数据是直接在`web.js`中写好的，并不是从其他文件读取，且文件开头的`var scatter_origin_data `其实可以删了。此外，这份文件中的个人和组织项目散点图我并不需要，因此也直接删了。

接下来将`web.js`中的数据修改成前面统计好的数据，打开`web.html`就可以看到图表了。

由于hexo博客也可以显示echarts，因此我就把图表放这里了。



{% echarts 400 '85%' %}
{
title: {
            text: '每月新Dapp项目数量分布',
            x:'center'
        },
        tooltip: {
            trigger: 'axis'
        },
        toolbox: {
            show: true,
            feature: {
                dataZoom: {
                    yAxisIndex: 'none'
                },
                dataView: {readOnly: false},
                magicType: {type: ['line', 'bar']},
                restore: {},
                saveAsImage: {}
            }
        },
        xAxis:  {
            name: "创建时间",
            type: 'category',
            boundaryGap: false,
            data: ["2009-01","2009-02","2009-03","2009-04","2009-05","2009-06","2009-07","2009-08","2009-09","2009-10","2009-11","2009-12","2010-01","2010-02","2010-03","2010-04","2010-05","2010-06","2010-07","2010-08","2010-09","2010-10","2010-11","2010-12","2011-01","2011-02","2011-03","2011-04","2011-05","2011-06","2011-07","2011-08","2011-09","2011-10","2011-11","2011-12","2012-01","2012-02","2012-03","2012-04","2012-05","2012-06","2012-07","2012-08","2012-09","2012-10","2012-11","2012-12","2013-01","2013-02","2013-03","2013-04","2013-05","2013-06","2013-07","2013-08","2013-09","2013-10","2013-11","2013-12","2014-01","2014-02","2014-03","2014-04","2014-05","2014-06","2014-07","2014-08","2014-09","2014-10","2014-11","2014-12","2015-01","2015-02","2015-03","2015-04","2015-05","2015-06","2015-07","2015-08","2015-09","2015-10","2015-11","2015-12","2016-01","2016-02","2016-03","2016-04","2016-05","2016-06","2016-07","2016-08","2016-09","2016-10","2016-11","2016-12","2017-01","2017-02","2017-03","2017-04","2017-05","2017-06","2017-07","2017-08","2017-09","2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04","2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12","2019-01","2019-02","2019-03","2019-04","2019-05","2019-06","2019-07","2019-08","2019-09","2019-10","2019-11","2019-12","2020-01","2020-02","2020-03","2020-04","2020-05","2020-06","2020-07","2020-08","2020-09","2020-10","2020-11","2020-12","2021-01","2021-02","2021-03","2021-04"]
        },
        yAxis: {
            name: '新项目数量',
            type: 'value',
            axisLabel: {
                formatter: '{value}'
            }
        },
        series: [
            {
                name:'新项目数量',
                type:'line',
                data: [1,0,0,1,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0,0,1,0,0,1,1,1,0,2,0,1,2,1,3,2,1,2,2,2,0,1,5,2,3,5,2,1,1,5,4,4,3,3,8,8,8,5,7,5,8,11,16,9,9,5,6,6,12,19,5,12,16,10,14,21,29,29,26,18,29,34,34,32,34,52,42,49,63,56,50,58,66,64,66,60,41,37,69,60,75,109,128,118,148,203,193,217,223,229,339,353,455,425,625,506,526,596,488,490,432,456,441,341,433,391,385,387,383,379,278,303,313,288,320,288,291,407,479,348,381,316,339,411,448,392,492,456,633,581],
                markPoint: {
                    data: [
                        {type: 'max', name: '最大值'}
                    ]
                },
                markLine: {
                    data: [
                        {type: 'average', name: '平均值'}
                    ]
                }
            }
        ]
}
{% endecharts %}

{% echarts 400 '85%' %}
{
        title : {
            text: '项目语言分布',
            x:'center'
        },
        tooltip : {
            trigger: 'item',
            formatter: "{b} <br/> 项目数量: {c} ({d}%) "
        },
        legend: {
            orient: 'vertical',
            left: 'left',
            data: ['JavaScript','HTML', 'TypeScript', 'CSS', 'Others']
        },
        series : [
            {
                name: '项目语言分布',
                type: 'pie',
                radius : '55%',
                center: ['50%', '60%'],
                data:[
                    {value:10018, name:'JavaScript'},
                    {value:2087, name:'C#'},
                    {value:1019,name:"HTML"},
                    {value:806,name:"TypeScript"},
                    {value:376,name:"CSS"},
                    {value:336,name:"Solidity"},
                    {value:335,name:"Vue"},
                    {value:295,name:"Java"},
                    {value:256,name:"Python"},
                    {value:139,name:"Shell"},
                    {value:133,name:"Go"},
                    {value:120,name:"C++"},
                    {value:687, name:'Others'}
                ],
                itemStyle: {
                    emphasis: {
                        shadowBlur: 10,
                        shadowOffsetX: 0,
                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                    }
                }
            }
        ]
}
{% endecharts %}

{% echarts 400 '85%' %}
{
        title : {
            text: '剩余语言分布',
            x:'center'
        },
        tooltip : {
            trigger: 'item',
            formatter: "{b} <br/> 项目数量: {c} ({d}%) "
        },
        series : [
            {
                name: '编程语言数量分布',
                type: 'pie',
                radius : '55%',
                center: ['50%', '60%'],
                data:[                    
                    {value:72,name:"Dockerfile"},
                    {value:65,name:"PHP"},
                    {value:54,name:"Kotlin"},
                    {value:52,name:"Ruby"},
                    {value:49,name:"Swift"},
                    {value:37,name:"Dart"},
                    {value:34,name:"Rust"},
                    {value:30,name:"Jupyter Notebook"},
                    {value:30,name:"Objective-C"},
                    {value:25,name:"C"},
                    {value:24,name:"Makefile"},
                    {value:22,name:"WebAssembly"},
                    {value:19,name:"TSQL"},
                    {value:17,name:"SCSS"},
                    {value:10,name:"PowerShell"},
                    {value:10,name:"F#"},
                    {value:137,name:"Others"},
                ],
                itemStyle: {
                    emphasis: {
                        shadowBlur: 10,
                        shadowOffsetX: 0,
                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                    }
                }
            }
        ]
}
{% endecharts %}

![fe0b04c74a9bae3058a8c53ba973f4e](https://i.loli.net/2021/04/30/UC8DSlosXBIu5Mv.png)

热力图不知道为什么始终没法在这里显示，所以就放个截图了......