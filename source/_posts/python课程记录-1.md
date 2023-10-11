---
title: python课程记录-1
date: 2020-02-26 08:56:54
tags: python
categories: 课程笔记
---
1. 为了给变量x, y, z 赋初值5，下面正确的赋值语句是（）
   A. xyz=5					B. x=5 y=5 z=5
   C. x=5, y=5, z=5        D. x=5;y=5;z=5
   E. x=y=z=5		 		F. x,y,z=5
   G. x,y,z=5,5,5

	[DEG]
	没什么说的，运行一下就知道哪个对哪个错了。
   
2. x是一个两位数的整数，现将该两位数的个位和十位数字交换，例如13变成31，正确的表达式是（）
   A. (x%10)\*10+x/10		   B. (x%10)//10+x/10
   C. (x/10)%10+x//10		  D. (x%10)\*10+x//10
	
	[D]
	这里要区分“/”和“//”，前者是不取整数的除法，后者是取整，例如7/3=2.333，而7//3=2。

<!--more-->

课后练习：

1.  编写程序1：提示输入姓名和出生年份，输出姓名和年龄。运行 结果如：
   ​		请输入你的姓名：***张三***
   ​		你好，张三
   ​		请输入你的出生年份：***1999***
   ​		你好，张三，你今年19岁了。

这个很简单了，就是普通的输入输出。

```python
name = input("请输入你的姓名：")
print("你好，", name)
age = input("请输入你的出生年份：")
print("你好，", name, "你今年", 2020-int(age), "岁了")
```

2. 编写程序2： 三角形的三条边长为3、7、9，计算三角形的三个角（提示：余弦定理）

这个也还是很简单，虽然我忘了三角形的边角关系公式，不过没关系，百度有。

```python
import math
a = 3
b = 7
c = 9
A = math.acos((math.pow(c, 2) + math.pow(b, 2) - math.pow(a, 2)) / (2 * b * c))
B = math.acos((math.pow(c, 2) + math.pow(a, 2) - math.pow(b, 2)) / (2 * a * c))
C = math.acos((math.pow(a, 2) + math.pow(b, 2) - math.pow(c, 2)) / (2 * b * a))
print("弧度值：", A, B, C)
print("角度值：", math.degrees(A), math.degrees(B), math.degrees(C))
```

3. 编写程序3：对猜数字游戏的程序进行如下修改：

   1. 由用户键盘输入秘密数的上限和下限。
   2. 现有的程序每次只能玩一局，对此进行修改，玩完一局后自动进入下一局。
   3. 退出游戏时打印用户总共玩的局数（没玩完的局不算），如果局数不为0，则还需打印每一局的猜测次数。

这个看起来复杂，但就是条件说的不清楚而已，总的来说要求是用户输入数字的时候玩猜数字，反之结束游戏，然后输出一下统计结果。

```pyhon
import random
count = 0
number = []
a = input("请输入一个整数下限：")
while a.isdigit():
    b = input("请输入一个整数上限：")
    secret = random.randint(int(a), int(b))
    print("我有一个", a, "-", b, "的秘密数，想要猜出它？请按数字键并回车；如果不想猜了，可按非数字键并回车或直接回车。")
    inputStr = input("你猜是？")
    number.append(0)
    while inputStr.isdigit():
        guess = int(inputStr)
        number[count] += 1
        if guess < secret:
            print("小了")
        elif guess > secret:
            print("大了")
        else:
            print("Bingo! 你猜对啦！这个数就是", secret)
            print("继续玩下一局！")
            count += 1
            break
        inputStr = input("你猜是？")
    a = input("请输入一个整数下限：")
print("再见，下次玩！")
print("你一共玩了", count, "局")
for i in range(0, count):
    print("第", i + 1, "局猜测次数为", number[i])
```

