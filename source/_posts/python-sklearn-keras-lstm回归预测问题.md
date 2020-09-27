---
title: python+sklearn+keras+lstm回归预测问题
date: 2020-07-24 07:38:36
tags: 
	- python
	- sklearn
	- keras
	- lstm
	- 回归
categories: 无分类项
---

因为接到了股票价格预测的任务，所以开始研究怎么写代码。实际上代码是网上现成的，并不需要自己研究算法和网络结构，复制粘贴再重新组合一下就好，所以难度不大。之前做过sklearn的分类问题，现在做回归，思路是差不多的。

<!--more-->

具体场景是：给定某时间段内的股票价格数据，预测接下来的走势；用同样的算法预测其他股票，看准确率是否有变化；老师的想法是对比国内外市场的差异，看是否在机器学习算法中也有体现。

## 导入包

这里先列一下所有导入的包，可能有一些实际没用上：

```python
import math
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib import style
from sklearn import preprocessing, svm
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from datetime import datetime as date
from sklearn import tree
from sklearn import linear_model
from sklearn import svm
from sklearn import neighbors
from sklearn import ensemble
from sklearn.ensemble import BaggingRegressor
from sklearn.tree import ExtraTreeRegressor
from sklearn.model_selection import cross_val_score
import quandl
from sklearn import metrics
#from pandas_datareader import data
import yfinance as yf
# 下面这几个是lstm用的
from sklearn.preprocessing import MinMaxScaler
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Dropout, LSTM
```

## 数据获取

1. 使用quandl获取数据：

   ```python
   import quandl
   import pandas as pd
   start = date(2000,1,1)
   end = date.today()
   quandl.ApiConfig.api_key = "gEyzpvmpXReFE8Z8TEps"
   stock_df = pd.DataFrame(quandl.get("WIKI/GOOGL", start_date=start, end_date=end))
   ```

   第一行导入包；

   第二行第三行设置要获取的数据时间范围

   第四行设置api_key，这里是需要到quandl官网注册账户，注册好以后会得到这个key，可以用免费数据。注册的时候分为3步，填名字邮箱密码啥的，第三步的时候会需要点一个人机验证的东西才能注册成功，如果没出现人机验证且无法点注册的按钮，说明需要翻墙。

   第五行通过`quandl.get()`函数可以得到所需数据，转成`pandas`格式方便后续分析。这里的`WIKI/GOOGL`是谷歌的股票数据在quandl网站上的代码，不过我看不懂那个网站，不清楚要怎么找其他公司和时间的数据，所以暂时就先只用这个了。

2. 通过tushare和pandas_datareader这两个模块也可以获取数据，此外还有其他相关网站。

3. 发现pandas_datareader实际上不太行，查了一下找到了新的数据，数据来源是雅虎财经：

   ```python
   import yfinance as yf
   start_date = "2000-01-01"
   end_date = "2018-12-01"
   stock_df = yf.download(tickers = "MCD", start = start_date, end = end_date)
   ```

   这里`start_date`和`end_date`也可以写成前面`date(2000,1,1)`的样子，`tickers`的参数是可以百度到的股票代码，如果是上海的就是`600673.SS`这种代码后面加`.SS`，深圳是`.SZ`，香港是`.HK`，美国就是那串字母本身，此外，香港的代码查到的都是五位数，但是在这里要把最高位的0去掉，只用四位数。

   `yf.download`会直接返回`pandas`的`dataframe`结构，方便后续处理。

   和前面那个`quandl`的比起来，简单了很多，但是有时候会运行很慢，等十几分钟也不出结果。

## sklearn的普通方法

1. 把预测要用的数据列提取出来

   ```python
   stock_df = stock_df[['Open', 'High', 'Low', 'Close', 'Volume']]
   ```

2. 这里使用过去一天的数据来预测当天的收盘价，因此要新建一列来存下一天的收盘价作为机器学习的目标值`y`

   ```python
   stock_df['object'] = stock_df['Close'].shift(-1)
   ```

3. 把除了目标列以外的其他数据单独提出来作为`X`，并做标准化处理，然后去掉最后一行（因为最后一行没有下一天的目标值，所以没法用）

   ```python
   X = np.array(stock_df.drop(['object'], 1))
   X = preprocessing.scale(X)
   X = X[:-1]
   ```

4. 把目标列提出来作为`y`

   ```python
   stock_df.dropna(inplace=True)
   y = np.array(stock_df['object'])
   ```

5. 划分训练集和测试集

   ```python
   x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=1)
   ```

6. 把要用的模型都准备好

   ```python
   models = []
   models.append(('DecisionTree', tree.DecisionTreeRegressor()))
   models.append(('LR', linear_model.LinearRegression()))
   models.append(('KNN', neighbors.KNeighborsRegressor()))
   models.append(('RF', ensemble.RandomForestRegressor(n_estimators=20)))
   models.append(('ABR', ensemble.AdaBoostRegressor(n_estimators=50)))
   models.append(('SVM', svm.SVR(gamma='auto')))
   models.append(('GBRT', ensemble.GradientBoostingRegressor(n_estimators=100)))
   models.append(('Bagging', BaggingRegressor()))
   models.append(('ExtraTree', ExtraTreeRegressor()))
   ```

7. 遍历所有模型，在训练集上10折交叉验证并输出模型评价，在测试集上计算均方根误差

   ```python
   for name, model in models:
       scores = cross_val_score(model, x_train, y_train, cv=10, scoring=None)
       print('%s: %f (%f)' % (name, scores.mean(), scores.std()))
       model.fit(x_train, y_train)
       #print(model.score(x_test, y_test))
       y_predict = model.predict(x_test)
       print('RMSE: ', np.sqrt(metrics.mean_squared_error(y_test,y_predict)))
   ```

8. 接下来要随便选个模型画图看看

   1. 选模型并用训练集训练模型

      ```python
      clf = ensemble.GradientBoostingRegressor(n_estimators=100)
      clf.fit(x_train, y_train)
      ```

   2. 从所有的X中，选取后30%，用模型预测结果

      ```python
      index = len(X)*7//10
      predict_x = X[index:]
      predict_y = clf.predict(predict_x)
      ```

   3. 在原本的`dataframe`中新建一列来存这个预测结果，这样后面画图能直接用

      ```python
      stock_df['Predict'] = np.nan			#新建一列并初始化为空值
      for i in predict_y:						#遍历预测结果
          stock_df['Predict'][index] = i		#从预测的第一个数开始填值
          index += 1							#移动到下一个值
      ```

   4. 画图

      ```python
      plt.plot(stock_df['Close'], label='Close', color='deepskyblue')	#实际值
      stock_df['Predict'].plot()										#预测值
      plt.legend(loc=4)
      plt.xlabel('Date')
      plt.ylabel('Price')
      plt.show()
      ```

## lstm

1. 预处理

   ```python
   stock_df['Date'] = stock_df.index
   data = stock_df.sort_index(ascending=True, axis=0)
   new_data = pd.DataFrame(index=range(0, len(stock_df)), columns=['Date', 'Close'])
   for i in range(0, len(data)):
       new_data['Date'][i] = data['Date'][i]
       new_data['Close'][i] = data['Close'][i]
   # setting index
   new_data.index = new_data.Date
   new_data.drop('Date', axis=1, inplace=True)
   ```

   这样的结果就是把日期和收盘价单独提出来了

2. 划分训练集和测试集，是7:3划分

   ```python
   dataset = new_data.values
   t_len = len(dataset)*7//10
   train = dataset[0:t_len, :]
   valid = dataset[t_len:, :]
   ```

3. 处理训练集数据，lstm会利用过去一段时间的数据，这里设置为过去60天

   ```python
   #标准化处理
   scaler = MinMaxScaler(feature_range=(0, 1))
   scaled_data = scaler.fit_transform(dataset)
   #定义列表存放数据
   x_train, y_train = [], []
   #对于每一天的y，x的值都是过去60天的收盘价
   for i in range(60, len(train)):
       x_train.append(scaled_data[i - 60:i, 0])
       y_train.append(scaled_data[i, 0])
   #把x的格式改成训练需要的格式
   x_train, y_train = np.array(x_train), np.array(y_train)
   x_train = np.reshape(x_train, (x_train.shape[0], x_train.shape[1], 1))
   ```

4. 建立和训练lstm模型，这里训练50轮，实际上感觉100轮和50轮没什么差别；这里用了4层lstm，但实际上感觉一层就够了。在训练时用均方根误差作为指标。

   ```python
   # create and fit the LSTM network
   model = Sequential()
   model.add(LSTM(units=50, return_sequences=True, input_shape=(x_train.shape[1], 1)))
   model.add(Dropout(0.2))
   model.add(LSTM(units = 50, return_sequences = True))
   model.add(Dropout(0.2))
   model.add(LSTM(units = 50, return_sequences = True))
   model.add(Dropout(0.2))
   model.add(LSTM(units=50))
   model.add(Dropout(0.2))
   model.add(Dense(1))
   model.compile(loss='mean_squared_error', optimizer='adam')
   model.fit(x_train, y_train, epochs=50, batch_size=32, verbose=2)
   ```

   需要注意的是，这些lstm模型用的激活函数是默认的`tanh`，然后训练时会有错误信息，据一些人说是可以忽略的，参见[链接](https://github.com/tensorflow/tensorflow/issues/30263)。如果把激活函数改成`sigmoid`，就没报错了，但是发现训练结果格外不好，不清楚是我操作问题还是这个激活函数就不合适。总之暂且先用`tanh`。

5. 准备测试集，这里和训练集一样，往前倒60个数据

   ```python
   inputs = new_data[len(new_data) - len(valid) - 60:].values
   inputs = inputs.reshape(-1, 1)
   inputs = scaler.transform(inputs)
   X_test, Y_test = [], []
   for i in range(60, inputs.shape[0]):
       X_test.append(inputs[i - 60:i, 0])
   X_test = np.array(X_test)
   X_test = np.reshape(X_test, (X_test.shape[0], X_test.shape[1], 1))
   ```

6. 用训练好的lstm模型对测试集进行测试并把值改回标准化前的值

   ```python
   closing_price = model.predict(X_test)
   closing_price = scaler.inverse_transform(closing_price)
   ```

7. 输出均方根误差

   ```
   rms = np.sqrt(np.mean(np.power((valid - closing_price), 2)))
   print(rms)
   ```

8. 和sklearn一样的画图

   ```python
   train = new_data[:t_len]
   valid = new_data[t_len:]
   valid['Predictions'] = closing_price
   plt.plot(train['Adj. Close'], color='deepskyblue', label = 'Adj. Close')
   plt.plot(valid['Adj. Close'], color='deepskyblue')
   plt.plot(valid['Predictions'], label = 'Predict')
   plt.legend(loc=4)
   plt.xlabel('Date')
   plt.ylabel('Price')
   plt.show()
   ```

## 其他说明

1. 之后有空的时候再增加这些算法的理论介绍
2. 回归和分类的代码结构差不多，只是函数名称和模型评价指标不太一样，关于评价指标可以参考[这个](https://scikit-learn.org/stable/modules/model_evaluation.html#the-scoring-parameter-defining-model-evaluation-rules)。

