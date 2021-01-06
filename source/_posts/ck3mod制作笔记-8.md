---
title: ck3mod制作笔记-8
date: 202-12-24 19:08:27
tags:	
	- 游戏相关
	- ck3
	- mod
categories: CK3
description: 这部分学习flag和customizable_localization。
---

# flag

1. `flag`的作用是对人物或者事件进行标记，可以用在`if`或`trigger`或其他类似的地方，而其中人物对应的`flag`就是`character_flag`。

2. 我们使用`add_character_flag`来添加一个人物标记，用`remove_character_flag`移除一个人物标记。

3. 接下来看一个例子：有两个事件，其中事件1001有2个选项，选项a和b分别会给人物添加两个不同的`flag`，2个选项都会触发事件1002；而事件1002则有1个选项，根据事件1中的两个`flag`，该选项会显示不同的内容。那么代码如下：

   ```
   namespace = adventure
   adventure.1001 = {
       type = character_event
   	title = adventure.1001.t1
   	desc = adventure.1001.desc
   	theme = adventure
   	left_portrait = root
   	option = {
   		name = adventure.1001.option.a
   		add_character_flag = flag_adventure_random_1
   		trigger_event = adventure.1002
   	}
   	option = {
   		name = adventure.1001.option.b
   		add_character_flag = flag_adventure_random_2
   		trigger_event = adventure.1002
   	}
   }
   adventure.1002 = {
   	type = character_event
   	title = adventure.1002.t1
   	desc = adventure.1002.desc1
   	theme = adventure
   	left_portrait = root
   	option = {
   		name = {
   			trigger = {has_character_flag =flag_adventure_random_1}
   			text = adventure.1002.option.a
   		}
   		name = {
   			trigger = {has_character_flag = flag_adventure_random_2}
   			text = adventure.1002.option.b
   		}
   	}
   }
   ```

4. `flag`的基础用法大致就是这样，除了`character_flag`也还有其他类型的`flag`。

# customizable_localization

1. 起因是想了解如何随机显示出现的文字，例如某事件的标题随机显示。试图使用`random_list`等方式都没用，在论坛提问以后大佬告诉我可以用`customizable_localization`来实现。学习了一下大致写法如下。

2. 首先，文件夹路径是`Crusader Kings III\game\common\customizable_localization`，该文件夹中有很多已经写好的文件。

3. 其次，相关的本地化文件夹路径是`Crusader Kings III\game\localization\simp_chinese\custom_localization`。

4. 一个普通的`customizable_localization`文件格式如下：

   ```
   YourCustomLoc = {
       type = character 
       random_valid = yes
       text = {
           trigger = {  
               your_triggers = yes
           }
           localization_key = title_1
       }
       text = {
           trigger = {  
               your_triggers = no
           }
           localization_key = title_2
       }
   }
   ```

   其中，`type`可以有其他选项，具体选项在`CUSTOM_LOC_README.txt`文件中列出了。

   `random_valid`表示随机选，如果去掉这行就不随机了，而是按顺序第一个满足条件的文本。

   接下来的`text`块就是待选的随机文本，其中`trigger`是该文本的触发条件，如果没有条件则可以不写`trigger`块。`localization_key`所对应的文字是在前面第3条提到的路径中。

5. 在使用该随机文本的时候，例如我们要让某事件的标题随机出现，则把该标题所对应的本地化字符串写成`"[ROOT.Char.Custom('YourCustomLoc')]"`

6. 具体来看一个例子：

   1. `common\customizable_localization\00_test_random_loc.txt`

      ```
      YourCustomLoc = {
          type = character
          random_valid = yes
          text = {
              localization_key = title_1
          }
          text = {
              localization_key = title_2
          }
      }
      ```

   2. `localization\simp_chinese\custom_localization\test_random_loc_l_simp_chinese.yml`

      ```
      l_simp_chinese:
       title_1:0 "大胆"
       title_2:0 "善心"
      ```

   3. `events\adventure_events\adventure_events.txt`

      ```
      adventure.1001 = {
          type = character_event
      	title = adventure.1001.t1
      	desc = adventure.1001.desc
      	theme = adventure
      	left_portrait = root
      	option = {
      		name = adventure.1001.option.a
      		add_character_flag = flag_adventure_random_1
      		trigger_event = adventure.1002
      	}
      	option = {
      		name = adventure.1001.option.b
      		add_character_flag = flag_adventure_random_2
      		trigger_event = adventure.1002
      	}
      	option = {
      		name = adventure.1001.option.c
      		trigger_event = adventure.1003
      	}
      }
      ```

   4. `localization\simp_chinese\event_localization\adventure\adventure_l_simp_chinese.yml`

      ```
      l_simp_chinese:
       adventure.1001.t1:0 "[ROOT.Char.Custom('YourCustomLoc')]"
       adventure.1001.desc:0 "氪金可以获得更好的体验"
       adventure.1001.option.a:0 "我相信上天会为我选择最合适的道路！"
       adventure.1001.option.b:0 "我相信我的能力，命运掌握在我自己手中！"
       adventure.1001.option.c:0 "人的能力是有极限的，我要充钱变强！"
      ```

   5. 写好这些文件以后，游戏中通过`event adventure.1001`触发该事件，可以看到标题会随机显示为`大胆`或者`善心`。

   6. 类似地，`desc`部分也可以这样写。此外，除了`[ROOT.Char.Custom('YourCustomLoc')]`这样的写法，也还有其他类型的写法，具体可以参考游戏本体文件中的内容。