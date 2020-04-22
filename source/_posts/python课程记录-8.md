---
title: python课程记录-8
date: 2020-04-17 11:59:29
tags: python
category: 课程笔记
---

这一课讲文件读写。

<!--more-->

## 文件

1. 文件类型：文本文件（纯文本、源代码、网页）、二进制文件（图片、word、excel、ppt）

2. 读文本文件：

   ```python
   with open(r'/path/to/file') as f:
       s = f.read()
   print(s)
   ```

   这样用`with open as`可以不用写`close`

3. open函数：`open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)` 返回一个文件对象，是可迭代对象

4. tell函数：`f.tell()`返回文件指针当前在的位置

5. 文本文件操作：

   以下都是写在`with open`的代码块里面

   1. 读文本文件：
      1. `f.readline()`：读一行，到换行符结束，返回字符串
      2. `f.read(2)`：接着读2个字符，返回字符串
      3. `f.readlines()`：把剩下的都读完，返回列表，每一行作为列表中的一个元素
   2. 写文本文件：
      1. `f.write`：写入字符串，不换行，可以在字符串末尾加`\n`来实现换行
      2. `f.writelines`：写入列表元素，不换行，可以在要换行的元素末尾加`\n`
   3. 文件模式相关：
      1. 用新建文件的模式（`x`）来open文件，如果存在同名文件，则会报错
      2. 用`w`方式写文件，如果存在同名文件，会清空原有内容再写入
      3. 用`a`方式写文件，存在同名文件的时候，不会清空内容，而是在文件末尾写入新内容
      4. 用`r+`的方式读写文件，默认位置为文件开头，读完以后移到结尾
      5. 用`w+`的方式读写文件，清空文件内容以后读写
      6. 用`a+`的方式读写文件，默认位置是文件末尾
      7. 带+的都是可读可写，不带+的则只有读或者写
   4. 改变文件读写位置：`seek(offset, whence=0)`，从whence(0起始，1当前，2末尾)偏移offset个字节。例如`f.seek(0)`可以把文件指针放到最开始的地方。
   5. 字符字节说明：utf-8编码中文3字节英文1字节；gbk编码中文2字节英文1字节

6. 二进制文件操作：

   1. open函数的打开模式为`b`

7. CSV文件：文本文件

   1. 由若干条记录组成，每条记录由字段组成，字段间由分 隔符分开，最常见的分隔符是逗号，也可以是制表符、分号等
   2. 列表读写：`csv.reader`和`csv.writer`
   3. 字典读写：`csv.DictReader`和`csv.DictWriter`
   4. ` csv.reader(csvfile, dialect=‘excel’, **fmtparams)`：以字符串列表的形式读入文件内容，一行是一个列表
      1. 是可迭代对象，可以通过`next`访问，比如读完文件先用next把第一行存在标题变量里，再遍历剩下的内容
   5. `csv.writer(csvfile, dialect=‘excel’, **fmtparams) `：把字符串列表写入csv文件，注意先写入标题行，再写入剩下内容
   6. `csv.DictReader(csvfile, fieldnames=None, restkey=None, restval=None, dialect=‘excel’, *args, **kwds)`字典方式读入，标题是key，内容是value
      1. csv文件没有标题行时，可以设定列表指定标题行，然后`csv.DictReader(f,headers)`来建立内容和标题一一对应的字典
   7. `csv.DictWriter(csvfile, fieldnames, restval=‘’, extrsaction=‘raise’, dialect=‘excel’, *args, **kwds)` 要指定标题行和内容，在创建csv对象时就指定标题，接下来写入标题，最后写入内容

### 随堂练习

1. 读入《Declaration.txt》文件，该文件里存的是一篇英文文章 ，要求把这篇文章中的不重复单词（单词的不同时态以及单复数都 看成是不同的单词）进行排序后写入一个新的txt文件，并要求每行 5个单词，同时注意对齐。

   思路：

   1. 使用`with open`方式读入文件并把其中的单词存入列表；

   2. 借助`Counter`统计词频和排序

   3. 把排序之后的列表写入新文件

      代码如下：

      ```python
      import collections
      import re
      # 读入文件内容，把单词存入列表并返回
      def strtolist(name):
          with open (name,'r') as f:
              s = f.read()
          words = re.findall(r'[a-zA-Z]+',s)
          return words
      
      # 对列表中的单词按字母顺序排序并返回
      def sortword(words):
          list1 = collections.Counter(words)
          list2 = sorted(list1.items())
          list3 = []
          for key, value in list2:
              list3.append(key)
          return list3
      
      # 把列表中的元素按要求写入文件
      def writefile(lists):
          count=0
          with open('wordlist.txt', 'w') as f:
              for i in lists:
                  f.write('{:<16}'.format(i))
                  f.write('\t')
                  count+=1
                  if count % 5 == 0:
                      f.write('\n')
                      
      def main():
          words = strtolist('Declaration.txt')
          lists = sortword(words)
          writefile(lists)
      
      if __name__ == '__main__':
          main()
      ```

2. 读入存有姓名和成绩的 成绩单.csv 文件，计算最终加权成绩（ 小数点后保留两位，三个成绩的权重分别为0.22,0.18,0.6），并 把该成绩追到到原有信息中，保存为一个新的csv文件。

   思路：

   1. 读入csv文件，并为标题行添加“最终成绩”

   2. 计算平均分并添加到这一行的末尾，保留2位小数

   3. 把这一行的数据存到列表中

   4. 把刚才的内容逐行写入新的csv文件，注意这里要在之前那个文件关闭之前完成

      代码如下：

      ```python
      import csv
      import pandas
      with open ('成绩单.csv','r') as f:
          f_csv=csv.reader(f)
          headers = next(f_csv)
          headers.append('最终成绩')
          lists = []
          i=0
          for row in f_csv:
              avg = 0.22*int(row[1])+0.18*int(row[2])+0.6*int(row[3])
              row.append(avg)
              row[4]=str('{:.2f}'.format(row[4]))
              lists.append(row)
      
          with open('新成绩单.csv','w', newline='') as s:
              s_csv=csv.writer(s)
              s_csv.writerow(headers)
              for row in lists:
                  s_csv.writerow(row)    
      ```

      