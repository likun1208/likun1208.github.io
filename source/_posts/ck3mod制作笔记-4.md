---
title: ck3mod制作笔记-4
date: 2020-11-08 11:48:18
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
description: 这是对CK3的wiki中的事件模组的简要翻译。
---
# 事件模组

[链接](https://ck3.paradoxwikis.com/Event_modding)

## 位置

事件在`events`文件夹下的`.txt`文件中。

## 结构

整体结构与[CK2](https://ck2.paradoxwikis.com/Event_modding)的事件很相似，但是对语法进行了一些调整，并增加了大量额外的功能，其中许多功能是可选的。此处将介绍每个事件所必须的部分，并在稍后部分中单独介绍其他内容。

```mod
example.1 = {
	desc = example.1.desc
	
	option = {
		name = example.1.a
	}
}
```

写好该mod并加载，即可在游戏中的控制台输入`event triggers [example.1]`来触发该事件。

### ID和命名空间

ID是事件独一无二的标志。

### 标识

这些是确定事件种类和外观的顶级变量。它们具有有限的值集。

| Flag   | Meaning                                                      | Possible values                                              |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| type   | 事件类型，决定root的范围。                                   | * character_event none (when an event doesn't use the root scope at all) |
| hidden | 如果设置为true，则该事件不会显示出来，只会在后台触发。可用于执行与玩家不立即相关的维护事件。 | true, false                                                  |

## 画像

### 画像位置

| Portrait Position     | Description              |
| --------------------- | ------------------------ |
| left_portrait         | 出现在事件场景的左边。   |
| right_portrait        | 出现在事件场景的右边。   |
| lower_left_portrait   | 出现在事件场景的左下方。 |
| lower_center_portrait | 出现在事件场景的中下方。 |
| lower_right_portrait  | 出现在事件场景的右下方。 |

```
example_event.1001 = {
	left_portrait = {
		character = ROOT # 无论是什么角色，都显示在此事件窗口位置，显示所选的动画。
		animation = fear # 请注意，具有某些遗传特征（例如，巨人主义、侏儒主义）的角色更改其角色模型具有不同的动画，如果将其中一个动画分配给没有该模型的角色，则可能会发生崩溃。
	}
	right_portrait = {
		character = ROOT
		animation = scheme
	}
	lower_left_portrait = {
		character = ROOT
	}
	lower_center_portrait = {
		character = ROOT
	}
	lower_right_portrait = {
		character = ROOT
	}
}
```

这段mod所对应的事件如下图所示。

![img](https://i.loli.net/2020/11/08/GlaYmiW15KjkUzF.png)

### 动画

| Animation           |                        |                       |                           |
| ------------------- | ---------------------- | --------------------- | ------------------------- |
| happiness           | dismissal              | prison_dungeon        | admiration                |
| disgust             | schadenfreude          | stress                | personality_compassionate |
| pain                | ecstasy                | fear                  | rage                      |
| anger               | flirtation_left        | shame                 | boredom                   |
| shock               | worry                  | scheme                | personality_bold          |
| personality_callous | personality_irrational | personality_content   | flirtation                |
| disbelief           | personality_cynical    | personality_rational  | personality_dishonorable  |
| idle                | paranoia               | personality_vengeful  | disapproval               |
| sick                | beg                    | personality_zealous   | newborn                   |
| love                | sadness                | personality_honorable | personality_forgiving     |
| personality_greedy  | personality_coward     | severelyinjured       | severelywounded           |
| war_attacker        | war_defender           | war_over_win          | grief                     |

### 主题

| Theme                     |                                 |                             |                            |
| ------------------------- | ------------------------------- | --------------------------- | -------------------------- |
| abduct_scheme             | alliance                        | bastardy                    | battle                     |
| befriend_scheme           | claim_throne_scheme             | corruption                  | crown                      |
| culture_change            | death                           | default                     | diplomacy                  |
| diplomacy_family_focus    | diplomacy_foreign_affairs_focus | diplomacy_majesty_focus     | dread                      |
| dungeon                   | dynasty                         | education                   | fabricate_hook_scheme      |
| faith                     | family                          | feast_activity              | friend_relation            |
| friendly                  | generic_intrigue_scheme         | healthcare                  | hunt_activity              |
| hunting                   | intrigue                        | intrigue_intimidation_focus | intrigue_skulduggery_focus |
| intrigue_temptation_focus | learning                        | learning_medicine_focus     | learning_scholarship_focus |
| learning_theology_focus   | love                            | lover_relation              | marriage                   |
| martial                   | martial_authority_focus         | martial_chivalry_focus      | martial_strategy_focus     |
| medicine                  | mental_break                    | mental_health               | murder_scheme              |
| party                     | pet                             | physical_health             | pilgrimage_activity        |
| pregnancy                 | prison                          | realm                       | recovery                   |
| rival_relation            | romance_scheme                  | secret                      | seduce_scheme              |
| seduction                 | skull                           | stewardship                 | stewardship_domain_focus   |
| stewardship_duty_focus    | stewardship_wealth_focus        | sway_scheme                 | unfriendly                 |
| vassal                    | war                             | witchcraft                  |                            |

#### 背景

| Background             |                           |                   |                    |
| ---------------------- | ------------------------- | ----------------- | ------------------ |
| alley_day              | alley_night               | armory            | army_camp          |
| battlefield            | bedchamber                | burning_building  | corridor_day       |
| corridor_night         | council_chamber           | courtyard         | docks              |
| dungeon                | farmland                  | feast             | gallows            |
| garden                 | market                    | market_east       | market_india       |
| market_tribal          | market_west               | physicians_study  | sitting_room       |
| study                  | tavern                    | temple            | temple_church      |
| temple_generic         | temple_mosque             | temple_scope      | terrain            |
| terrain_activity       | terrain_scope             | throne_room       | throne_room_east   |
| throne_room_india      | throne_room_mediterranean | throne_room_scope | throne_room_tribal |
| throne_room_west       | wilderness                | wilderness_desert | wilderness_forest  |
| wilderness_forest_pine | wilderness_mountains      | wilderness_scope  | wilderness_steppe  |

#### 环境

当选择好背景后，对应的环境也会被自动选择，但如果有必要可以自行指定覆盖。

| Environment                                  |                                                |                                              |
| -------------------------------------------- | ---------------------------------------------- | -------------------------------------------- |
| environment_body                             | environment_council                            | environment_cw_east_main                     |
| environment_cw_east_spouse                   | environment_cw_east_throneroom_main            | environment_cw_east_throneroom_spouse        |
| environment_cw_india_main                    | environment_cw_india_spouse                    | environment_cw_india_throneroom_main         |
| environment_cw_india_throneroom_spouse       | environment_cw_mediterranean_main              | environment_cw_mediterranean_spouse          |
| environment_cw_mediterranean_throneroom_main | environment_cw_mediterranean_throneroom_spouse | environment_cw_tavern                        |
| environment_cw_tavern_spouse                 | environment_cw_tribal_main                     | environment_cw_tribal_spouse                 |
| environment_cw_west                          | environment_cw_west_spouse                     | environment_event_alley                      |
| environment_event_alley_day                  | environment_event_armory                       | environment_event_battlefield                |
| environment_event_bedchamber                 | environment_event_church                       | environment_event_corridor_day               |
| environment_event_courtyard                  | environment_event_desert                       | environment_event_docks                      |
| environment_event_dungeon                    | environment_event_farms                        | environment_event_feast                      |
| environment_event_forest                     | environment_event_forest_pine                  | environment_event_gallows                    |
| environment_event_garden                     | environment_event_genericcamp                  | environment_event_market_east                |
| environment_event_market_tribal              | environment_event_market_west                  | environment_event_mosque                     |
| environment_event_mountains                  | environment_event_sittingroom                  | environment_event_standard                   |
| environment_event_steppe                     | environment_event_study                        | environment_event_study_physician            |
| environment_event_tavern                     | environment_event_temple                       | environment_event_throne_room_west           |
| environment_frontend_east_heir               | environment_frontend_east_main                 | environment_frontend_east_secondary          |
| environment_frontend_india_heir              | environment_frontend_india_main                | environment_frontend_india_secondary         |
| environment_frontend_mediterranean_heir      | environment_frontend_mediterranean_main        | environment_frontend_mediterranean_secondary |
| environment_frontend_tribal_heir             | environment_frontend_tribal_main               | environment_frontend_tribal_secondary        |
| environment_frontend_west_heir               | environment_frontend_west_main                 | environment_frontend_west_secondary          |
| environment_head                             | environment_hud                                | environment_portrait_editor                  |
| environment_shoulders                        | environment_standard                           | environment_torso                            |
| environment_war_overview                     |                                                |                                              |

### 触发器

是使事件工作的附加内容。

### on_trigger_fail

当触发器失败时运行该项。

### 描述

解释一个描述如何由多个字符串组成，例如`first_valid`和所有其他选项。

### 即时模块

该代码块中的内容会在事件触发时立即执行，甚至早于标题、描述和画像的渲染等等。一般用于设置变量和保存范围，或想要实现无玩家控制的功能影响。

### 选项

事件中的选项可以由用户选择。每个事件可以有任意数量的选项，包括无选项（一个典型的例子就是隐藏事件）。每个选项在主事件中如下定义：

```mod
example.1 = {
	[...]
	option = {
		# option info
	}
	[...]
}
```

下列表格介绍了`option`块中的可选关键字：

| Key                 | Required | Description                                                  | Example                                                      |
| ------------------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| name                | Yes      | Points to a localization key for the event option button text. | name=example.1.a                                             |
| (effects)           | No       | Any [effects](https://ck3.paradoxwikis.com/Effect) that the option may have can be written directly in the `option` block. | play_music_cue = mx_cue_banquet                              |
| trigger             | No       | Defines a [trigger](https://ck3.paradoxwikis.com/Trigger) that has to be fulfilled for the option to be valid and thus available to the user. Not to be confused with the [main event trigger](https://ck3.paradoxwikis.com/Event_modding#Trigger). | `trigger = { has_trait = shy } `                             |
| show_as_unavailable | No       | If the option is invalid, but this trigger is, the option will be  shown, but disabled. This behavior is also influenced by the  EVENT_OPTIONS_SHOWN_HIDE_UNAVAILABLE define. | `show_as_unavailable = { short_term_gold < medium_gold_value } ` |
| highlight_portrait  | No       | Highlights the event portrait of this character while this option is hovered. This is in addition to the automatic highlighting when  hovering an event option that has an effect that affects portrait  characters. | highlight_portrait = scope:custom                            |

### 结束后

该代码块描述了事件发生后运行的内容。

### 部件

有什么类型的小部件。

## On_actions

这部分wiki上是不完整的，先不详细写了。

该部分分为两类：On_actions from Code和On_actions Not from Code（即from script）

## 策略

这部分wiki也不完整，以后有了在写。

分为两部分：触发事件 和 技术与设计模式。