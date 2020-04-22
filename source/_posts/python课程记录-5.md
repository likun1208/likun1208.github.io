---
title: python课程记录-5
date: 2020-03-31 13:29:20
tags: python
category: 课程笔记
---

这一课讲的是字典和集合。

<!--more-->

### 课堂练习

1. 对于下列财经数据`lst`，如何构造公司代码和股票价格的字典`d`？

   ```python
   lst=[('AXP','American Epress Company','86.40'),
   	('BA','The Boeing Company','22.64'),
   	('CAT','Caterpillar Inc.','99.44'),
   	('CSCO','Cisco System Inc.','23.78'),
   	('CVX','Chevron Corporation','115.91')]
   d={'AXP':'86.40','BA':'122.64','CAT':'99.44','CSCO':'23.78','CVX':'115.91'}
   ```

   答案：从列表构造字典，根据定义可以直接使用`d={a:c for a,b,c in lst}`

2. 如何把一个字典中的`key`和`value`互换？

   ```python
   d={'blue':500, 'green':300, 'red':100}
   {100:'red', 300:'green', 500:'blue'}
   ```

   答案：使用`items`属性即可，`new_d={value:key for key, value in d.items()}`

### 课后作业

1. 利用内置数据类型对李白和杜甫的诗歌进行分析，包括：

(1)读入李白.txt，得到李白诗歌内容中的所有的汉字，并进一步得到其中的不重复汉字，最后打印所有汉字的个数、不重复汉字的个数、以及不重复汉字，打印汉字时要求每行10个汉字，汉字之间以2个空格分开。

(2)进一步利用字典进行李白诗歌的字频统计，即统计每个字出现的次数，并对字频进行归一化处理（即除以诗歌篇数），然后输出显示字频统计结果，输出显示时根据字频降序排列，只打印频率大于等于0.1的结果，并注意对齐。

(3)采用同上述相同的方法，得到杜甫诗歌的字频统计结果。在此基础上获得李白和杜甫的高频词集合（即频率大于等于0.1的汉字集合），利用集合操作进一步获得两位诗人都爱用的字、李白爱用但杜甫不爱用的字以及杜甫爱用而李白不爱用的字，并输出上述结果，输出时每行10个单词，汉字之间以2个空格分开。

说明：统计时不考虑题目、朝代、作者，只考虑诗歌内容。

作业思路：

首先是要读取txt文件，这里作业提示说用下述代码来读取文件：

```python
with open('李白.txt', encoding='utf-8') as f:
    poems_text = f.read()
```

此时整个文件的内容作为字符串存在`poems_text`中，并且每首诗之间有换行符，因此使用`poems_list = poems_text.splitlines()`将每首诗作为列表`poems_list`中的元素（字符串）存储。

接下来遍历列表元素，因为每首诗格式都是“标题 朝代 作者 诗歌内容”这样，所以对每个字符串查找作者名字，并只留存作者名字之后的字符串内容，就可以得到我们需要统计的诗歌内容，再去掉中文标点、英文标点和空格，就得到了一个处理好的诗歌内容列表，这部分代码如下：

```python
    for i in range(len(poems_list)):
        index = poems_list[i].find(name)
        poems_list[i] = poems_list[i][index + 3:]
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', zhon.hanzi.punctuation))
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', string.punctuation))
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', ' '))
```

这时`poems_num = len(poems_list)`即可得到诗歌总数，`poems_text = ''.join(poems_list)`即可得到处理好之后的字符串。然后进行统计。

我们使用`poems_counter = collections.Counter(poems_text)`可以得到一个`Counter`计数器，该计数器是一个字典，`key` 是字，`val`是该字出现的次数，按出现次数遍历这个计数器，并把`key`加入到集合`poems_set`中，当字频（`val/poems_num`）低于0.1时，break结束遍历，这样就得到了高频字的集合，这部分代码如下：

```python
    for key, val in poems_counter.most_common():
        if val / poems_num < 0.1:
            break
        poems_set.add(key)
```

接下来通过集合运算就可以得到李白和杜甫的交集和差集了。

关于格式化输出，首先是输出诗歌本身的字数、不重复字数和不重复字，每10个换行，字中间是两个空格，代码如下：

```python
print(name + '诗词中总共有' + str(len(poems_text)) + '个汉字，其中不重复的汉字有' + str(len(poems_counter)) + '个，具体如下：')
print_num = 0
for key in poems_counter:
    print(key, end='  ')
    print_num += 1
    if print_num % 10 == 0:
        print()
print()
```

接下来是字频统计的输出，代码如下：

```python
print('字频统计信息如下：\n汉字---频率')
for key, val in poems_counter.most_common():
    if val / poems_num < 0.1:
        break
    print('{}\t {}'.format(key, val / poems_num))
```

最后是字频集合的输出，代码如下：

```python
print_num = 0
for i in set:
    print(i, end='  ')
    print_num += 1
    if print_num % 10 == 0:
        print()
print()
```



整体代码我把诗歌的预处理部分作为一个函数、诗歌输出作为一个函数、集合输出作为一个函数，最后主函数调用它们。如下：

```python
import string
import collections
import zhon.hanzi


def poems(name):
    with open(name + '.txt', encoding='utf-8') as f:
        poems_text = f.read()
    poems_list = poems_text.splitlines()
    for i in range(len(poems_list)):
        index = poems_list[i].find(name)
        poems_list[i] = poems_list[i][index + 3:]
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', zhon.hanzi.punctuation))
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', string.punctuation))
        poems_list[i] = poems_list[i].translate(str.maketrans('', '', ' '))
    poems_num = len(poems_list)
    poems_text = ''.join(poems_list)
    poems_counter = collections.Counter(poems_text)
    poems_set = set()
    for key, val in poems_counter.most_common():
        if val / poems_num < 0.1:
            break
        poems_set.add(key)
    return poems_num, poems_text, poems_counter, poems_set


def print_poem(name, poems_num, poems_text, poems_counter):
    print(name + '诗词中总共有' + str(len(poems_text)) + '个汉字，其中不重复的汉字有' + str(len(poems_counter)) + '个，具体如下：')
    print_num = 0
    for key in poems_counter:
        print(key, end='  ')
        print_num += 1
        if print_num % 10 == 0:
            print()
    print()
    print('字频统计信息如下：\n汉字---频率')
    for key, val in poems_counter.most_common():
        if val / poems_num < 0.1:
            break
        print('{}\t {}'.format(key, val / poems_num))
    return


def print_set(set):
    print_num = 0
    for i in set:
        print(i, end='  ')
        print_num += 1
        if print_num % 10 == 0:
            print()
    print()


num_libai, text_libai, counter_libai, set_libai = poems('李白')
num_dufu, text_dufu, counter_dufu, set_dufu = poems('杜甫')
print_poem('李白', num_libai, text_libai, counter_libai)
print_poem('杜甫', num_dufu, text_dufu, counter_dufu)
s_both_love = set_libai & set_dufu
s_only_libai = set_libai - set_dufu
s_only_dufu = set_dufu - set_libai
print('两位诗人都爱用的字有' + str(len(s_both_love)) + '个，这些字为：')
print_set(s_both_love)
print('李白爱用但杜甫不爱用的字有'+str(len(s_only_libai))+'个，这些字为：')
print_set(s_only_libai)
print('杜甫爱用但李白不爱用的字有'+str(len(s_only_libai))+'个，这些字为：')
print_set(s_only_dufu)
```

