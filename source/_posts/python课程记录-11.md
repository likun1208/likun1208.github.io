---
title: python课程记录-11
date: 2020-05-12 15:03:39
tags: python
category: 课程笔记
---
这一课是matplotlib画图的介绍。

`import matplotlib.pyplot as plt`

<!--more-->

## 基础绘图

1. `plt.plot([x坐标],[y坐标])`设置图像坐标

   x坐标如果是`[1,2,3,4]`这样连贯的，可以省略。

2. `plt.show()`显示图像，`plt.sacefig('文件名', dpi=300)`保存图像为png文件。

3. 多条曲线：`plt.plot(a,a,a,a*2,a,a**2)`，`a`是numpy数组。

## 设置图像属性

1. 颜色和线宽：`plt.plot(x, y, format_string, **kwargs) `，其中`format_string`设定颜色、风格和标记。
2. 坐标轴范围：
   1. 获取坐标范围：`xlim(),ylim(),axis()`
   2. 设置坐标范围：`xlim(xmin,xmax), ylim(ymin,ymax), axis([xmin,xmax,ymin,ymax])`
3. 设置坐标轴刻度：`plt.xticks(),plt.yticks(0`)
4. 设置坐标轴标签和图的题目：`xlabel(), ylabel(),title()`
5. 添加图例：`legend()`
6. 添加文字注释：`text()`
7. 标注：`ax.annotate()`
8. 网格：`plt.grid(True)`，可以设置颜色、线型等等

## 中文显示问题

1. `rc_params()`指定字体：`matplotlib.rcParams['font.family']='STSong'`
2. 在用到的地方指定字体：`fontproperties='Kaiti'`

## 绘制子图

1. `subplot(numRows, numCols, plotNum)`
2. `subplot2grid(shape, loc, rowspan=1, colspan=1)`
3. `gridspec.GridSpec()`和`subplot()`结合

## 绘制多个图

`figure()`

## 常用图标函数

1. 散点图：`plt.scatter()`
2. 柱状图：`plt.bar()`
3. 直方图：`plt.hist()`
4. 饼图：`plt.pie()`
5. 等值线图：`plt.contour()`
6. 极坐标图：`plt.plot()`
7. 矢量场流线图：`plt.streamplot()`
8. 三维绘图：`Axes3D(fig)`或者`fig.add_subplot()`

## 课后作业

 IBM.csv和BABA.csv分别保存了IBM和阿里巴巴股票信息，请读入它们的日期和收盘价，绘制收盘价曲线图，要求：
1. 设置横坐标和纵坐标轴标签、图的题目，另外横坐标轴刻度标签为日期；
2. 对每一支股票求最高收盘价和最低收盘价，并在曲线上对这两个点进行标注。

### 思路

1. 导入所需库文件：

   ```python
   import numpy as np
   import matplotlib.pyplot as plt
   import matplotlib.ticker as ticker
   ```

2. 首先是读入csv文件，因为日期是字符串，所以这里先统一用字符串的格式读入：

   ```python
   ibm = np.loadtxt("IBM.csv", dtype=np.str, delimiter=',')
   baba = np.loadtxt("BABA.csv", dtype=np.str, delimiter=',')
   ```

3. 接下来提取所需数据：日期、收盘价、最高和最低收盘价，这里使用`astype()`把收盘价从numpy字符串转为numpy浮点数：

   ```python
   # IBM
   date_ibm = ibm[1:, 0]
   close_ibm = ibm[1:, 4].astype(np.float)
   close_ibm_u = close_ibm.max()
   date_ibm_u = date_ibm[np.where(close_ibm == close_ibm_u)]
   close_ibm_l = close_ibm.min()
   date_ibm_l = date_ibm[np.where(close_ibm == close_ibm_l)]
   # BABA
   date_baba = baba[1:, 0]
   close_baba = baba[1:, 4].astype(np.float)
   close_baba_u = close_baba.max()
   date_baba_u = date_baba[np.where(close_baba == close_baba_u)]
   close_baba_l = close_baba.min()
   date_baba_l = date_baba[np.where(close_baba == close_baba_l)]
   ```

4. 得到数据以后就开始画图：

   ```python
   # 画曲线
   plt.plot(date_ibm, close_ibm, color="green", linewidth=0.5, label='IBM')
   plt.plot(date_baba, close_baba, color="blue", linewidth=0.5, label='BABA')
   # 设置x轴密度和刻度大小方向
   plt.gca().xaxis.set_major_locator(ticker.MultipleLocator(80))
   plt.xticks(rotation=45, size=6)
   # 设置标题和轴标签内容及字体
   plt.title('2014年以来IBM和BABA股票收盘价随日期的变换', fontproperties='Kaiti')
   plt.xlabel('日期', fontproperties='Kaiti')
   plt.ylabel('收盘价', fontproperties='Kaiti')
   # 设置图例位置
   plt.legend(loc='upper center')
   # 在四个最高最低点加圆圈标记
   plt.plot(date_ibm_u,close_ibm_u,'ro',markersize=3)
   plt.plot(date_ibm_l,close_ibm_l,'ro',markersize=3)
   plt.plot(date_baba_u,close_baba_u,'ro',markersize=3)
   plt.plot(date_baba_l,close_baba_l,'ro',markersize=3)
   # 给四个最高最低点加文字说明
   plt.annotate(r'最高'+str(close_ibm_u),color='r',xy=(date_ibm_u,close_ibm_u),xytext=(-17,15),textcoords='offset points',fontproperties='Kaiti',fontsize=5,arrowprops={'arrowstyle':'->','color':'r'})
   plt.annotate(r'最低'+str(close_ibm_l),color='r',xy=(date_ibm_l,close_ibm_l),xytext=(-17,-15),textcoords='offset points',fontproperties='Kaiti',fontsize=5,arrowprops={'arrowstyle':'->','color':'r'})
   plt.annotate(r'最高'+str(close_baba_u),color='r',xy=(date_baba_u,close_baba_u),xytext=(-50,6),textcoords='offset points',fontproperties='Kaiti',fontsize=5,arrowprops={'arrowstyle':'->','color':'r'})
   plt.annotate(r'最低'+str(close_baba_l),color='r',xy=(date_baba_l,close_baba_l),xytext=(-50,-6),textcoords='offset points',fontproperties='Kaiti',fontsize=5,arrowprops={'arrowstyle':'->','color':'r'})
   ```

5. 最后显示和保存图像：

   ```python
   plt.tight_layout()
   plt.savefig('homework', dpi=300)
   plt.show()
   ```

   注意：在一开始的时候可以设置图像尺寸：`plt.figure(figsize=(1080/100,520/100),dpi=100)`