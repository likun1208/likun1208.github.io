---
title: python课程记录-6
date: 2020-04-01 09:34:22
tags: python
category: 课程笔记
---

这一课讲第三方库的安装和使用，然后用`wordcloud`和`jieba`生成词云图和中文分词。没有课堂练习，只有一个在上次作业基础上的作业。

我是直接`pip`安装的，在安装`wordcloud`时，`pip install wordcloud`很快就装好了，但是`jieba`反复出错，最后指定清华镜像，`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple jieba`，这样就飞快地安装好了。

<!--more-->

### 课后作业

(4) 利用`wordcloud`结合`jieba`分别对李白（杜甫）诗歌内容中的字和词语进行可视化。

##### 思路

上节课的作业完成后已经得到了处理好的诗歌字符串和字频统计`Counter`，那么接下来就可以用这两个变量来得到对应的词云图。

用`fit_words()`函数根据已经得到的计数器`poems_counter`生成的字典来制作字频的词云图片，用`generate()`函数对用`jieba`进行分词之后的`poems_text`生成词频的词云图片。在保存图片后可以用`PIL`中的`Image.open()`和`show()`来显示图片。对应的函数代码如下：

```python
# 导入库
import jieba
import wordcloud
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt

def cloud_character(poems_counter, name):
    dict_character = dict(poems_counter)
    # 生成图像对应的遮罩，这样词云图就是按图像里非白色区域来填充的
    mask = np.array(Image.open(name + '.jpg'))
    # 根据图像生成颜色表，这样词云图的颜色就和图像一致了
    color = wordcloud.ImageColorGenerator(mask)
    # 生成词云图变量，背景白色，random_state的值影响字在图中的分布，color_func设定词云颜色，mask设定图像遮罩，font_path指定字体，不指定的话中文没法显示
    wc = wordcloud.WordCloud(background_color="white", random_state=42, color_func=color, mask=mask, font_path='C:\Windows\Fonts\simsun.ttc')
    # 根据字典生成词云图的内容
    wc.fit_words(dict_character)
    # 保存词云图
    wc.to_file(name + "cloud_character.png")
    return
def cloud_word(poems_text, name):
    text = ' '.join(jieba.cut(poems_text))
    mask = np.array(Image.open(name + '.jpg'))
    color = wordcloud.ImageColorGenerator(mask)
    wc = wordcloud.WordCloud(background_color="white", random_state=42, color_func=color, mask=mask, font_path='C:\Windows\Fonts\simsun.ttc')
    # 根据文本字符串生成词云图的内容
    wc.generate(text)
    wc.to_file(name + "cloud_word.png")
    return
def show_pic(name):
    # 打开图像
    img1 = Image.open(name + "cloud_character.png")
    img2 = Image.open(name + "cloud_word.png")
    # 显示图像
    img1.show()
    img2.show()
    return

# 主程序部分
cloud_character(counter_libai, 'libai')
cloud_character(counter_dufu, 'dufu')
cloud_word(text_libai, 'libai')
cloud_word(text_dufu, 'dufu')
show_pic("libai")
show_pic("dufu")
```

（最后生成的词云图是真的怪怪的......）