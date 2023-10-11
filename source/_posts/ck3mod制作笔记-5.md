---
title: ck3mod制作笔记-5
date: 2020-11-08 12:49:13
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
description: 这部分学习条件式写法。
---
# 条件式

## 基本结构

条件式的基本结构和CK2里一样，如下：

```
if = {
	limit = {
		# 条件
	}
	# 符合条件时执行的指令
}
```

其实条件式不仅限于if语句，在其他地方也会出现这样`limit = ...`的句式，用来表示执行指令的条件。

接下来沿用之前的例子，将送钱的事件写一个条件式：

```
namespace = give_money
give_money.1 = {
	type = character_event
	title = give_money.1.t
	desc = give_money.1.desc
	theme = stewardship
	left_portrait = root
	option = {
		name = give_money.1.option.a
		add_gold = 1000
		add_prestige = -500
	}
	option = {
		name = give_money.1.option.b
	}
	option = {
		name = give_money.1.option.c
		if = {
			limit = {
				gold >1000
			}
			remove_short_term_gold = 1000
			add_prestige = 500
		}
	}
}
```

可以看到，现在第三个选项是有条件执行的，当金币大于1000时，会执行减少金钱增加虔诚的指令，而金币不足则不会执行。具体到游戏中，界面如下图，当右上角金币只有26的时候，第三个选项是没有直接影响；而金币超过1000时，则第三个选项变成了扣钱换虔诚。

![image-20201205211819919](https://i.loli.net/2020/12/05/nUe1ExpMmFwiJgr.png)

![image-20201205212017719](https://i.loli.net/2020/12/05/EMmug4AC7KRUFrS.png)

## 增加分支

在一些情况下，我们需要表达“如果满足条件，则执行A指令；反之，则执行B指令”，这种情况就不能简单地用上面的结构来实现，而是使用else语句，具体结构如下：

```
if = {
	limit = {
		# 条件
	}
	# 符合条件时执行的指令
}
else = {
	# 不符合条件时执行的指令
}
```

比如我们希望，当金钱不够1000时加200虔诚，不扣钱，那就如下改写：

```
	option = {
		name = give_money.1.option.c
		if = {
			limit = {
				gold >1000
			}
			remove_short_term_gold = 1000
			add_prestige = 500
		}
		else = {
			add_prestige = 200
		}		
	}
```

具体效果如下图，当金币够1000时，和前面一样；而金币不够1000时，则只有加200虔诚。

![image-20201205213507855](https://i.loli.net/2020/12/05/42FCzK9NsnlEhZ5.png)

![image-20201205213724688](https://i.loli.net/2020/12/05/VbFYSnlX19IqHAp.png)

* 需要补充说明的是，CK2中没有else语句，而是通过增加break来实现分支的；相反，CK3中无法通过break来实现分支。

# 随机性结果

当我们希望某个选项的结果随机出现的时候，就需要用到`Random`，它有以下两种用法：

1. 该选项有一定概率得到某种结果，或者无事发生：

   ```
   random = {
   	chance = 概率值
   	选项结果
   }
   ```

   例如，我们将之前的事件中的第二个选项修改成如下内容：

   ```
   	option = {
   		name = give_money.1.option.b
   		random = {
   			chance = 50
   			add_gold = 1000
   		}
   	}
   ```

   这个选项很容易理解，有50%的概率得到1000金币，游戏中的截图如下：

   ![image-20201210193754167](https://i.loli.net/2020/12/10/GLtuAOkoBDZ4S8h.png)

   顺带一提，明明是50%的概率，我却直到第5次才获得1000......

2. 更多情况我们会希望得到多个随机结果，这时就要用`Random_list`，结构如下：

   ```
   random_list = {
   	概率1 = {
   		第一个结果
   	}
   	概率2 = {
   		第二个结果
   	}
   	概率3 = {
   		第三个结果
   	}
   	......
   }
   ```

   在这种结构下，每个情况的触发概率并不是直接的百分比，而是该选项值占所有概率总和的百分比，具体来说，比如（30，30，15）这样的组合中，第一个选项的概率并不是30%，而是30/75，也就是40%。

   我们再将刚才的事件进行如下修改：

   ```
   	option = {
   		name = give_money.1.option.b
   		random_list = {
   			30 = {
   				add_gold = 1000
   			}
   			30 = {
   				add_gold = 100
   			}
   			15 = {
   				remove_short_term_gold = 200
   			}
   		}
   	}
   ```

   这样修改后，游戏中的截图如下：

   ![image-20201210194729366](https://i.loli.net/2020/12/10/T1wtGmAWIkvqrCo.png)

   

## 有触发的随机性条件

虽然到目前为止还没学触发（Trigger），但大致意思应该能看懂，和前面if语句中的limit有点像。

如果某个随机选项有触发条件，那么当满足条件时，该选项的概率值才会参与计算，例如下面这个事件：

```
	option = {
		name = give_money.1.option.b
		random_list = {
			30 = {
				trigger = {
					gold > 1000
				}
				add_gold = 1000
			}
			30 = {
				add_gold = 100
			}
			15 = {
				remove_short_term_gold = 200
			}
		}
	}
```

当金币不足1000时，可以看到游戏中截图如下：

![image-20201210195304419](https://i.loli.net/2020/12/10/EkoGJDMLstROlY5.png)

而当金币够1000时，事件就变成了：

![image-20201210195404539](https://i.loli.net/2020/12/10/mp8YdqW7IS1R2vZ.png)

## 修改随机权重

有时候我们希望某个随机结果的权重根据不同的条件而发生改变，例如前面的事件中，同样是得到1000金币，我们希望根据不同的管理值会有不同的概率。这样的效果我们可以使用`modifier`来实现，具体事件代码如下：

```
	option = {
		name = give_money.1.option.b
		random_list = {
			30 = {
				trigger = {
					gold > 1000
				}
				modifier = {
					add = stewardship
				}
				add_gold = 1000
			}			
			30 = {
				add_gold = 100
			}
			15 = {
				remove_short_term_gold = 200
			}
		}
	}
```

这段代码的含义就是，该选项有3个随机结果，分别是金币+1000，金币+100，金币-200，其概率组合是（30+管理值，30，15）。

那么在游戏中我们可以看到，该角色管理值是8，对应的事件如下：

![image-20201210203011631](https://i.loli.net/2020/12/10/htkEOdT9qS3xBQY.png)

需要注意的是，不能直接把类似stewardship这样的词放到概率值那里，如果我们希望直接用stewardship当概率，则应该按下面的形式来写：

```
random_list = {
	0 = {
		modifier = {
			add = stewardship
		}
		......
	}
	......
}
```

此外，我尝试给modifier加trigger或者if语句，都失败了，可能它只能单独出现吧。