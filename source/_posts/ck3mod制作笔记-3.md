---
title: ck3mod制作笔记-3
date: 2020-10-05 09:25:51
tags:
	- 游戏相关
	- ck3
	- mod
categories: CK3
---

接下来是事件和决议的简单写法。

<!--more-->

# 事件

## 一些文件说明

1. 首先在`...\Crusader Kings III\game\common\event_themes`文件夹中，可以看到`00_event_themes.txt`文件，里面记录了游戏中的事件主题，不同的主题会有不同的图标、音乐和背景。

2. 其次在`...\Crusader Kings III\game\common\event_backgrounds`文件夹中，可以看到`01_event_backgrounds.txt`文件，里面记录了上一条提到的背景对应的信息，包括图片、环境和氛围，例如对于`event_background = study`的事件主题而言，它的背景信息如下：

   ```
   study = {
   	background = {
   		reference = "gfx/interface/illustrations/event_scenes/study.dds"
   		environment = "environment_event_study"
   		ambience = "event:/SFX/Events/Backgrounds/office_workplace"
   	}
   }
   ```

3. 接下来在`...\Crusader Kings III\game\events`文件夹中，可以看到大量事件的具体说明，其中的`_events.info`文件介绍了事件格式。

## 事件格式

1. 以勒索事件为例来分析事件格式，勒索事件一共有3种：勒索被拒绝、勒索某人造成恐惧、被勒索带来压力。

   这里分析第一种：

   ```
   blackmail.0001 = {
   	title = blackmail.0001.t
   	desc = blackmail.0001.desc
   	theme = intrigue
   	left_portrait = {
   		character = scope:recipient
   		animation = dismissal
   	}
   	# TODO_CD: Add a check that the secret has not yet been exposed
   	immediate = {
   		play_music_cue = mx_cue_murder
   	}
   	after = { remove_variable = currently_blackmailing }
   	option = {
   		name = blackmail.0001.a
   		scope:target = {
   			expose_secret = root
   		}
   	}
   	option = {
   		name = blackmail.0001.b
   		scope:target = {
   			disable_exposure_by = root
   		}
   		ai_chance = {
   			factor = 0
   		}
   	}
   }
   ```

   `title`和`desc`两个字段对应的内容可以在文本文件（`...\Crusader Kings III\game\localization\simp_chinese\event_localization\blackmail_events_l_simp_chinese.yml`）中看到；

   `theme`是`intrigue`，在之前的事件主题中可以找到：

   ```
   intrigue = {
   	icon = {
   		reference = "gfx/interface/icons/event_types/type_intrigue.dds"
   	}
   	sound = {
   		reference = "event:/SFX/Events/Themes/sfx_event_theme_type_intrigue"
   	}
   	background = {
   		event_background = corridor_night
   	}
   }
   ```

   也就是说该事件出现时会有如上图标音乐和背景。

   接下来`left_portrait`是说事件发生时左边的人物形象，这里还可以根据需要设置`right_portrait, lower_left_portrait, lower_center_portrait, lower_right_portrait`。

   `immediate`和`after`根据字面意思来理解应该是指事件发生时和发生后所触发的效果。

   接下来两个`option`表示这个事件有两个选项，分别对应的文字是`blackmail.0001.a`和`blackmail.0001.b`，对应的结果是暴露用来勒索的秘密和放弃勒索。

2. 事件的写法还涉及很多东西，之后再继续看，然后先看看决议。

# 决议

1. 决议文件存放在`...\Crusader Kings III\game\common\decisions`中，同样有一个`_decisions.info`来说明决议的大致格式。
2. 决议的格式和事件其实大同小异，就不记录了。

# 一个基础尝试

我希望做一个决议，通过决议触发一个事件，而该事件有3个选项，分别是

1. 消耗500威望给我1000金钱；
2. 无事发生；
3. 消耗1000金钱给我500威望。

那么首先新建一个mod文件夹，它的路径是`C:\Users\LiKun\Documents\Paradox Interactive\Crusader Kings III\mod\test`，需要注意的是，在`C:\Users\LiKun\Documents\Paradox Interactive\Crusader Kings III\mod`文件夹里同时会出现一个`test.mod`，它指向我们mod的路径，内容是：

```
version="0.0"
tags={
	"Historical"
}
name="test"
supported_version="1.1.*"
path="C:/Users/LiKun/Documents/Paradox Interactive/Crusader Kings III/mod/test"
```

接下来，我们在mod文件夹中按照游戏本体那样逐层建立所需要的文件夹，这里一定要严格弄清楚所有路径。建立好的文件夹和文件如下：

```
- test
	- common
		- decisions
			give_money_decision.txt
	- events
		- give_money_events
			give_money_events.txt
	- localization
		- simp_chinese
			- event_localization
				give_money_l_simp_chinese.yml
			decisions_test_l_simp_chinese.yml
	descriptor.mod
```

我首先写的是事件，也就是`give_money_events.txt`，该文件如下：

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
		remove_short_term_gold = 1000
		add_prestige = 500
	}
}
```

是非常简单的格式，一共3个选项，没有任何触发限制。其中`title, desc, name`等需要替换成文字的地方，之后在文本文件会增加内容。

需要注意的是，这里用`add_gold`增加金钱，用`remove_short_term_gold`减少金钱，是因为我发现`add_gold = -1000`根本不会生效，查了一下现成的和金钱有关的事件文档，发现用的是`remove_short_term_gold`，但这个句式到底是什么意思目前也不是很确定。

接下来写决议文件`give_money_decision.txt`，内容如下：

```
give_money_decision = {
	picture = "gfx/interface/illustrations/decisions/decision_personal_religious.dds"
	desc = give_money_decision_desc
	is_valid_showing_failures_only = {
		is_available_adult = yes
	}
	effect = {
		custom_tooltip = give_money_decision_effect_tooltip
		trigger_event = give_money.1
	}
	ai_check_interval = 0
}
```

这里增加了需要成年人的限制，`custom_tooltip = give_money_decision_effect_tooltip`是指效果区域会显示`give_money_decision_effect_tooltip`所对应的文字，而`trigger_event = give_money.1`表示确认该决议后，会触发事件`give_money.1`。

事件和决议都写好以后，就开始完成文本内容，首先是决议文本`decisions_test_l_simp_chinese.yml`，内容如下：

```
l_simp_chinese:
 give_money_decision:0 "我好穷啊"
 give_money_decision_tooltip:0 "收点钱。"
 give_money_decision_desc:0 "想要钱吗？"
 give_money_decision_effect_tooltip:0 "点击按钮一键收钱。"
 give_money_decision_confirm:0 "当然"
```

完成这部分内容后，该决议在游戏中的效果就是

![image-20201006221530141](https://i.loli.net/2020/10/06/HQoYAK7JVivqI62.png)

相应的，事件文本也是一样的格式：

```
l_simp_chinese:
 give_money.1.t:0 "给我打钱！"
 give_money.1.desc:0 "神说要有钱。"
 give_money.1.option.a:0 "好，我喜欢！"
 give_money.1.option.b:0 "不了不了。"
 give_money.1.option.c:0 "你这是看不起我！"
```

游戏中的效果是

![image-20201006221637015](https://i.loli.net/2020/10/06/MWy7KlRpa1wA4kN.png)

把文件们都保存好，就完成了第一个基础mod了。

补充一个重要说明，文件的编码格式非常重要，统一采用UTF-8-BOM编码，不然会出问题。