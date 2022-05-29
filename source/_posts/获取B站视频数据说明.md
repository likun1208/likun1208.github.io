---
title: 获取B站数据说明
date: 2022-05-29 11:01:05
tags:
     - python
categories: 无分类项
---

# 获取B站数据说明

## 用到的包

1. `bilibili_api`：必用，获取视频数据的接口

2. `json`：选用，用于格式化输出获取的字典数据

3. `time`：选用，用于转换获取的时间戳数据

4. `csv`：选用，用于将数据输出到csv文件

5. 其他说明：如果需要更高级的表格数据输出，建议用pandas

## 获取数据

通过下列代码可以获取指定`bv`号的视频数据：

```python
v = video.Video(bvid)        # 实例化video类
info = await v.get_info()    # 获取信息
```

`info`是一个字典类型的数据，包含了视频的标题、作者、播放量等等大量信息，我们可以用以下代码格式化输出来看一下：

```python
print(json.dumps(info,indent=4,ensure_ascii=False))    # 格式化输出
```

输出结果如下图，这个字段信息很多，就不一一列出说明了。

![](https://s2.loli.net/2022/05/29/oHEL1jBekdJzXZ2.png)

![](https://s2.loli.net/2022/05/29/IVlywznM7u9cdJK.png)

目前我们需要获取的是标题、发布时间、播放量和三连数据，可以看出，标题对应的字段是`title`，发布时间是`pubdate`，而播放量和三连数据则是在`stat`中的`view, favorite, coin, like`，需要注意的是这里发布时间是用的时间戳，因此要使用`time`来转换成标准时间。总的来说，就是以下代码：

```python
title = info['title']                                    # 获取标题
view = info['stat']['view']                              # 获取播放量
like = info['stat']['like']                              # 获取点赞
coin = info['stat']['coin']                              # 获取投币
favorite = info['stat']['favorite']                      # 获取收藏
time_tmp = info['pubdate']                               # 获取发布时间
timeArray = time.localtime(time_tmp)                     # 时间戳转为标准时间的数组形式
time_pub= time.strftime("%Y-%m-%d %H:%M:%S", timeArray)  # 时间数组转为常见的可直接输出的字符串形式
```

在得到数据以后，可以直接打印输出、写入本地文件或者进行后续其他需要的操作，这里我们要把数据作为函数返回值，因此把它们放到一个列表中，从而一起返回，如下：

```python
videoinfo = [title, time_pub, view, like, coin, favorite]
return videoinfo
```

获取数据的部分到此结束，需要注意的是，由于最初获取数据用到了`awati`，因此该函数是异步的，在声明的时候需要用如下形式：

```python
async def main(bvid):
    ...
```

接下来是如何把数据写入文件。

## 写入文件

首先是设置`bv`号，然后异步地调用前面写好的函数：

```python
bvid = "BV1MR4y1K7Tv"
info = asyncio.get_event_loop().run_until_complete(main(bvid))
```

如果要连续获取多个视频的信息，就可以直接循环。

接下来由于输出的文件格式比较特殊，所以要对获取到的数据列表进行处理，如下：

```python
list = [None,None,info[0],"https://www.bilibili.com/video/"+bvid,info[1],None,None,info[2],None,None,None,None,str(info[3])+'/'+str(info[4])+'/'+str(info[5])]
```

这行代码的含义是把列表的前两个字段设置为空，接着是`info`中的第一个数据即标题，随之是视频链接，再次是投稿时间，接着继续两个空，然后是播放量，在四个空位之后是`点赞/投币/收藏`这种形式的三连数据。之所以置空，是因为要写入表格中在这些位置有其他列，需要用其他方式写入数据。

数据按格式排好后就可以写入了：

```python
with open("bilibili.csv", 'a', newline='', encoding='utf-8-sig') as f:
    f_csv = csv.writer(f)
    f_csv.writerow(list)
```

第一行表示以追加写入的方式打开`bilibili.csv`这个文件，文件编码是`utf-8-sig`，`a`表示是追加写入，也就是在文件最后一行新增要写入的内容，`newline`表示换行符为空，最后的`as f`表示把打开的这个文件变量命名为`f`，这个名字只影响后面对文件变量的操作，不影响文件名本身。

第二行是调用函数表示要对文件`f`进行写入操作，第三行是指将名为`list`的列表作为新的一行直接写入到csv文件中。

写入文件以后就结束。 

## 参考

[bilibili-api 开发文档](https://bili.moyu.moe/#/)
