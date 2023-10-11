---
title: ck3mod制作笔记-8
date: 2021-01-06 19:08:27
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
description: 这部分学习flag、modifier、customizable_localization和message。
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



# modifier

1. `modifier`可以理解为对数值的修正，和特质有点像，但结构和功能简单一些。

2. `modifier`的基础结构如下：

   ```
   modifer_key = {
   	icon = icon_name	
   	# Effects, such as
   	# tax_mult = 0.25
   	# county_opinion_add = -30
   }
   ```

   其中，`icon` 的文件名如果以`positive`或`negative`结尾，则该`modifier`的颜色会有相应的变化（指游戏里那行字的颜色），例如`diplomacy_positive`和`diplomacy_negative`。

3. 在写好一个`modifier`之后，我们可以使用`add_character_modifier = XXX`来给角色添加`modifier`，使用`has_character_modifier = XXX`来判断角色是否有该`modifier`，使用`remove_character_character_modifier = XXX`来移除一个角色的某个`modifier`。在添加的时候，可以加时间字段来限制该`modifier`的持续时间，前面部分中的`flag`也是同样的用法。

4. 一个例子：

   ```
   # modifier file
   adventure_diplomacy_add_modifier = {
   	icon = diplomacy_positive
   	diplomacy = 2
   }
   
   # event file
   trigger = {
   	has_character_flag = flag_adventure_diplomacy
   	NOT = { has_character_modifier = adventure_diplomacy_add_modifier }
   }
   send_interface_message = {
   	type = adventure_success_type
   	add_character_modifier = {
   		modifier = adventure_diplomacy_add_modifier
   		years = 20
   	}
   }
   ```

   这里的`modifier`的作用是让角色外交+2，在事件中，判断该角色有相应的`flag`且没有该`modifier`时，就给角色增加该`modifier`，且持续20年。

5. 需要注意的是，我们无法在mod中直接修改角色的健康，只能通过`trait`或者`modifier`这样的方式来修改；类似地，角色的五个属性值也不能直接通过`diplomacy = 1`或者`add_diplomacy = 1`这样的方式来改，而是要通过`trait`或`modifier`修正（实际上，一定要改角色的属性值时，可以使用`add_diplomacy_skill = 1`这样的代码，但是一般不建议这么做，因为这样改完全看不出来数值是因为什么事件发生了变化，也不利于后续取消这个改变）。

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

# message

1. `message`是指游戏右下角弹出的那个信息提示，或者屏幕上半部分的中间弹出的那个横幅。

2. 代码基础结构如下：

   ```
   my_message = {
   	display = (feed|toast)		# where to display the message; default: feed
   	text = some_loc_key			# string to be used in the message; default: same as message key (e.g "my_message" in this example)
   	desc = some_loc_key			# string that gives more info about what happened
   	tooltip = some_loc_key		# string to be used for tooltip of type(optional); default: no tooltip
   	soundeffect = sound_name	# sound effect played when showing the message (optional); default: no sound
   	icon = "texture.dds"		# icon textures found in gfx\interface\message_icons
   	style = good/bad/neutral	# neutral is default, affects the look of message items
   }
   ```

   第一行的`display`如果是`feed`，则信息是右下角的形式；如果是`toast`，则信息是中间横幅的形式；默认值是`feed`；

   第二行是该信息的标题文本，默认是信息本身的关键字段，例如这里的`my_message`；

   第三行是改信息的描述文本；

   接下来是该信息的工具栏提示文本；

   最后三行分别是音效、图标和类型，图标的路径在`gfx\interface\message_icons`，类型的设置会影响信息框的颜色外观。

3. 一个例子：

   ```
   my_message_type = {
   	display = feed
   	title = "My Title"
   	desc = "My $EFFECT$"
   	tooltip = "My Tooltip"
   	soundeffect = "blah.audio"
   	icon = "nice.dds"
   	style = good
   }
   
   send_interface_message = {
   	type = my_message_type
   	add_gold = 50
   }
   ```

   这里`$EFFECT$`的写法表示该信息的文本描述是实际造成的效果，比如这里是钱增加50。这样写的一个好处就是，类似的事件可以共用同一个`message_type`，而不用针对每个事件都写一个新的。上述这个例子所弹出的信息提示如下：

   	Title = My Title
   	Description = My Add 50 Gold
   	Tooltip = My Tooltip

4. 第二个例子：

   ```
   send_interface_message = {
   	type = my_message_type
   	desc = {
   		desc = "My Start Line"
   		desc = linebreak
   		desc = "My $EFFECT$"
   	}
   	title = "My Cooler Title"
   	tooltip = "$DESCRIPTION$"
   	add_gold = 50
   }
   ```

   仍然使用刚才的`message_type`，但是在调用的时候指定了`desc`、`title`和`tooltip`这3个字段，则输出的信息中的3个字段会替换为指定的内容，如下：

   ```
   Title = My Cooler Title
   Description = My Start Line \n My Add 50 Gold
   Tooltip = My Start Line \n My Add 50 Gold
   ```

   