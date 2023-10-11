---
title: python课程记录-7
date: 2020-04-16 21:56:12
tags: python
category: 课程笔记
---
这一课讲python中函数和模块的使用。

<!--more-->

## 函数参数

1. 函数定义时可以为参数指定默认值，这样没传参的时候就会用默认值，要先声明没默认值的参数，再声明有默认值的。

2. 调用函数时可以直接指明参数值（`mysum1(score1 = 88, score2 = 79)`)，不指明的参数写前面，指明的放后面。

3. 指针（*）形式传递可变参数，以元组形式打包多出来的参数，例如：

   ```python
   def my_sum2(a,b,*c):
   	total=a+b
       for n in c:
           total=total+n
       return total
   
   print(my_sum2(1,2))				#c=()	输出3
   print(my_sum2(1,2,3,4,5))		#c=(3,4,5)	输出15
   print(my_sum2(1,2,3,4,5,10,20))	#c=(3,4,5,10,20)	输出45
   ```

4. 双指针（**）形式传递可变参数，以字典形式打包多出来的参数，例如：

   ```python
   def my_sum3(a,b,*c,**d):
   	total=a+b
       for n in c:
           total=total+n
       for key in d:
           total=total+d[key]
       return total
   
   print(my_sum3(1,2))							#c=() d={} 输出3
   print(my_sum3(1,2,3,4,5))					#c=(3,4,5) d={} 输出15
   print(my_sum3(1,2,3,4,5,male=6,female=7))	#c=(3,4,5) d={'male':6,'female':7} 输出28
   ```

5. *或**的参数如果不位于形参列表的最后位置，则会使其后面声 明的参数为强制命名参数；使用一个星号而不带参数，也可导致强制命名参数。

6. 列表、元组、字典可通过*和**作为参数传递给函数。

7. 形参复制了实参所指对象的引用

## 函数返回值

1. 返回多个值时，是元组形式。

## 变量作用域

1. 局部变量全局变量和其他语言基本一致

2. 只要在函数体内有赋值语句，不管在哪一行，这个变量就是局部变量，函数体内也只会用这个局部变量，而不是同名全局变量，例如下述代码会直接报错

   ```python
   var=1
   def fun():
       print(var)
       var=200
   fun()
   
   var=1
   def fun():
       var+=1
       print(var)
   fun()
   
   报错信息：local variable 'var' referenced before assignment
   ```

3. 函数体内用全局变量，要声明`global`

4. 函数嵌套函数时，内层函数变量作用域只在内层，不影响外层，如果要内层用外层的，要声明`nonlocal`

## 特殊函数

1. 递归

2. 函数可以作为其他函数的参数

3. lambda匿名函数：实现行内函数 `lambda 形参列表:返回的结果`

   例如：`f=lambda x,y: x+y`，`f(12,34)=46`

4. map函数：以把一个函数作用到可迭代对象（如列表）的每一个 元素。

   例如：

   ```python
   a=[-2,1,3]
   b=map(lambda x: x+2, a)
   print(b)	#b是一个可迭代对象，print得到它的地址，for循环输出可知b=0,3,5
   c=map(abs, a)
   c=list(c)
   print(c)	#c=[2,1,3]
   d=map(lambda x,y: x+y, a,c)
   print(list(d))	#输出[0,2,6]
   ```

5. filter函数：筛选出可迭代对象中满足条件的元素，`filter( 返回bool类型的函数, 可迭代对象) `

   ```python
   a=filter(lambda x: x>0, (-1,2,-3,0,5))
   print(list(a))		# [2,5]
   b=filter(str.isalpha, ('word',',','\n','$','1'))
   print(list(b))		# ['word']
   c=filter(None,(-1,2,-3,0,5))
   print(list(b))		# [-1,2,-3,5] 传入None时会把序列中的false值比如0、空值等都去掉
   ```

6. reduce函数：对可迭代对象的元素进行递归计算，`reduce( 带两个参数的函数, 可迭代对象) `

   ```python
   a=functools.reduce(operator.add,[1,3,5,7,9])
   print(a)	#25
   b=functools.reduce(lambda x,y:x+y,[1,3,5,7,9])
   print(b)	#25
   ```



### 课后练习

1. 对第二次作业进行函数封装，按功能封装成几个函数。

   这个没啥说的，本来就是用函数写的。

2. 利用map、filter、reduce、lambda函数，对一个元素为数字 字符串的列表，分别求出其中包含的所有奇数的乘积以及所有 偶数的乘积。

   思路：首先要把列表`l`的元素从字符串转成整数，从而才能进行奇偶性判断和乘法运算，这里用`map`函数来完成；接下来用`filter`函数就可以把奇数和偶数分别放到两个列表中，最后用`reduce`函数即可进行乘法运算，代码如下：

   ```python
   import functools
   l=['1','2','3','14','20','5','7','39','150','6']
   m=list(map(lambda x: int(x), l))
   a=filter(lambda x: x%2==0, m)
   b=functools.reduce(lambda x,y:x*y,list(a))
   c=filter(lambda x: x%2==1, m)
   d=functools.reduce(lambda x,y:x*y,list(c))
   print('偶数乘积是',b)
   print('奇数乘积是',d)
   ```

3. 第一次作业第三题打印结果为24的表达式，我们通过嵌套循环 得到列表的全排列，试试采用递归函数求全排列。

   思路：

   1. 列表只有1个元素时，全排列就是列表本身；

   2. 列表有2个元素时，全排列是[a,b]和[b,a]；

   3. 列表有n个元素时，将第一个元素固定，对剩下n - 1个元素进行全排列。再将第一个元素依此与其他元素交换，对每次交换后剩下的n-1个元素进行全排列。对n-1个元素的全排列就是一个递归过程。

      代码如下：

      ```python
      def permutations(l, start, end):
          if start==end:
              print(l)
          else:
              i = start
              for j in range(start, end): 
                  l[j], l[i] = l[i], l[j]
                  permutations(l, start + 1, end)
                  l[i], l[j] = l[j], l[i]            
      num=['2','3','4','5']
      permutations(num, 0, 4)
      ```

      

