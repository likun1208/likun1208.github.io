---
title: python课程记录-12
date: 2020-05-19 14:25:07
tags: python
category: 课程笔记
---

这一课讲Pandas库。

`import pandas as pd`

<!--more-->

## Pandas初探

1. 读取文件：`data = pd.read_excel(filename, index_col=0)`
2. 取出数据中的一列或一行：`data['学号'], data.loc[1]`

## Pandas数据类型

### Series(一维序列)

1. 由index+value组成

2. 通过列表创建Series：

   ```python
   a = pd.Series(['apple','peach'])
   # 0	apple
   # 1	peach
   a.index
   # RangeIndex(start=0,stop=2,step=1)
   a.values
   # array(['apple','peach'],dtype=object)
   a[1]
   # 'peach'
   a[1]=100
   # 原来是peach的地方改成了100
   a[0:]
   # 整个输出
   a[[0,1]]
   # 输出0和1
   a = pd.Series(['apple','peach'],index=['a','p'])
   # a    apple
   # p    peach
   a['a']
   # 输出索引a对应的value
   a['a']='lemon'
   # 原来的apple改成lemon
   a['a':]
   # 整个输出
   a[['a','p']]
   # 输出a和p对应的value
   ```

3. 通过字典创建Series：

   ```python
   d = {'Japan':'Tokyo','S.Korea':'Seoul','China':'Beijing'}
   a = pd.Series(d)
   # Japan        Tokyo
   # S.Korea      Seoul
   # China      Beijing
   
   indexL=['China','Japan','ingapore','S.Korea']
   a = pd.Series(d, index=indexL)
   # China       Beijing
   # Japan         Tokyo
   # ingapore        NaN
   # S.Korea       Seoul
   ```

4. 通过标量创建Series：

   ```python
   a = pd.Series('无')
   # 0    无
   a = pd.Series('无',index=np.arange(1,6))
   # 1    无
   # 2    无
   # 3    无
   # 4    无
   # 5    无
   ```

5. Series的基本运算：

   1. 和numpy一样，过滤、广播、ufunc等
   2. 对齐：对应索引进行运算
   3. 频数统计：value_counts()

### DataFrame(二维表)

1. 共用index的Series的有序集合

2. 从二维数组创建：

   ```python
   data = np.array([('Japan','Tokyo',4000),('S.Korea','Seoul',1300),('China','Beijing',9100)])
   DF1 = pd.DataFrame(data, columns=['nation','capital','GDP'],index=['a','b','c'])
   ```

3. 查看索引和数据

   ```python
   DF1.index
   DF1.columns
   DF1.values
   ```

4. 从字典创建：

   ```python
   dct = {'nation':['Japan','S.Korea','Japan'],'capital':pd.Series(['Tokyo','Seoul','Beijing'],index=['a','b','c']),'GDP':np.array([4900.1300.9100],dtype=int)}
   DF2=pd.DataFrame(dct)
   ```

5. 把某列数据作为index：`DF2.set_index('nation')`

6. 修改index/columns：`DF2.reindex(index=['c','a','b','d'])`

7. 数据选择：

   1. 取头尾若干行：`df.head(行数), df.tail(行数)`
   2. 选择一列：`DF2.nation, DF2['GDP']`
   3. 选择一行或多行：`DF2[0:2], DF2['a':'c']`    序号只能是切片，不能是下标
   4. `loc[index, columns]`根据索引对多个轴进行选取
   5. 取单个数据：`DF2.loc['c','GDP']`或`DF2.at['c','GDP']`
   6. 布尔索引：
      1. 用某列的值来选取数据：`DF2[DF2.GDP>3000]`
      2. `isin()`方法过滤数据：`DF2[DF2.nation.isin(['China','S.Korea'])]`
   
8. 增加一列：`DF2['population']=[130,55,1600]`

9. 增加行：`append(要添加的行)`，序号是添加行的name，如果加入参数ignore_index=True，则序号就是数字

10. 删除行列：`drop(序号)`，根据索引删除行列，默认删行，axis=1是删列

11. 多个DataFrame对象数据拼接：

    1. `pd.concat([p1,p2])`，序号是p1和p2各自的序号直接拼起来，是前几行p1，后几行p2；增加参数ignore_index=True，则序号是0、1、2、3这样；增加参数axis=1，则是前几列p1，后几列p2。
    2. `pd.merge(p1,p3,on='name')`，把p1和p3中name列相同的部分融合起来，如果是要把所有name都留下，但是只要一列name，则增加参数`how='outer'`

12. 缺失值处理：

    1. `isnull()`和`notnull()`：是否缺失
    2. `fillna()`：补充缺失值
    3. `dropna()`：删除包含缺失值的行或列

13. 基本运算：`sub,add,mul,div,sum,min,max,mean,std,describe`，默认是竖着运算，加axis=1变成横着运算

14. 分组：`groupby()`、`get_group(列名)`，也可以进行上一条的基本运算

15. 排序：`sort_value(by='', ascending=False)` 根据by的取值排序，可以是一个字符串`by='成绩'`，也可以是多个字符串的列表，`by=['成绩','年级']`

## 文件读写

### csv

1. `read_csv()/to_csv()`读写csv和txt
2. 加入参数index_col=0可以没有序号

### excel

1. `read_excel()/to_excel()`读写excel

2. 把几个dataframe写到一个excel文件里的不同sheet中：

   ```python
   writer=pd.ExcelWriter('atest.xlsx')
   a.to_excel(writer, sheet_name='Sheet1')
   b.to_excel(writer, sheet_name='Sheet2')
   # 或者
   with pd.ExcelWriter('atest.xlsx') as writer:
       a.to_excel(writer, sheet_name='Sheet1')
   	b.to_excel(writer, sheet_name='Sheet2')
   ```

3. 读取一个exce里的多个sheet

   ```python
   reader = pd.ExcelWriter('atest.xlsx')
   df1 = pd.read_excel(reader, 'Sheet1')
   df2 = pd.read_excel(reader, 'Sheet2')
   # 或者
   with pd.ExcelWriter('atest.xlsx') as reader:
       df1 = pd.read_excel(reader, 'Sheet1')
   	df2 = pd.read_excel(reader, 'Sheet2')
   ```

## 绘图

1. matplotlib中的pyplot模块：`b.plot()`，表格中的一列代表一条线的数据，行名是横坐标，表格内容值是纵坐标，默认是折线图
2. 柱状图：`b.plot(kind='bar')      b.plot.bar()`

## 随堂练习

 读入第三次作业第一部分爬虫得到的 csv文件，之后：
1. 绘制浏览次数曲线图；
2. 根据浏览次数进行降序排序，并打印前10篇报道的日期、标题； 
3. 求浏览次数为1的报道的链接并打印结果；
4. 统计每一年的报道数量，以及每一年的总的浏览次数，并分别以 柱状图的形式绘制出来；
5. 统计2019年每个月的报道数量并以饼图的形式绘制出来。

### 思路

1. 首先加载库文件：

   ```python
   import pandas as pd
   import matplotlib.pyplot as plt
   ```

2. 接下来读取csv文件，考虑到后面的几项要求，把日期作为DatetimeIndex的索引比较好：

   ```python
   reports = pd.read_csv('reportsInfo.csv', header=0, index_col=0, parse_dates=True)
   ```

3. 第一个要求是绘制浏览次数的折线图，因为原本的csv文件时间是先2020年再2019年然后2018年这样倒着来的，所以先重新排序，再取浏览次数那一列的数值来画图：

   ```python
   reports.sort_index()['浏览次数'].plot(linestyle='-', linewidth=2, color='steelblue')
   plt.title('浏览次数折线图', fontproperties='Kaiti')
   plt.xlabel('日期', fontproperties='Kaiti')
   plt.ylabel('浏览次数', fontproperties='Kaiti')
   plt.tight_layout()
   plt.savefig('reports_linechart', dpi=300)
   plt.show()
   ```

4. 根据浏览次数降序排序的操作和上一步索引排序差不多，然后切片取前10，用loc定位到标题那一列，输出即可：

   `print(reports.sort_values(by='浏览次数', ascending=False)[0:10].loc[:, ['标题']])`

5. 筛选浏览次数为1的链接，用下标的方式找即可：

   `print(reports[reports.浏览次数 == 1].链接)`

6. 统计每年的报道数量，需要先按年来分组，然后得到分组的size，对size画图即可：

   ```python
   reports_group = reports.groupby(reports.index.year)
   reports_group.size().plot.bar()
   plt.title('年报道数量柱状图', fontproperties='Kaiti')
   plt.xlabel('年份', fontproperties='Kaiti')
   plt.ylabel('报道数量', fontproperties='Kaiti')
   plt.tight_layout()
   plt.xticks(rotation=0)
   plt.savefig('reports_bar_1', dpi=300)
   plt.show()
   ```

7. 统计每年的总浏览次数，在上一条分组的基础上对浏览次数那一列求和，然后画图即可：

   ```python
   reports_group['浏览次数'].sum().plot.bar()
   plt.title('年浏览次数柱状图', fontproperties='Kaiti')
   plt.xlabel('年份', fontproperties='Kaiti')
   plt.ylabel('浏览次数', fontproperties='Kaiti')
   plt.tight_layout()
   plt.xticks(rotation=0)
   plt.savefig('reports_bar_2', dpi=300)
   plt.show()
   ```

8. 统计2019年每月的报道数量，在之前的分组中可以得到2019的分组，然后对2019部分再按月来分组，得到size，对size画图即可：

   ```python
   reports_2019 = reports_group.get_group(2019)
   reports_2019_month = reports_2019.groupby(reports_2019.index.month).size()
   reports_2019_month.name = ''
   reports_2019_month.plot.pie(startangle=90)
   plt.title('2019年每月报道数量饼状图', fontproperties='Kaiti')
   plt.savefig('reports_pie', dpi=300)
   plt.show()
   ```