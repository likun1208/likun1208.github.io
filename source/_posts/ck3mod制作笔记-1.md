---
title: ck3mod制作笔记-1
date: 2020-10-03 10:34:06
tags:
	- 游戏相关
	- ck3
	- mod
categories: CK3
---

首先新建一个mod文件。

<!--more-->

# 新建Mod

## 方法

1. 启动器里找到mod新建会自动生成相关文件；
2. 也可以文档里找到`...\Paradox Interactive\Crusader Kings III\mod`文件夹（如果没有就新建），新建`test.mod`文件，用随便什么编辑器打开，输入一系列内容并保存。

## `test.mod`文件

1. 启动器生成的该文件基础内容：

   ```
   version="0.0"
   tags={
   	"Historical"
   }
   name="test"
   supported_version="1.0.3"
   path="C:/Users/LiKun/Documents/Paradox Interactive/Crusader Kings III/mod/test"
   ```

   `version`和`supported_version`自己可以看着改。

2. 可以附加：

   ```
   picture = 图像文件名
   ```

   指定mod开场图片。

3. ck2可以设定`replace_path`，覆盖文件夹中的所有内容，ck3是否可以还没试过，之后看看。

## `common`文件夹

1. 正常来说ck3游戏本体内容不在上面那个文档里，而在其他安装路径，比如`C:\Program 1\Steam\steamapps\common\Crusader Kings III`，在这个文件夹中的`...\game\common`文件夹中存放着很多游戏相关文件，接下来试图逐一分析。

2. 文件夹里有很多文件夹和一个`achievement_groups.txt`，这个txt文件内容看上去是对成就进行分组，分为`very_easy_achievements`, `easy_achievements`, `medium_achievements`, `hard_achievements`, `very_hard_achievements`，文件夹中的第一个是`achievements`，包括`standard_achievements.txt`和`msgrdk_achievements.json`。

   1.  `standard_achievements.txt`中的内容格式是：

      ```
      成就名 = {
      	possible = {
      		# 这里应该是写成就达成所需要的条件，是很多逻辑语句组成的内容
      	}
      	happened = {
      		custom_description = {
      			# 这里应该是写直接触发成就达成的行为
      		}
      	}
      }
      ```

      

   2. `msgrdk_achievements.json`看起来是给每个成就映射了一个数字编号，总共55个。

   3. 目前对成就这部分很不熟悉，可能有理解错的内容。

3. `activities`文件夹，这里看上去是五个主要活动的说明，这五个都是决议里的，但应该不是决议。

   1. `_activities.info` 说明了具体格式：

      ```
      name_of_activity = {
      	expiration_days = X 			# default number of days after an activity is started to it's expiration
      	days_until_auto_start = X 		# number of days from an activity is spawned until it's started (regardless of if participants have arrived or not)
      	valid = trigger					# checked daily on the activity scope
      	on_spawn = effect 				# executed on the activity when spawned
      	on_activate = effect			# executed on the activity when started (all invitations replied to or auto started)
      	on_complete = effect			# executed on the activity when completed through complete_activity = yes (ignored if complete_activity = no)
      	on_expire = effect				# executed on the activity when expiring from timeout
      	on_participant_death			# executed on the dying character during death, activity set as scope:activity
      	on_invalidated = effect			# executed on the activity when failing the valid trigger	
      	solo = yes						# marks the activity as a solo activity (no guests)
      	has_activity_window = yes		# determines if a certain activity should show a window or not
      }
      ```

      接下来结合其他几个具体文件来理解。

   2. `00_activity_feast.txt` 宴会：

      1. `expiration_days = 300` 持续300天

      2. `has_activity_window = yes` 有事件窗口

      3. `days_until_auto_start = 20` 默认20天后开始

      4. `frame = 2`

      5. `color = { 0.853906 0.867188 0.105469 1 }`

      6. ```
         valid = {
         		activity_owner = {
         			is_alive = yes
         			is_imprisoned = no
         		}
         	}
         ```

         宴会期间每天检查召开宴会的人活着且没被关起来。

      7. `on_spawn`这段很长，大致是宴会事件生成时同时执行的内容，主要是设置潜在宾客列表`potential_guests`。这段有个`if`语句，提到条件和配偶有关，没配偶似乎是会随机初始化一个配偶，没太看懂这个设计。

      8. `on_activate` 也很长，大致是宴会事件开始时触发的，如果没人来，就结束这个事件，`feast.2003`可能就是结束宴会的意思；如果有人来，给召开宴会的人选关键事件，再从宾客里选人参与到这个事件中，这里提到`reveler（狂欢者？）`会得到两点不知道是生活方式还是生活经验的奖励。而作为客人，`reveler（狂欢者？）`会得到一点，此外针对所有客人，会移除宴会邀请的拦截器（？这里还得之后再看）。

      9. `on_complete` 宴会结束时，主人和封臣似乎会有什么互动，每个参与者的目标体重（？）会改变。

      10. `on_expire` 宴会到期以后会触发`feast.2002`。注释说这个不应出现，只是作为异常记录。

      11. `on_participant_death`是空的。

      12. `on_invalidated` 失效的情况？

   3. `00_activity_hunt.txt` 打猎：

      1. 和刚才的宴会不同，这里有`solo = yes`，说明可以一个人打猎。
      2. `frame`和`color`暂时还是不太理解。
      3. 这里的很多限制都要求是健康成年人。

   4. `00_activity_meditation.txt` 冥想：

      1. 同样可以一个人进行。
   2. 看上去冥想是宗教决议中的一个：`religious_decision.0211`
   
   5. `00_activity_pilgrimage.txt` 朝圣：因为没看懂就没什么能说的。
   
   6. `00_activity_witch_ritual.txt` 巫术仪式：
   
      1. 其实也没看懂，看起来是可以搞巫术仪式的，但是限制有点多。
      2. 似乎是有个宴会，参加宴会的可以为所欲为，还会有不一样的音乐（？`play_music_cue = "mx_cue_sacredrite"`）。
      3. 这里提到的事件有`witch.3003` `witch.3010` `witch.3098`。
   
   7. 补充一下：从`event`文件夹的内容来看，官方没搞冥想和巫术仪式，以后可能会有吧。
   
4. `ai_goaltypes`文件夹和`ai_war_stances`，这俩看名字是和AI设定有关的。

   1. `ai_goaltypes`文件夹里只有`00_testgoals.txt`，内容相当于是空的，可能是留着做mod用。
   2. `ai_war_stances`文件夹里有`_ai_war_stances.info`和`00_ai_war_stances.txt`：
      1. `_ai_war_stances.info`说明了战争立场格式，注释说无法添加新的或者删除现有的，但是都能改。
      2. 这里设定的是AI战争时的目标优先级，优先级范围是1-1000，有一个战争目标列表。
      3. 有一个变量`enemy_unit_priority`，这个值目前设置为100，是指对于战力不到该AI一半的敌军单位，战争优先级数字会增加100，该值的影响似乎会随着敌军单位战力的增长而降低，当敌军战力和该AI战力一样的时候这个值就被看作0。
      4. `00_ai_war_stances.txt`是具体的优先级设定，分为进攻方和防御方：
         1. 作为进攻方的AI，在进攻时作战优先级由高到低：目标省（`wargoal_province` 500）、敌军所在地（`enemy_unit_province` 300 这个翻译我猜的，可能不对）、敌军首都（`enemy_capital_province` 150）、敌军省（`enemy_province` 100）、敌军盟友省（`enemy_ally_province` 75）、首都（`capital_province` 50）、普通省（`province` 25）、保卫战争目标省（`defend_wargoal_province` 5）。
         2. 作为进攻方的AI，在防御时会优先找敌军所在地（`enemy_unit_province` 500），其次才是战争目标省（`wargoal_province` 300），其他和刚才一样。
         3. 作为防御方，也有和上面一样的两部分。

5. `bookmark_portraits`文件夹看上去是存了游戏命令行里生成的外貌数据。

6. `bookmark`文件夹中有`00_bookmarks.txt`，这里存了游戏开局可以选的剧本数据，一共6个时代（3个867和3个1066），设定了起止时间和可选的几个角色以及角色们的相关数据。

7. 后面还有好多好多文件夹，等以后用到了再看吧......

