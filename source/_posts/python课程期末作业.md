---
title: python课程期末作业
date: 2020-06-13 15:20:05
tags: python
category: 课程笔记
---

实际上在这之前还有两课讲`scikit`库，不过没作业，就不整理内容了。

<!--more-->

<h1 align = "center">北京师范大学2019～2020学年第二学期期末大作业</h1>
<h1 align = "center">（研究生）</h1>

__课程名称：__<u>Python编程之美</u>   &nbsp;&emsp;&emsp;&emsp; __任课教师姓名：__<u>邓擎琼</u>

__总分__：<u>40</u>   

__院 系：__<u>人工智能学院</u> &nbsp;&emsp;&emsp;&emsp; __年级：__<u>2019级</u>

__姓 名：__<u>李琨</u>   &nbsp;&emsp;&emsp;&emsp; __学 号：__<u>201931210003</u>

| 题号 | 第一题 | 第二题 | 第三题 | 第四题 | 第五题 | 总分 |
| :--- | :----: | :----: | :----: | :----: | :----: | ---: |
| 得分 |        |        |        |        |        |      |

__阅卷教师（签字）：__<u> &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; &emsp;&emsp;&emsp;&emsp;</u>

## 题目：

1. 读入北京历史天气数据（北京天气.xlsx）；  <font color='red'>分值：3</font>
或者：从http://www.tianqihoubao.com/lishi/beijing.html
网站上通过爬虫把北京2011年-至今的天气数据爬下来，并保存为Excel文件；  <font color='red'>分值：10</font>
2. 读入北京空气质量数据（北京空气质量.xlsx），并把该数据和第1步中得到的北京天气数据进行融合，得到一个同时包含天气和空气质量的表格数据，保存为Excel文件；   <font color='red'>分值：5</font>
3. 对2011-2019年的每一年，统计这一年中白天为晴、雨、多云、阴、雪、雾霾、扬沙的天数，并绘制成饼图；    <font color='red'>分值：4</font>
4. 对2014-2019年的每一年，统计这一年中持续1天污染的次数、持续2天污染的次数、持续3天污染的次数、持续4天污染的次数和持续5天及以上有污染的次数，把所有年份的统计结果绘制成一幅柱状图；    <font color='red'>分值：6</font>
5. 在北京历史天气和空气质量数据的基础上，根据当天的天气情况以及前两天的天气及空气质量情况，预测当天的空气质量等级，要求至少比较两种算法，从中选出较优的算法并确定最优超参数（如果算法有超参数的话） 。  <font color='red'>分值：15</font>

## 承诺：

本人承诺本程序是自己编写的，没有抄袭。

### 导入库

首先列出所有用到的库，如下：

```python
import requests
import re
import csv
import numpy as np
import pandas as pd
from bs4 import BeautifulSoup
from requests.compat import urljoin
import matplotlib.pyplot as plt
from sklearn.feature_extraction import DictVectorizer
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.preprocessing import PolynomialFeatures, StandardScaler
from collections import Counter
from sklearn.model_selection import train_test_split
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import StratifiedKFold
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix
from sklearn.metrics import accuracy_score
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from sklearn.naive_bayes import GaussianNB
from sklearn import svm
from sklearn.svm import SVC
from sklearn.svm import LinearSVC
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import OrdinalEncoder, OneHotEncoder, LabelEncoder
```

2. 考虑到题目中有画图的要求，而内容有中文，因此先将`plt`的字体改为中文字体。

```python
plt.rcParams['font.sans-serif'] = ['SimHei']
```

### 第一题

1. 首先分析天气数据的[网页链接](http://www.tianqihoubao.com/lishi/beijing.html)，该页面并不直接包含天气数据，而是包含了指向每个月天气数据的链接，因此需要先从该页面把所有月份的链接提取出来。经过分析可知，该页面所有链接都在`class_="box pcity"`的`div`块中，是`a`标签，因此可以通过以下函数来获取所有链接，该函数将所有链接存放在一个列表中并返回。

   ```python
   def get_href():
       '''
       作用：获取所有天气链接
       参数：无
       返回值：href_list 所有天气链接的列表
       使用方式：list = get_href()
       '''
       # 设置网页链接和head等信息
       url = 'http://www.tianqihoubao.com/lishi/beijing.html'
       head = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
       # 获取网页文件并分析
       html = requests.get(url, headers=head)
       bsObj = BeautifulSoup(html.content, 'lxml')
       # 找到所有天气链接所在区块
       allLinks = bsObj.find_all('div', class_="box pcity")
       href_list = []
       # 提取所有链接并存入列表返回
       for i in allLinks[:10]:
           aLink = i.find_all('a')
           for j in aLink:
               href = urljoin(url, j['href'])
               href_list.append(href)
       return href_list
   ```

2. 得到所有链接的列表后，遍历该列表即可访问每个月的天气数据网页，分析这些网页可以发现，天气数据存放在`table`中，每一行的标签为`tr`，每一项的标签为`td`，而一行有四项，分别是日期、天气、温度、风力风向，其中第一行是表格头，因此可以从表格的第二行（第二个`tr`）开始遍历，获取所有`td`的内容（是一个长度为4的列表），将内容逐一处理再存放在列表中。遍历完成后即可得到所有天气数据，我将这些数据存放在列表中并返回，函数如下：

   ```python
   def analysis_href(href_list):
       '''
       作用：分析处理天气链接里的数据
       参数：href_list 天气链接列表
       返回值：lists 所有处理后的天气数据，格式为[日期、天气、温度、风力风向]
       使用方式：lists = analysis_href(href_list)
       '''
       # 设置head
       head = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'}
       lists = []
       # 遍历所有链接
       for href in href_list:
           # 获取网页文件并分析
           html = requests.get(href, headers=head)
           bsObj = BeautifulSoup(html.content, 'lxml')
           # 找到天气数据所在的表格
           table = bsObj.find("table").find_all("tr")
           # 从表格第二行开始提取数据（第一行是表格的head）
           for i in table[1:]:
               content = i.find_all("td")
               # 提取日期并去除多余的空格和换行符等
               date = content[0].text.replace(
                   " ", "").replace('\n', '').replace('\r', '')
               # 提取天气并去除多余的空格和换行符等
               weather = content[1].text.replace(" ", "").replace(
                   " ", "").replace('\n', '').replace('\r', '')
               # 提取温度并去除多余的空格和换行符等
               temperature = content[2].text.strip().replace(
                   " ", "").replace('\n', '').replace('\r', '')
               # 提取风力风向并去除多余的空格和换行符等
               wind = content[3].text.strip().replace(
                   " ", "").replace('\n', '').replace('\r', '')
               # 将提取的数据存入列表
               lists.append([date, weather, temperature, wind])
       return lists
   ```

3. 在得到天气数据的列表后，需要将该列表数据写入excel文件，我先将列表转为`numpy`数组，再将该数组转为`DataFrame`，并把索引设置为`日期`列，这时就可以用`pandas`的库函数将所有内容写入`excel`文件了，函数如下：

   ```python
   def write_excel(filename):
       '''
       作用：将数据写入excel文件
       参数：filename 文件名
       返回值：无
       使用方式：write_excel("weather.xlsx")
       '''
       # 调用分析网页的函数获取所有天气数据所在列表
       a = np.array(analysis_href(get_href()))
       # 将天气数据列表转为DateFrame
       DF = pd.DataFrame(a, columns=['日期', '天气', '温度', '风力风向'])
       # 将索引设置为日期列，去除原本的索引序号
       DF.set_index('日期', inplace=True)
       # 将数据写入excel文件
       DF.to_excel(filename)
   ```

4. 在主函数中通过调用`write_excel()`即可得到天气数据文件，完成第一题。

### 第二题

1. 首先读取天气数据和空气质量数据，并将`日期`列设置为`datetime`格式的索引，以便后续分析。

   ```python
   # 读取数据
   df_weather = pd.read_excel('weather.xlsx', 'Sheet1', header=0)
   df_air = pd.read_excel('北京空气质量.xlsx', 'Sheet1', header=0)
   df_weather['日期'] = pd.to_datetime(df_weather['日期'], format="%Y年%m月%d日")
   df_weather.set_index('日期', inplace=True)
   df_air['日期'] = pd.to_datetime(df_air['日期'], format="%Y-%m-%d")
   df_air.set_index('日期', inplace=True)
   ```

2. 接下来通过`pandas`的库函数即可将两个`DataFrame`按日期融合起来，因为两个表格中的日期并没有完全一致，所以去除了不一致的日期。

   ```python
   df_merge = pd.merge(df_weather, df_air, on='日期')
   df_merge.index = df_merge.index.date
   ```

3. 将该`DataFrame`写入`excel`文件，完成第二题。

   ```python
   df_merge.to_excel('merge.xlsx')
   ```

### 第三题

1. 分析天气数据，可以看出白天天气和夜晚天气通过`/`分隔，因此首先通过`split()`函数得到白天天气。

2. 由于数据源本身的问题，有个别天气是无效的（是`-`符号），因此要删去这些数据。

3. 得到白天天气后，还需要将该天气转换为题目中提到的几个类别中的一个，例如“小雨”要转换为“雨”。值得注意的是，”雨夹雪“天气我算作雨天而不是雪天。

4. 上述处理天气数据的函数如下，该函数返回处理好的天气数据。

   ```python
   def process_weather_data(df_weather):
       '''
       作用：处理天气数据以便后续绘图
       参数：df_weather 初始天气数据
       返回值：df_weather 处理好的天气数据
       使用方式：df_weather = process_weather_data(df_weather)
       '''
       # 分离出白天天气
       df_weather['白天天气'] = df_weather['天气'].map(lambda x: x.split('/')[0])
       # 删去无效数据
       df_weather = df_weather.drop(df_weather[df_weather['白天天气'] == '-'].index)
       # 统一雨天数据
       df_weather.loc[(df_weather['白天天气'] == '小雨') | (df_weather['白天天气'] == '中雨') | (df_weather['白天天气'] == '大雨') | (df_weather['白天天气'] == '暴雨') | (df_weather['白天天气'] == '阵雨') | ( df_weather['白天天气'] == '小到中雨') | (df_weather['白天天气'] == '中到大雨') | (df_weather['白天天气'] == '雷阵雨') | (df_weather['白天天气'] == '雨夹雪'), '白天天气'] = '雨'
       # 统一雪天数据
       df_weather.loc[(df_weather['白天天气'] == '小雪') | (df_weather['白天天气'] == '中雪') | (df_weather['白天天气'] == '大雪') | ( df_weather['白天天气'] == '小到中雪') | (df_weather['白天天气'] == '中到大雪'), '白天天气'] = '雪'
       # 统一扬沙天气
       df_weather.loc[df_weather['白天天气'] == '浮尘', '白天天气'] = '扬沙'
       # 统一雾霾天气
       df_weather.loc[(df_weather['白天天气'] == '雾') | ( df_weather['白天天气'] == '霾'), '白天天气'] = '雾霾'
       return df_weather
   ```

5. 对处理好的数据按年分组，再遍历分组结果，可以得到每一年的数据，由于题目要求2011年至2019年，因此当遍历到2020年时终止循环。

6. 对每一年的数据按白天天气这一列分组，统计分组的`size`，即可得到每种天气的天数，在此基础上可以绘制图像。上述分组并统计绘图的函数如下：

   ```python
   def weather_pie(df_weather):
       '''
       作用：根据处理好的天气数据画饼状图
       参数：df_weather 处理好的天气数据
       返回值：无
       使用方式：weather_pie(df_weather)
       '''
       # 按年份分组
       weather_group_y = df_weather.groupby(df_weather.index.year)
       # 对每年进行循环
       for n, g in weather_group_y:
           # 不需要2020的数据
           if n == 2020:
               break
           # 按白天天气分组
           weather_group = g.groupby(g['白天天气']).size()
           # 输出分组结果
           print(str(n)+'年天气天数统计如下：')
           print(weather_group)
           # 画饼图并保存
           weather_group.name = ''
           weather_group.plot.pie(startangle=90)
           plt.title('', fontproperties='Kaiti')
           plt.savefig('weather-pie-of-'+str(n), dpi=300)
           plt.show()
   ```

7. 在主函数中调用`weather_pie()`，参数为第二题中读取的天气数据，完成第三题。

   ```python
   weather_pie(process_weather_data(df_weather))
   ```

### 第四题

1. 针对每一年的数据，首先根据空气质量等级区分开有污染和无污染，为了方便，我新增一列存储污染情况，将所有无污染的数据设置为0，有污染的设置为1。

2. 同样是数据源的问题，个别数据的空气质量等级是无，属于无效数据，因此我删去这些数据。

3. 接下来统计持续污染天数，这里参考了[知乎](https://www.zhihu.com/question/41265794)。具体方法是首先找到污染情况不同的坐标，该坐标就是持续同一污染状态的终点，而上一次持续的终点也是下一次持续的起点，因此可以得到一个存储了持续污染情况天数的表格，再从该表格中取出污染情况为1的部分，并进行分组统计，即可得到这一年持续`n`天污染的统计结果。需要注意的是，因为题目要求最高统计5天及以上，这里要把持续天数超过5天的也改为5。

4. 由于这一题并不是每一年画一个图，而是所有数据一起画图，因此这里最后要把得到的统计结果转置，存储为行名是年份、列名是污染持续天数的新`DataFrame`，并返回。上述处理过程的函数如下：

   ```python
   def process_air_data(df, year):
       '''
       作用：处理空气污染数据
       参数：df 初始空气污染数据
       返回值：df3 处理好的空气污染数据
       使用方式：df_air = process_air_data(df_air)
       '''
       # 统一污染
       df.loc[(df['质量等级'] == '轻度污染') | (df['质量等级'] == '中度污染') | (
           df['质量等级'] == '重度污染') | (df['质量等级'] == '严重污染'), '污染'] = 1
       # 统一无污染
       df.loc[(df['质量等级'] == '优') | (df['质量等级'] == '良'), '污染'] = 0
       # 删去无效数据
       df = df.drop(df[df['质量等级'] == '无'].index)
       # 找污染数字相同的位置
       pos, = np.where(np.diff(df['污染']))
       # 定位连续污染和连续无污染的起止点
       start, end = np.insert(pos+1, 0, 0), np.append(pos, len(df)-1)
       # 计算污染状态的持续天数
       df2 = pd.DataFrame({'污染': df['污染'][start], '持续天数': end-start+1})
       # 从连续天数的表格中提取是污染的
       df3 = df2.loc[df2['污染'] == 1]
       # 连续天数大于5的统一变成5，方便下一步分组统计画图
       df3.loc[df3['持续天数'] > 5, '持续天数'] = 5
       # 按持续天数分组计数，并将计数结果存为DateFrame
       df3 = df3.groupby(df3['持续天数']).size().reset_index(name=str(year))
       # 重置index
       df3.set_index('持续天数', inplace=True)
       # 转置行列，方便后续合并分组结果和画图
       df3 = pd.DataFrame(df3.values.T, index=df3.columns, columns=['1天', '2天', '3天', '4天', '5天及以上'])
       return df3
   ```

5. 接下来是画图函数，该函数将合并了所有年份的数据绘制为条形图，如下：

   ```python
   def pollution_bar(df):
       '''
       作用：按处理好的空气污染数据画柱状图
       参数：df 处理好的空气污染数据
       返回值：无
       使用方式：pollution_bar(df_air)
       '''
       df.plot.bar()
       plt.title('2014年至2019年持续污染天数柱状图', fontproperties='Kaiti')
       plt.xlabel('天数', fontproperties='Kaiti')
       plt.ylabel('出现次数', fontproperties='Kaiti')
       plt.tight_layout()
       plt.xticks(rotation=0)
       plt.savefig('pollution-bar', dpi=300)
       plt.show()
   ```

6. 最后要在主函数中将原始的空气污染数据按年分组，并对分组结果逐一调用`process_air_data()`，再将得到的持续污染天数的数据合并起来，如下：

   ```python
   air_group_y = df_air.groupby(df_air.index.year)
   df_air_processed = process_air_data(air_group_y.get_group(2014), 2014)
   for i in range(2015, 2020):
       df_air_processed = pd.concat([df_air_processed, process_air_data(air_group_y.get_group(i), i)])
   ```

7. 对处理好的数据调用`pollution_bar()`绘制条形图，参数是第二题中读取的空气污染数据，完成第四题。

   ```python
   pollution_bar(df_air_processed)
   ```

### 第五题

1. 分析题目要求，”**根据当天的天气情况以及前两天的天气及空气质量情况，预测当天的空气质量等级**“，而天气情况包括天气、温度和风力风向，因此需要进行以下处理。

2. 首先将白天和晚上的天气、风力风向和最高最低温度分离出来，这三个数据都是以`/`为分隔，因此可以用`split()`来完成。这里要注意，温度数据的最后一位是摄氏度的标记，因此要去掉，只保留前面的数字。

3. 接下来分离风力和风向，这里的规律并不明显，但总体而言可以用`风`字作为分隔符来提取，并在之后的处理中将相同含义但不同表示的字符串合并起来。

4. 天气、风力和风向的数据都是字符串，而其它数据则是数字，由于`scikit`处理的数据都是数字，因此这里需要对字符串进行特征提取和编码，最初我尝试用`DictVectorizer`来做，但是这样出来的矩阵略大，而结果准确率也略低，因此决定在这里直接用字典和`mapping()`将字符串转数字。需要注意的是，在这里我把`西南偏南`和`西南`算作同一类，用相同的数字表示。另一方面，上一步中分离出的风力数据，如`向≤3级`、`<3级`、`1-2级`等这些显然是同一个含义的也算作一类，用相同的数字表示。

5. 由于天气种类很多，而其中有一些属于同一类，如果不合并相同类别的数据，会对之后的模型训练造成影响，因此按第三题的方法将所有天气统一，并转为数字表示。

6. 质量等级也是字符串，因此采用同样的方法进行转换。转换结束后，原本的天气、风力风向等等数据就可以删除了。

7. 由于预测还用到了前两天的天气和空气质量情况，因此要把前两天的数据逐一增加到当天数据中，作为新的一列保存，之后要删除无效数据。

8. 由于预测时并没有用到当天的空气质量情况，因此要把当天的空气质量数据都删除，只保留空气质量等级这一列作为训练模型的`target`。

9. 至此，所有数据已经转为数字类型，并剔除不需要的数据，接下来需要进行标准化，并返回标准化之后的数组，该数组第一列是`target`，剩下数据是训练用数据。上述数据处理过程为如下函数：

   ```python
   def process_predict_data(df):
       '''
       作用：处理天气和空气数据，方便后续训练
       参数：df 初始合并好的天气+空气数据
       返回值：df 处理好的数据
       使用方式：predict_array = process_predict_data(df)
       '''
       # 划分天气、风力、风向和温度
       df['白天天气'] = df['天气'].map(lambda x: x.split('/')[0])
       df['夜晚天气'] = df['天气'].map(lambda x: x.split('/')[1])
       df['白天风力风向'] = df['风力风向'].map(lambda x: x.split('/')[0])
       df['夜晚风力风向'] = df['风力风向'].map(lambda x: x.split('/')[1])
       df['最高温度'] = df['温度'].map(lambda x: int(x.split('/')[0][:-1]))
       df['最低温度'] = df['温度'].map(lambda x: int(x.split('/')[1][:-1]))
       df['白天风力'] = df['白天风力风向'].map(lambda x: x.split('风')[0])
       df['白天风向'] = df['白天风力风向'].map(lambda x: x.split('风')[1])
       df['夜晚风力'] = df['夜晚风力风向'].map(lambda x: x.split('风')[0])
       df['夜晚风向'] = df['夜晚风力风向'].map(lambda x: x.split('风')[1])
       # 风向转数字
       wind_map_1 = {'无持续': 0, '东': 1, '南': 2, '西': 3, '北': 4, '东北': 5, '东南': 6, '西北': 7, '西南': 8, '西南偏南': 8}
       df['白天风力'] = df['白天风力'].map(wind_map_1)
       df['夜晚风力'] = df['夜晚风力'].map(wind_map_1)
       # 风力转数字
       wind_map_2 = {'向≤3级': 0, '<3级': 0, '1-2级': 0, '≤3级': 0, '向<3级': 0, '向3-4级': 1, '3-4级': 1, '3～4级': 1, '3～4级': 1, '4': 1, '4-5级': 1, '4～5级': 1, '5～6级': 2, '5-6级': 2, '6-7级': 2}
       df['白天风向'] = df['白天风向'].map(wind_map_2)
       df['夜晚风向'] = df['夜晚风向'].map(wind_map_2)
       # 删去不需要的列
       df = df.drop('天气', axis=1).drop('温度', axis=1).drop('风力风向', axis=1).drop('白天风力风向', axis=1).drop('夜晚风力风向', axis=1)
       # 删去无效数据
       df = df.drop(df[df['质量等级'] == '无'].index)
       # 统一雨天数据
       df.loc[(df['白天天气'] == '小雨') | (df['白天天气'] == '中雨') | (df['白天天气'] == '大雨') | (df['白天天气'] == '暴雨') | (df['白天天气'] == '阵雨') | (df['白天天气'] == '小到中雨') | (df['白天天气'] == '中到大雨') | (df['白天天气'] == '雷阵雨') | (df['白天天气'] == '雨夹雪'), '白天天气'] = '雨'
       df.loc[(df['夜晚天气'] == '小雨') | (df['夜晚天气'] == '中雨') | (df['夜晚天气'] == '大雨') | (df['夜晚天气'] == '暴雨') | (df['夜晚天气'] == '阵雨') | (df['夜晚天气'] == '小到中雨') | (df['夜晚天气'] == '中到大雨') | (df['夜晚天气'] == '大到暴雨') | (df['夜晚天气'] == '雷阵雨') | (df['夜晚天气'] == '雨夹雪'), '夜晚天气'] = '雨'
       # 统一雪天数据
       df.loc[(df['白天天气'] == '小雪') | (df['白天天气'] == '中雪') | (df['白天天气'] == '大雪') | (df['白天天气'] == '小到中雪') | (df['白天天气'] == '中到大雪'), '白天天气'] = '雪'
       df.loc[(df['夜晚天气'] == '小雪') | (df['夜晚天气'] == '中雪') | (df['夜晚天气'] == '大雪') | (df['夜晚天气'] == '小到中雪') | (df['夜晚天气'] == '中到大雪'), '夜晚天气'] = '雪'
       # 统一扬沙天气
       df.loc[df['白天天气'] == '浮尘', '白天天气'] = '扬沙'
       df.loc[df['夜晚天气'] == '浮尘', '夜晚天气'] = '扬沙'
       # 统一雾霾天气
       df.loc[(df['白天天气'] == '雾') | (df['白天天气'] == '霾'), '白天天气'] = '雾霾'
       df.loc[(df['夜晚天气'] == '雾') | (df['夜晚天气'] == '霾'), '夜晚天气'] = '雾霾'
       # 质量等级转数字
       quality_mapping = {'优': 0, '良': 1, '轻度污染': 2, '中度污染': 3, '重度污染': 4, '严重污染': 5}
       df['质量等级'] = df['质量等级'].map(quality_mapping)
       # 天气转数字
       weather_mapping = {'晴': 0, '雨': 1, '阴': 2, '雪': 3, '多云': 4, '雾霾': 5, '扬沙': 6}
       df['白天天气'] = df['白天天气'].map(weather_mapping)
       df['夜晚天气'] = df['夜晚天气'].map(weather_mapping)
       # 增加昨天和前天的数据
       for i in range(len(df)-2):
           df.ix[i+2, '昨天AQI'], df.ix[i+2, '昨天质量等级'], df.ix[i+2, '昨天PM2.5'], df.ix[i+2, '昨天PM10'], df.ix[i+2, '昨天SO2'], df.ix[i+2, '昨天CO'], df.ix[i+2, '昨天NO2'], df.ix[i+2, '昨天O3_8h'], df.ix[i+2, '昨天白天天气'], df.ix[i+2, '昨天夜晚天气'], df.ix[i+2, '昨天白天风力'], df.ix[i+2, '昨天夜晚风力'], df.ix[i+2, '昨天白天风向'], df.ix[i+2, '昨天夜晚风向'], df.ix[i+2, '昨天最高温度'], df.ix[i+2, '昨天最低温度'], df.ix[i+2, '前天AQI'], df.ix[i+2, '前天质量等级'], df.ix[i+2, '前天PM2.5'], df.ix[i+2, '前天PM10'], df.ix[i+2, '前天SO2'], df.ix[i+2, '前天CO'], df.ix[i+2, '前天NO2'], df.ix[i+2, '前天O3_8h'], df.ix[i+2, '前天白天天气'], df.ix[i+2, '前天夜晚天气'], df.ix[i+2, '前天白天风力'], df.ix[i+2, '前天夜晚风力'], df.ix[i+2, '前天白天风向'], df.ix[i+2, '前天夜晚风向'], df.ix[i+2, '前天最高温度'], df.ix[i+2, '前天最低温度'] = df.ix[i+1, 'AQI'], df.ix[i+1, '质量等级'], df.ix[i+1, 'PM2.5'], df.ix[i+1, 'PM10'], df.ix[i+1, 'SO2'], df.ix[i+1, 'CO'], df.ix[i+1, 'NO2'], df.ix[i+1, 'O3_8h'], df.ix[i+1, '白天天气'], df.ix[i+1, '夜晚天气'], df.ix[i+1, '白天风力'], df.ix[i+1, '夜晚风力'], df.ix[i+1, '白天风向'], df.ix[i+1, '夜晚风向'], df.ix[i+1, '最高温度'], df.ix[i+1, '最低温度'], df.ix[i, 'AQI'], df.ix[i, '质量等级'], df.ix[i, 'PM2.5'], df.ix[i, 'PM10'], df.ix[i, 'SO2'], df.ix[i, 'CO'], df.ix[i, 'NO2'], df.ix[i, 'O3_8h'], df.ix[i, '白天天气'], df.ix[i, '夜晚天气'], df.ix[i, '白天风力'], df.ix[i, '夜晚风力'], df.ix[i, '白天风向'], df.ix[i, '夜晚风向'], df.ix[i, '最高温度'], df.ix[i, '最低温度']
       # 删除无效数据
       df = df.dropna(how='any')
       # 删除今天空气数据
       df = df.drop('AQI', axis=1).drop('PM2.5', axis=1).drop('PM10', axis=1).drop(
           'SO2', axis=1).drop('CO', axis=1).drop('NO2', axis=1).drop('O3_8h', axis=1)
       # 数据标准化
       ss = StandardScaler()
       predict_array = ss.fit_transform(df)
       return predict_array
   ```

10. 处理好数据后，就可以开始训练模型。首先用`train_test_split()`划分训练集和测试集。

11. 接下来建立一个算法列表，该列表包含了几个不同的分类器。

12. 对每一个分类器，用K折交叉判断其在训练集的准确率并输出。

13. 根据输出结果选择最优分类器，测试其在测试集上的性能并输出。

14. 在这里经过对比，选择了LDA分类器。整体训练过程如下：

    ```python
    def predict_air(array):
        '''
        作用：训练和测试模型
        参数：array 处理好的数据集
        返回值：无
        使用方式：predict_air(array)
        '''
        # 划分训练集和测试集
        x = array[:, 1:]
        y = array[:, 0]
        x_train, x_test, y_train, y_test = train_test_split(
            x, y, test_size=0.7, random_state=1)
        # 把备选算法放入列表
        models = []
        models.append(('LR', LogisticRegression(
            solver='liblinear', multi_class='ovr')))
        models.append(('LDA', LinearDiscriminantAnalysis()))
        models.append(('KNN', KNeighborsClassifier()))
        models.append(('CART', DecisionTreeClassifier()))
        models.append(('NB', GaussianNB()))
        models.append(('SVM', SVC(gamma='auto')))
        # 用训练集训练每个模型并评价
        results = []
        names = []
        for name, model in models:
            kfold = StratifiedKFold(n_splits=10, random_state=1, shuffle=True)
            cv_results = cross_val_score(model, x_train, y_train.astype(
                'int'), cv=kfold, scoring='accuracy')
            results.append(cv_results)
            names.append(name)
            print('%s: %f (%f)' % (name, cv_results.mean(), cv_results.std()))
        # 从上面的输出可知lda准确率最高，因此训练lad模型并输出测试集的准确率
        lda = LinearDiscriminantAnalysis()
        lda.fit(x_train, y_train.astype('int'))
        print(lda.score(x_test, y_test.astype('int')))
    ```

15. 在主函数中先后调用`process_predict_data()`和`predict_air()`，在已知某天天气情况和前两天的天气及空气情况时，也可以调用该模型来预测当天空气质量。完成第五题。

    ```python
    predict = process_predict_data(df_merge)
    predict_air(predict)
    ```



好了我终于写完了，这个空气质量预测根本就靠不住，一开始只有准确率只有0.3，用很麻烦的方法处理数据之后才达到现在的0.7。啊写实验报告好累，我总算不用再上课了。希望分数能好点。