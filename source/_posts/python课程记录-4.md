---
title: python课程记录-4
date: 2020-03-23 08:51:26
tags: python
category: 课程笔记
---

这一课是讲字符串的，主要是几种字符串函数。

<!--more-->

### 课堂练习

1. 下面代码的运行结果是（）

   ```python
   s=[1,2,3,4,5]
   for i in s:
   	print(i)
   	s.remove(i)
   ```

   A. 1 2 3 4 5（一个数字一行）

   B. 1 3 5（一个数字一行）

   答案：显然这里`s`是个列表，列表的`remove()`函数的作用是移除指定元素，只移除匹配到的第一个。按顺序看，最初列表是`s=[1,2,3,4,5]`，`for`循环访问列表的第一个元素，并输出`1`，接着移除`1`，这之后列表`s`的元素会整体往前挪一位，变成`s=[2,3,4,5]`，此时第一个元素变成`2`，第二个元素变成`3`，而`for`循环会去找列表的第二个元素，也就是跳过`2`访问`3`，输出并移除，变成`s=[2,4,5]`，然后输出并移除第三个元素`5`。

   所以最后输出的是`1 3 5`，选B。

2. 下列操作的结果是（）

   ```python
   s='!..Hi!!!.'
   s.strip('!.')
   ```

   A. '.Hi!!!'		B. '.Hi!!!.'		C. '!..Hi!!'		D. 'Hi'

   答案：字符串的`strip`函数是移除字符串左右两边的指定字符元素，如果没指定就移除空格，然后遇到第一个不是指定元素的时候就停止移除操作，并且指定元素不会连起来看，也就是这里的`!`和`.`是会被看做两个元素，而不是一个完整的字符串，因此移除的时候并不是找连续的`!.`，而是移除左右两边所有`!`和`.`，也就是选D。

3. 用一行代码把字符串`s='  abcdbacbaab'`中的`b`去掉。

   答案：首先可以用字符串中的翻译函数，将所有`b`翻译成空字符，也就是`s.translate(str.maketrans('', '','b'))`。最直接的方法是用字符串的`replace`函数，将`b`替换成空字符，即`s=s.replace('b','')`。

4. 判断两个单词s1和s2是否构成字谜：即s1和s2的字母和个数相同，只是顺序不同，如cinema何iceman，要求只用一行代码。

   答案：如果不要求只用一行代码，那办法有很多了，现在要求只用一行代码，因此只能采用字符串的`sorted()`函数，该函数会返回一个排好序的字符串，因此可以通过`sorted(s1)==sorted(s2)`来判断两个字符串是否构成字谜。

5. 如何将`s='d,f,a,c,b,d,g,e,a,f'`中的字母进行排序，得到新的字符串`s='a,a,b,c,d,d,e,f,f,g'`？

   答案：直接来说就是要把原字符串中的`,`去掉，剩下的字符串按字母顺序排序，然后再把`,`加回来。

   ```python
   s='d,f,a,c,b,d,g,e,a,f' 
   s1=s.replace(',','') 
   s2=sorted(s1) 
   ','.join(s2)
   ```

   ### 课后练习

   1. 在第一课练习的基础上，判断一个字符串是否是回文串（规则更宽松）：

      例如：Madam, I'm Adam 				True

      A man, a plan, a canal, Panama!				True

      客上天然居，居然天上客.				True

      答案：我的思路是把字符串里所有的标点和空格去掉，然后把所有字母都转成小写字母，最后用之前写好的函数来判断字符串是否是回文串。 `string.punctuation`中存有所有标点符号，因此可以将字符串中所有的`string.punctuation`替换成空字符，从而去掉标点；空格同理；`casefold()`函数可以使字符串中所有的字符都变成小写。代码如下：

      ```python
      import string
      def is_palindromic4(num):
          return num == num[::-1]
      
      def del_space_punc(s):
          s_1=s.translate(str.maketrans('', '', string.punctuation))
          s_2=s_1.translate(str.maketrans('', '', ' '))
          s_3=s_2.casefold()
          return s_3
          
      s1 = "Madam, I'm Adam"
      s2 = "A man, a plan, a canal, Panama!"
      s3 = "客上天然居，居然天上客."
      
      print(is_palindromic4(del_space_punc(s1)))
      print(is_palindromic4(del_space_punc(s2)))
      print(is_palindromic4(del_space_punc(s3)))
      ```

   2. 输入一个字符串a和字符串b，查找b在a中的所有位置并输出显示。

      例如：请输入一个字符串：abcdeabcdeab

      请输入另一个字符串：ab

      输出0 5 10

      答案：直接一些的想法是遍历字符串检索`a`，然后看下一个字符是否是`b`，不过百度之后发现可以直接用正则匹配，代码如下：

      ```python
      import re
      a = input('请输入一个字符串：')
      b = input('请输入另一个字符串：')
      print([i.start() for i in re.finditer(b,a)])
      ```

   3. 统计一篇文章（看成一个长字符串）中单词的个数，以及文章中不同单词的个数及列表，最后输出显示。（备注：单词的不同时态 以及单复数都看成是不同的单词）。字符串为变量speech，`speech='''  Four score and seven years ago our fathers brought forth on this continent, a new nation, conceived in Liberty, and dedicated to the proposition that all men are created equal. Now we are engaged in a great civil war, testing whether that nation, or any nation so conceived and so dedicated, can long endure. We are met on a great battle-field of that war. We have come to dedicate a portion of that field, as a final resting place for those who here gave their lives that that nation might live. It is altogether fitting and proper that we should do this. But, in a larger sense, we can not dedicate -- we can not consecrate -- we can not hallow -- this ground. The brave men, living and dead, who struggled here, have consecrated it, far above our poor power to add or detract. The world will little note, nor long remember what we say here, but it can never forget what they did here. It is for us the living, rather, to be dedicated here to the unfinished work which they who fought here have thus far so nobly advanced. It is rather for us to be here dedicated to the great task remaining before us -- that from these honored dead we take increased devotion to that cause for which they gave the last full measure of devotion -- that we here highly resolve that these dead shall not have died in vain -that this nation, under God, shall have a new birth of freedom -- and that government of the people, by the people, for the people, shall not perish from the earth.'''`

      答案：受上一题的影响，这里也考虑用正则匹配来判断有多少个单词，并把单词放到一个列表中，再根据列表建立字典从而输出词频。统计词频可以用`collections.Counter`。

      ```python
      import collections
      import re
      speech='''  Four score and seven years ago our fathers brought forth on this continent, a new nation, conceived in Liberty, and dedicated to the proposition that all men are created equal. Now we are engaged in a great civil war, testing whether that nation, or any nation so conceived and so dedicated, can long endure. We are met on a great battle-field of that war. We have come to dedicate a portion of that field, as a final resting place for those who here gave their lives that that nation might live. It is altogether fitting and proper that we should do this. But, in a larger sense, we can not dedicate -- we can not consecrate -- we can not hallow -- this ground. The brave men, living and dead, who struggled here, have consecrated it, far above our poor power to add or detract. The world will little note, nor long remember what we say here, but it can never forget what they did here. It is for us the living, rather, to be dedicated here to the unfinished work which they who fought here have thus far so nobly advanced. It is rather for us to be here dedicated to the great task remaining before us -- that from these honored dead we take increased devotion to that cause for which they gave the last full measure of devotion -- that we here highly resolve that these dead shall not have died in vain -that this nation, under God, shall have a new birth of freedom -- and that government of the people, by the people, for the people, shall not perish from the earth.'''
      words=re.findall(r'[a-zA-Z]+',speech)
      count=len(words)
      print('单词出现次数：',count)
      list2 = collections.Counter(words)
      print(dict(list2))
      ```

      

   