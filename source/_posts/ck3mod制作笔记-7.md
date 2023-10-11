---
title: ck3mod制作笔记-7
date: 2020-12-24 19:08:27
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
description: 这部分学习on_action的使用。
---
# On_Action

在前面学习到的事件是由玩家在游戏中通过决议而触发的，但有时候我们会希望实现一些自动触发的事件，比如一年触发一次、五年触发一次等，这个时候就需要使用`on_action`了。

在[ck3mod制作笔记-4](https://likun1208.github.io/2020/11/08/ck3mod%E5%88%B6%E4%BD%9C%E7%AC%94%E8%AE%B0-4/)中，我们提到过这个东西。它可以理解成一种编码，当游戏中有对应编码的事件发生时，就会执行`on_action`代码块中的内容，比如当角色结婚时，`on_marriage`部分的内容就会被触发。游戏本体的相关代码存放在`Crusader Kings III\game\common\on_action`文件夹中，可以看到有很多很多选项。

## 结构

`_on_actions.info`文件中说明了一个`on_action`代码块的写法，这里简要翻译和介绍一下。

首先，一个`on_action`的基础结构如下：

```
on_action_name = {
	trigger = {
		trigger_conditions = yes
	}
	weight_multiplier = {
		base = 1
		modifier = {
			add = 1
			trigger_conditions = yes
		}
	}
	events = {
		event_id_1
		delay = { days = 365 }
		event_id_2
		delay = { months = { 6 12 } }
		event_id_3
	}
	random_events = {
		chance_to_happen = 25
		chance_of_no_event = {
			value = 0
			if = {
				limit = { trigger_conditions = yes }
				add = 10
			}
		}
		100 = event_id_1
		200 = event_id_2
		100 = 0
	}
	first_valid = {
		event_id_1
		event_id_2
		fallback_event_without_trigger
	}
	on_actions = {
		on_action_1
		on_action_2
		on_action_3
	}
	random_on_actions = {
		100 = on_action_1
		200 = on_action_2
		100 = 0
	}
	first_valid_on_action = {
		on_action_1
		on_action_2
	}
	effect = {
		effects = yes
	}
	fallback = another_on_action
}
```

1. 第一部分`trigger`是这个`on_action`的触发条件，如果一个`on_action`被游戏中的事件触发，而该`on_action`的`trigger`条件不满足，则无事发生。例如我们写一个`on_action`设定角色结婚后立刻获得1000金币，但是在`trigger`中限制该角色需要年龄低于30，则一个60岁角色的结婚事件触发该`on_action`后，不会获得金币。
2. 第二部分是对该`on_action`权重的修正，这个权重是用于其他`on_action`的随机`on_action`列表（这个列表会在下面介绍），比如我们有3个`on_action`，分别是`on_action_1, on_action_2, on_action_3`，其中后两个在第一个的随机`on_action`列表中，这意味着当`on_action_1`触发时，`on_action_2`和`on_action_3`会以一定概率触发，概率由它们在列表中的权重决定，权重的计算方式在前面介绍过，而这里的`weight_multiplier`就是对该权重值的修正。
3. 第三部分表示该`on_action`触发时会发生的事件列表，事件们在满足自身触发条件（`trigger`）的情况下，会从上到下按顺序逐一发生。这一部分中可以使用`delay`来控制事件之间的间隔，表示上一个事件发生多久后再继续进行下一个事件。`delay`可以是`days`，也可以是`months`和`years`，且这3者的值可以是形如`{6 12}`这样的随机列表，具体到游戏中会从大括号的值里挑一个，注意这两个数字之间没有逗号。上述代码中的这段例子就是当`on_action`触发时，先立刻发生`event_id_1`，等365天后会发生`event_id_2`，接着等6个月或12个月会发生`event_id_3`。
4. 第四部分`random_events`是随机事件列表，`on_action`发生时，会从该列表中根据权值随机挑选事件发生，会且只会挑一个。其中的`chance_to_happen`是基础权值，比如这里是25。`chance_of_no_event`是可以格式化为脚本值的条目，出于性能原因独立于`chance_to_happen`，只有当`chance_to_happen`为真时，该值会被考虑。接下来的列表就是会被挑选的事件列表，其中形如`100 = 0`这样的条目，是指有一定概率没有任何事件发生，这个设置是为了避免玩家通过让随即列表中的其他事件都不满足触发条件，从而触发稀有事件。
5. 第五部分`first_valid`选取触发器返回 true 的第一个事件。
6. `on_action`除了规定发生什么事件以外，也可以规定触发哪些`on_action`，接下来的第六、第七和第八部分和前面的`events, random_events, first_valid`很像，它们分别是该`on_action`会触发的其他`on_action`列表、随机`on_action`列表和`first_valid on_action`列表，前面第二部分的权重就是在这里用的。
7. 接着的`effect`这里先不说明，因为我还没学。
8. 最后`fallback`有点类似于编程语言中的函数返回值，它表示该`on_action`定义的返回`on_action`。如果没有事件/on_actions由该`on_action`触发，则该返回`on_action`会被调用。注意，要避免创建无限的返回循环，否则游戏可能会被阻止推进时间。

## 例子

在这一部分中，我们写一个很基础朴素的`on_action`，来略微了解一下。

1. 首先我们要完成一个如下`on_action`：当角色结婚时，弹出第一个事件，1天后弹出第2个事件，10天或15天后弹出第3个事件，3个事件就很随意了。

2. 那么首先，我们先写3个事件：

   ```
   namespace = test_action
   test_action.1001 = {
   	type = character_event
   	title = test_action.1001.t
   	desc = test_action.1001.desc
   	theme = stewardship
   	left_portrait = root
   	option = {
   		name = test_action.1001.option.a
   	}
   	option = {
   		name = test_action.1001.option.b
   		trigger_event = give_money.1
   	}
   }
   
   test_action.1002 = {
   	type = character_event
   	title = test_action.1002.t
   	desc = test_action.1002.desc
   	theme = stewardship
   	left_portrait = root
   	option = {
   		name = test_action.1002.option.a
   	}
   }
   
   test_action.1003 = {
   	type = character_event
   	title = test_action.1003.t
   	desc = test_action.1003.desc
   	theme = stewardship
   	left_portrait = root
   	option = {
   		name = test_action.1003.option.a
   	}
   }
   ```

   这里要注意，`namespace`一定要写对（也就是数字id前面的字符串），不能写成别的，不然就找不到这些事件了。

   一个反例就是一开始我没注意，namespace写成了test，于是无论怎么搞都只会触发这个文件中的最后一个事件（test_action.1003）。

   事件对应的文本文件内容如下：

   ```
   l_simp_chinese:
    test_action.1001.t:0 "测试"
    test_action.1001.desc:0 "测试一下action。"
    test_action.1001.option.a:0 "好耶！"
    test_action.1001.option.b:0 "来点钱呗！"
   
    test_action.1002.t:0 "测试2"
    test_action.1002.desc:0 "测试一下多个事件。"
    test_action.1002.option.a:0 "嗯嗯！"
   
     test_action.1003.t:0 "测试3"
     test_action.1003.desc:0 "测试一下delay。"
     test_action.1003.option.a:0 "加油！"
   ```

3. 准备好事件以后，就可以开始写`on_action`文件了。根据需求，我们的`on_action`应该是`on_marriage`，其中只包含`events`块。内容如下：

   ```
   on_marriage = {
   	events = {
   		test_action.1001
   		delay = { days = 1 }
   		test_action.1002
   		delay = { days = { 10 15 } }
   		test_action.1003
   	}
   }
   ```

4. 接下来检查一下代码结构，看每个文件是否都在其对应的地方，我的代码结构如下：

   ```
   - common
   	- on_action
   		test_on_action.txt
   - events
   	test_action_events.txt
   - localization
   	-simp_chinese
   		-event_localization
   			test_action_l_simp_chinses.yml
   ```

   实际上文件名并不严格要求按这个来，文件层级也不是特别严格，比如events里再套一层文件夹也没关系。

   此外，`test_action_events`事件中某个选项会触发之前写过的`give_money_events`，这个事件和其相关文件在此就不列出了。

5. 接下来进游戏加载mod，可以看到如下效果：

   结婚当天（9月23日）触发第一个事件

   ![image-20201225205736201](https://i.loli.net/2020/12/25/24QNcT3LekhHtpA.png)

一天后（9月24日）触发第二个事件

![image-20201225205913496](https://i.loli.net/2020/12/25/yA7d2Hzn9tMolkp.png)

15天后（10月8日）触发第3个事件

![image-20201225210107079](https://i.loli.net/2020/12/25/PeXHlhZViFoK5zM.png)

## 其他说明

除了一些游戏本身已经写好的`on_action`以外，我们也可以自己写`on_action`，并在决议、事件或其他会触发`on_action`的时候调用，格式如下：

```
trigger_event = {
	on_action = on_action_name
	days/months/years = X  	# 可选项，可以没有
}
```

## 现有的`on_action`

在这一部分中我整理列出了游戏本体中的一系列`on_action`的名称和触发条件（其实是根据名称猜的，所以可能有错），具体效果就不说明了。（实在是太多了......）

|                        类别（文件名）                        |                             名称                             |                           触发条件                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                 结盟（alliance_on_actions）                  |                      on_alliance_added                       |                           结成同盟                           |
|                                                              |                     on_alliance_removed                      |                         同盟到期移除                         |
|                                                              |                      on_alliance_broken                      |                           同盟破裂                           |
|                   军队（army_on_actions）                    |                       on_army_monthly                        |                 玩家拥有的军队每30天自动触发                 |
|                                                              |                      on_county_occupied                      | 当一个county完全被占领时触发，如果是被province owner重新占领则不触发 |
|                                                              |                     on_siege_completion                      |   当围攻完成时触发，如果是被province owner重新占领则不触发   |
|                                                              |                       on_siege_looting                       |             当围攻掠夺某一土地时触发（？不确定）             |
|                                                              |                    on_army_enter_province                    |                  军队进入一个province时触发                  |
|                                                              |                     on_raid_action_start                     |                        突袭发生时触发                        |
|                                                              |                  on_raid_action_completion                   |                        突袭结束时触发                        |
|                                                              |                    on_raid_action_weekly                     |                   突袭过程中每7天触发一次                    |
|                                                              |                    on_raid_loot_delivered                    |           当袭击者返回自己的领土并存入战利品时触发           |
|                                                              |                     on_defeat_raid_army                      |              当突袭失败且战利品被盗时触发（？）              |
|                  战争（battle_on_actions）                   |                  on_battle_owner_battle_end                  |                  战争结束时，对战争双方触发                  |
|                                                              |                   on_commander_battle_end                    |               战争结束时，对战争双方指挥官触发               |
|                       生日（birthday）                       |                         on_birthday                          |                         每个生日触发                         |
|                                                              |                     on_specific_birthday                     |          几个特殊年龄的生日触发（3、6、10、15、16）          |
|                                                              |                    on_birthday_childhood                     |                 未成年且可以受教育的年龄触发                 |
|                                                              |                  on_graceful_aging_birthday                  |                    这个没注释，我也没看懂                    |
|                 人物等级（character_levels）                 |                     on_piety_level_gain                      |                      信仰等级提升时触发                      |
|                                                              |                     on_piety_level_loss                      |                      信仰等级降低时触发                      |
|                                                              |                    on_prestige_level_gain                    |                      声望等级提升时触发                      |
|                                                              |                    on_prestige_level_loss                    |                      声望等级降低时触发                      |
|              孩子出生（child_birth_on_actions）              |                       on_birth_mother                        |                      生娃时对孩他妈触发                      |
|                                                              |                       on_birth_father                        |                      生娃时对孩他爸触发                      |
|                                                              |                     on_birth_real_father                     |  生娃时对亲生父亲触发（只在亲生父亲和法理父亲不一样时触发）  |
|                                                              |                        on_birth_child                        |                         对新生儿触发                         |
|                                                              |                     on_pregnancy_mother                      |                     对怀孕状态的母亲触发                     |
|                                                              |                     on_pregnancy_father                      |         对怀孕状态的父亲触发（**这不是指男性怀孕**）         |
|                                                              |                  on_pregnancy_ended_mother                   |                     怀孕结束时对母亲触发                     |
|               儿童时期（childhood_on_actions）               |                    child_personality_gain                    |                     儿童性格特质等的获得                     |
|                                                              |                      childhood_ongoing                       |                         儿童时期触发                         |
|                                                              |                       on_3rd_birthday                        |                           3岁触发                            |
|                                                              |                       on_6th_birthday                        |                           6岁触发                            |
|                                                              |                       on_10th_birthday                       |                           10岁触发                           |
|                                                              |                       on_15th_birthday                       |                           15岁触发                           |
|                                                              |                       on_16th_birthday                       |                           16岁触发                           |
|                                                              |                   on_action_add_sexuality                    |                       获得性取向后触发                       |
|                                                              |                 on_birthday_education_events                 |                         教育事件触发                         |
|                                                              |                  childhood_education_boost                   |                     检查儿童是否需要教育                     |
|                                                              |                     childhood_education                      |                        教育事件的触发                        |
|                                                              |                childhood_education_patronage                 | patronage-forcused或者scholarly 配偶会给儿童教育加分，这个赞助我没理解是什么意思，scholarly应该是指学识属性高 |
|                  战斗（combat_on_actions）                   |                     on_combat_end_winner                     |                      对打赢了的一方触发                      |
|                                                              |                     on_combat_end_loser                      |                      对打输了的一方触发                      |
|                内阁（councillor_on_actions）                 |                       on_leave_council                       |                      内阁成员辞职时触发                      |
|                                                              |                    on_fired_from_council                     |                  领主从内阁解雇该角色时触发                  |
|                                                              |        organize_the_levies_marshal_task_side_effects         |                     军事统帅任务事件列表                     |
|                                                              |               train_commanders_success_effect                |                    训练指挥官成功事件列表                    |
|                                                              |          train_commanders_marshal_task_side_effects          |                  训练指挥官军事统帅事件列表                  |
|                                                              |              task_increase_control_side_effects              |                    增强控制力任务事件列表                    |
|                                                              |              task_foreign_affairs_side_effects               |                         外交事件列表                         |
|                                                              |              task_domestic_affairs_side_effects              |                         国内事务列表                         |
|                                                              |              task_integrate_title_side_effects               |                       合并头衔事件列表                       |
|                                                              |             task_fabricate_claim_success_effect              |                     伪造宣称成功事件列表                     |
|                                                              |               task_collect_taxes_side_effects                |                         收税事件列表                         |
|                                                              |               task_develop_county_side_effects               |                         发展事件列表                         |
|                                                              |              task_promote_culture_side_effects               |                         文化事件列表                         |
|                                                              |            task_religious_relations_side_effects             |                       宗教关系事件列表                       |
|                                                              |                  task_convert_side_effects                   |                       转变文化事件列表                       |
|                                                              |              task_fabricate_claim_side_effects               |                       伪造宣称事件列表                       |
|                                                              |              task_support_schemes_side_effects               |                       支持密谋事件列表                       |
|                                                              |              task_disrupt_schemes_side_effects               |                       打断密谋事件列表                       |
|                                                              |                  task_find_secrets_outcome                   |                        寻找秘密的结果                        |
|                                                              |              task_find_secrets_reveal_selection              |                       揭示秘密选择列表                       |
|                                                              |                task_find_secrets_side_effects                |                       寻找秘密事件列表                       |
|                 伯爵领（county_on_actions）                  |                    on_county_faith_change                    |                       伯爵领改信时触发                       |
|                                                              |                   on_county_culture_change                   |                      伯爵领改文化时触发                      |
|             宫廷（court_maintenance_on_actions）             |                        on_join_court                         |                      角色加入宫廷时触发                      |
|                                                              |                        on_leave_court                        |                      角色离开宫廷时触发                      |
|       廷臣宾客（courtier_guest_management_on_actions）       |             on_courtier_decided_to_move_to_pool              |                 廷臣打算离开时针对该廷臣触发                 |
|                                                              |              on_courtier_ready_to_move_to_pool               |                廷臣准备好离开时针对该廷臣触发                |
|                                                              |                  on_guest_arrived_from_pool                  |                   宾客到达时针对该宾客触发                   |
|                                                              |                on_guest_ready_to_move_to_pool                |                 宾客准备好离开时针对宾客触发                 |
|                  文化（culture_on_actions）                  |                    on_culture_era_changed                    |                  文化区域改变时对该文化触发                  |
|                                                              |                 on_character_culture_change                  |                   角色改文化时对该角色触发                   |
|                        死亡（death）                         |                           on_death                           |                          死亡时触发                          |
|                                                              |                on_natural_death_second_chance                |       角色即将自然死亡，但通过脚本获得第二次生命时触发       |
|                  宗族（dynasty_on_actions）                  |                      on_dynasty_created                      |                        建立宗族时触发                        |
|                    游戏开始（game_start）                    |                        on_game_start                         |               游戏开局一系列历史信息生成后触发               |
|                                                              |                  on_game_start_after_lobby                   |          进入游戏后触发，主要是和游戏规则相关的内容          |
|                  健康（health_on_actions）                   |                     yearly_health_pulse                      |                        每年的健康事件                        |
|                                                              |                    disease_outbreak_pulse                    |                       随机生病触发事件                       |
|                                                              |                    wounded_recovery_pulse                    |                       伤口恢复触发事件                       |
|                                                              |                        commit_suicide                        |                         决议自杀触发                         |
|               骑士团（holy_order_on_actions）                |                   on_holy_order_new_lease                    |                  骑士团获得领地（？不确定）                  |
|                                                              |                     on_holy_order_hired                      |                         骑士团被雇佣                         |
|                                                              |                   on_holy_order_destroyed                    |                         骑士团被解散                         |
|                  骑士（knight_on_actions）                   |                    on_knight_combat_pulse                    |                    战争中每天针对骑士触发                    |
|                                                              |                  on_commander_combat_pulse                   |                    战争中每天对指挥官触发                    |
|                                                              |                 on_commander_combat_finished                 |                      战争后对指挥官触发                      |
|                                                              |                   on_army_combat_finished                    |                   战争后对每个参战军队触发                   |
|                                                              |                      varangian_ongoing                       |                   针对Varangian guard触发                    |
|                 婚姻（marriage_concubinage）                 |                         on_marriage                          |                          结婚时触发                          |
|                                                              |                          on_divorce                          |                          离婚时触发                          |
|                                                              |                        on_concubinage                        |                    角色成为他人侧室时触发                    |
|                                                              |                      on_concubinage_end                      |                      角色不当侧室时触发                      |
|                                                              |                     on_betrothal_broken                      |                        婚约破裂时触发                        |
|                  监狱（prison_on_actions）                   |                         on_imprison                          |                          被抓时触发                          |
|                                                              |                    on_release_from_prison                    |                          被放时触发                          |
|                                                              |              quarterly_prison_maintenance_pulse              |                       住监狱过程中触发                       |
|                                                              |                       dungeon_ongoing                        |                       关地牢过程中触发                       |
|                                                              |                     house_arrest_ongoing                     |                          软禁时触发                          |
|                                                              |                      escape_from_prison                      |                      试图逃出监狱时触发                      |
|                                                              |                  escape_from_prison_success                  |                      成功逃出监狱时触发                      |
|                                                              |                  escape_from_prison_failure                  |                         逃跑失败触发                         |
|             领地（realm_maintenance_on_actions）             |              realm_maintenance_quarterly_pulse               |                    当领主的过程中随机触发                    |
|                                                              |               in_debt_county_corruption_pulse                |                        负债时增加腐败                        |
|                                                              |                      low_control_pulse                       |                控制力较低时触发（会增加腐败）                |
|                                                              |                     high_countrol_pulse                      | 高控制力时触发（移除腐败）（这里的countrol真的没问题吗...）  |
|                 宗教（religion_on_actions）                  |                       on_faith_created                       |                        创建信仰时触发                        |
|                                                              |                     on_faith_conversion                      |                        转变信仰时触发                        |
|                                                              |                  on_character_faith_change                   |                        人物改信时触发                        |
|                                                              |                       on_faith_monthly                       |                      每种信仰每个月触发                      |
|                                                              |           faith_holy_order_land_acquisition_pulse            |                          骑士团信仰                          |
|                                                              |                  faith_heresy_events_pulse                   |                           异端事件                           |
|                                                              |                  faith_fervor_events_pulse                   |                         宗教热情事件                         |
|                                                              |                    pagan_conversion_pulse                    |                        异教徒转变事件                        |
|                                                              |           on_potential_great_holy_war_invalidation           |                      圣战目标无效时触发                      |
|                                                              |                on_great_holy_war_invalidation                |                   上一个没找到新目标时触发                   |
|                                                              |               on_great_holy_war_countdown_end                |                  圣战开始的倒计时为0时触发                   |
|                                                              |            on_great_holy_war_participant_replaced            |                    圣战中角色被替换时触发                    |
|                    规则（ruler_designer）                    |                  on_ruler_designer_finished                  |   新人物设计好后触发（应该是指那个自定义统治者属性的玩法）   |
|                  秘密（secret_on_actions）                   |                       secrets_ongoing                        |                      处理秘密事件时触发                      |
| 压力应对决策（主要是不同特质会有特殊事件）（stress_coping_decisions_on_actions） |                     stress_loss_drunkard                     |                        酒鬼减压时触发                        |
|                                                              |                   stress_loss_hashishiyah                    |                       瘾君子减压时触发                       |
|                                                              |                      stress_loss_rakish                      |                       放荡者减压时触发                       |
|                                                              |                    stress_loss_reclusive                     |                        隐居减压时触发                        |
|                                                              |                    stress_loss_irritable                     |                        烦躁减压时触发                        |
|                                                              |                    stress_loss_flagellant                    |                      自鞭笞者减压时触发                      |
|                                                              |                    stress_loss_profligate                    |                        挥霍减压时触发                        |
|                                                              |                   stress_loss_improvident                    |                     缺乏远见者减压时触发                     |
|                                                              |                     stress_loss_contrite                     |                        懊悔减压时触发                        |
|                                                              |                  stress_loss_comfort_eater                   |                      食以慰藉减压时触发                      |
|                                                              |                   stress_loss_inappetetic                    |                      没有食欲减压时触发                      |
|                                                              |                    stress_loss_journaller                    |                      写日记者减压时触发                      |
|                                                              |                     stress_loss_confider                     |                       倾诉者减压时触发                       |
|                                                              |                     stress_loss_athletic                     |                        健身减压时触发                        |
|                  压力（stress_on_actions）                   |                         stress_pulse                         |               暂时忽略，注释说这个块可能会被删               |
|                                                              |                   on_stress_level_reduced                    |                      压力等级降低时触发                      |
|                                                              |                      on_stress_level_1                       |                     压力等级变为1时触发                      |
|                                                              |                      on_stress_level_2                       |                     压力等级变为2时触发                      |
|                                                              |                      on_stress_level_3                       |                     压力等级变为3时触发                      |
|                                                              |                      on_stress_level_4                       |                     压力等级变为4时触发                      |
|                                                              |                stress_threshold_level_1_event                |                    压力达到1级阈值时触发                     |
|                                                              |                stress_threshold_level_2_event                |                    压力达到2级阈值时触发                     |
|                                                              |                stress_threshold_level_3_event                |                    压力达到3级阈值时触发                     |
|                                                              |            stress_threshold_level_1_event_prison             |             角色在监狱中，压力达到1级阈值时触发              |
|                                                              |            stress_threshold_level_2_event_prison             |             角色在监狱中，压力达到2级阈值时触发              |
|                                                              |            stress_threshold_level_3_event_prison             |             角色在监狱中，压力达到3级阈值时触发              |
|                   头衔（title_on_actions）                   |                      on_title_destroyed                      |                 头衔被销毁时对头衔持有者触发                 |
|                                                              |                        on_title_gain                         |               头衔获得时对该头衔的新持有者触发               |
|                                                              |                  on_title_gain_inheritance                   |                        继承头衔时触发                        |
|                                                              |                   on_title_gain_usurpation                   |                        篡夺头衔时触发                        |
|                                                              |                        on_title_lost                         |                        失去头衔时触发                        |
|                                                              |                    on_explicit_claim_gain                    |                        获得宣称时触发                        |
|                                                              |                    on_explicit_claim_lost                    |                        失去宣称时触发                        |
|                                                              |                          on_rank_up                          |                   头衔改变使角色升级时触发                   |
|                                                              |                         on_rank_down                         |                   头衔改变使角色降级时触发                   |
|                                                              |                       on_vassal_gained                       |                        得到封臣时触发                        |
|                  特质（traits_on_actions）                   |                     assign_trait_events                      |                    到新压力等级时获取特质                    |
|                                                              |                  assign_trait_ai_on_action                   |                   AI随机获取特质的触发系统                   |
|                                                              |                    assign_trait_on_action                    |                         获取特殊特质                         |
|                                                              |                     ongoing_trait_events                     |                      持有特殊特质时触发                      |
|                       教程（tutorial）                       |                 on_game_start_with_tutorial                  |                       用教程开局时触发                       |
|                    战争（war_on_actions）                    |                      on_war_transferred                      |                    战争中防御者改变时触发                    |
|                                                              |                   on_join_war_as_secondary                   |          角色作为战争的次要攻击者/次要防御者时触发           |
|                                                              |                        on_war_started                        |                        战争开始时触发                        |
|                                                              |                     on_war_won_attacker                      |                      攻方赢得战争时触发                      |
|                                                              |                     on_war_won_defender                      |                      守方赢得战争时触发                      |
|                                                              |                      on_war_white_peace                      |                     战争无条件和平时触发                     |
|                                                              |                      on_war_invalidated                      |                        战争无效时触发                        |
|                  体重（weight_on_actions）                   |                      on_weight_changed                       |     体重改变时触发（会触发一系列前面健康中的on_action）      |
|                   巫师（witch_on_actions）                   |            convert_to_witchcraft_failure_outcome             |                       转巫师失败时触发                       |
|                  年度（yearly_on_actions）                   |                     yearly_global_pulse                      |                        每年1月1日触发                        |
|                                                              |                    yearly_playable_pulse                     |                  每年固定时间对可玩角色触发                  |
|                                                              |                  three_year_playable_pulse                   |                     每3年对可玩角色触发                      |
|                                                              |                   five_year_playable_pulse                   |                     每5年对可玩角色触发                      |
|                                                              |                   quarterly_playable_pulse                   |                     每季度对可玩角色触发                     |
|                                                              |                 random_yearly_playable_pulse                 |                  每年随机日期对可玩角色触发                  |
|                                                              |                 random_yearly_everyone_pulse                 |                  每年随机日期对所有角色触发                  |
|                                                              |                   five_year_everyone_pulse                   |                     每5年对所有角色触发                      |
|                                                              |                    three_year_pool_pulse                     |                       每3年对穷人触发                        |
|                                                              |                   prison_maintenance_pulse                   |                    角色监狱里关着人时触发                    |
|                                                              |                      ai_character_pulse                      |                           对AI触发                           |
|                                                              |                       on_yearly_events                       |                       每年随机日期触发                       |
|           活动-宴会（activities/feast_on_actions）           |                  feast_main_event_selection                  |                        选择宴会主事件                        |
|                                                              |                      feast_start_events                      |                         宴会开始触发                         |
|                                                              |                feast_default_event_selection                 |              默认宴会主事件中对参与者触发的事件              |
|                                                              |              feast_default_host_event_selection              |              默认宴会主事件中对举办者触发的事件              |
|                                                              |                    feast_default_fallback                    |                      宴会默认的返回事件                      |
|           活动-打猎（activities/hunt_on_actions）            |                      hunt_start_events                       |                        打猎开始时触发                        |
|                                                              |                      hunt_random_pulse                       |                      打猎过程中随机触发                      |
|                                                              |                        hunt_end_pulse                        |                         打猎结束触发                         |
|        活动-朝圣（activities/pilgrimage_on_actions）         |                       pilgrimage_setup                       |                           朝圣开始                           |
|                                                              |                  pilgrimage_journey_events                   |                        朝圣过程中触发                        |
|                                                              |                     hajj_journey_events                      |                     伊斯兰朝圣过程中触发                     |
|                                                              |              pilgrimage_journey_event_fallback               |                      朝圣默认的返回事件                      |
|                                                              |                pilgrimage_destination_events                 |                        朝圣目的地事件                        |
|                                                              |                   hajj_destination_events                    |                       伊斯兰目的地事件                       |
|                                                              |                 christian_destination_events                 |                       基督徒目的地事件                       |
|                                                              |                  jewish_destination_events                   |                       犹太人目的地事件                       |
|                                                              |                  muslim_destination_events                   |                       穆斯林目的地事件                       |
|                                                              |                   hindu_destination_events                   |                       印度教目的地事件                       |
|                                                              |                 buddhist_destination_events                  |                       佛教徒目的地事件                       |
|                                                              |                   jain_destination_events                    |                       耆那教目的地事件                       |
|                                                              |                zoroastrian_destination_events                |                       拜火教目的地事件                       |
|                                                              |                   norse_destination_events                   |                       挪威人目的地事件                       |
|                                                              |                  tengri_destination_events                   |                       腾格里目的地事件                       |
|                                                              |                   pagan_destination_events                   |                      无信仰者目的地事件                      |
|                                                              |            pilgrimage_generic_destination_events             |                        普通目的地事件                        |
|                                                              |                   pilgrimage_return_events                   |                        朝圣后返回事件                        |
|  生活方式-外交（lifestyles/diplomacy_lifestyle_on_actions）  |              diplomacy_lifestyle_progress_pulse              |                  外交生活事件，一年触发4次                   |
|                                                              |              diplomacy_lifestyle_ongoing_events              |              外交生活中的事件（包括了下面几个）              |
|                                                              |              diplomacy_lifestyle_common_events               |                外交生活中的普通事件（2年1次）                |
|                                                              |             diplomacy_lifestyle_uncommon_events              |              外交生活中的不普通事件（10年1次）               |
|                                                              |               diplomacy_lifestyle_rare_events                |               外交生活中的稀有事件（30年1次）                |
|                                                              |            diplomacy_lifestyle_unavailable_events            |                    外交生活中的不可能事件                    |
|                                                              |      diplomacy_foreign_special_1010_court_visit_events       |                         外交事件链1                          |
|                                                              | diplomacy_foreign_special_1010_court_visit_interesting_relation_spy_outcome_events |                         外交事件链2                          |
|                                                              |                   commission_epic_ongoing                    |                       制作家族史诗事件                       |
|   生活方式-普通（lifestyles/general_lifestyle_on_actions）   |                      on_perks_refunded                       |                 玩家改生活方式时退还技能触发                 |
|  生活方式-谋略（lifestyles/intrigue_lifestyle_on_actions）   |              intrigue_lifestyle_progress_pulse               |                  谋略生活事件，一年触发4次                   |
|                                                              |              intrigue_lifestyle_ongoing_events               |              谋略生活中的事件（包括了下面几个）              |
|                                                              |               intrigue_lifestyle_common_events               |                谋略生活中的普通事件（2年1次）                |
|                                                              |              intrigue_lifestyle_uncommon_events              |              谋略生活中的不普通事件（10年1次）               |
|                                                              |                intrigue_lifestyle_rare_events                |               谋略生活中的稀有事件（30年1次）                |
|                                                              |            intrigue_lifestyle_unavailable_events             |                    谋略生活中的不可能事件                    |
|  生活方式-学识（lifestyles/learning_lifestyle_on_actions）   |              learning_lifestyle_progress_pulse               |                  学识生活事件，一年触发4次                   |
|                                                              |              learning_lifestyle_ongoing_events               |              学识生活中的事件（包括了下面几个）              |
|                                                              |               learning_lifestyle_common_events               |                学识生活中的普通事件（2年1次）                |
|                                                              |              learning_lifestyle_uncommon_events              |              学识生活中的不普通事件（10年1次）               |
|                                                              |                learning_lifestyle_rare_events                |               学识生活中的稀有事件（30年1次）                |
|                                                              |            learning_lifestyle_unavailable_events             |                    学识生活中的不可能事件                    |
|                                                              |                book_translation_story_events                 |                    翻译书事件（？不确定）                    |
|                                                              |             mystic_communion_side_effect_events              |                   神秘圣餐事件（？不确定）                   |
|   生活方式-军事（lifestyles/martial_lifestyle_on_actions）   |               martial_lifestyle_progress_pulse               |                  军事生活事件，一年触发4次                   |
|                                                              |               martial_lifestyle_ongoing_events               |              军事生活中的事件（包括了下面几个）              |
|                                                              |               martial_lifestyle_common_events                |                军事生活中的普通事件（2年1次）                |
|                                                              |              martial_lifestyle_uncommon_events               |              军事生活中的不普通事件（10年1次）               |
|                                                              |                martial_lifestyle_rare_events                 |               军事生活中的稀有事件（30年1次）                |
|                                                              |             martial_lifestyle_unavailable_events             |                    军事生活中的不可能事件                    |
|                                                              |                horse_riding_accident_outcome                 |                         骑马意外事件                         |
|                                                              |              search_for_troops_event_selection               |                         搜索部队事件                         |
|                                                              |                 learn_commander_trait_offer                  |                     获得指挥官特质的机会                     |
|                                                              |                  learn_commander_trait_test                  |                     获得指挥官特质的测试                     |
|                                                              |                learn_commander_trait_success                 |                      成功获得指挥官特质                      |
|                                                              |                learn_commander_trait_failure                 |                      未能获得指挥官特质                      |
| 生活方式-管理（lifestyles/stewardship_lifestyle_on_actions） |             stewardship_lifestyle_progress_pulse             |                  管理生活事件，一年触发4次                   |
|                                                              |             stewardship_lifestyle_ongoing_events             |              管理生活中的事件（包括了下面几个）              |
|                                                              |             stewardship_lifestyle_common_events              |                管理生活中的普通事件（2年1次）                |
|                                                              |            stewardship_lifestyle_uncommon_events             |              管理生活中的不普通事件（10年1次）               |
|                                                              |              stewardship_lifestyle_rare_events               |               管理生活中的稀有事件（30年1次）                |
|                                                              |           stewardship_lifestyle_unavailable_events           |                    管理生活中的不可能事件                    |
|                                                              |                 map_painting_event_selection                 |                         绘制地图选项                         |
|                                                              |                     map_painting_outcome                     |                         绘制地图结果                         |
|                                                              |               stewardship_road_building_chain                |                          修路事件链                          |
|                                                              |               stewardship_road_building_events               |                           修路事件                           |
|                                                              |                stewardship_lifestyle_domesday                |                   末日审判事件（？不确定）                   |
|                                                              |              stewardship_lifestyle_domesday_end              |                 默认审判结束事件（？不确定）                 |
|                                                              |       stewardship_duty_special_2000_mediation_stage_1        |                  调解事件阶段1（？不确定）                   |
|                                                              |       stewardship_duty_special_2000_mediation_stage_2        |                  调解事件阶段2（？不确定）                   |
|                                                              |  stewardship_duty_special_2000_mediation_background_events   |                   调解背景事件（？不确定）                   |
|                                                              |                sell_titles_side_effect_events                |                      买卖头衔副作用事件                      |
|           关系-主教（relations/bishop_on_actions）           |                        bishop_ongoing                        |                       有主教时随机触发                       |
|           关系-父母（relations/parent_on_actions）           |                        parent_ongoing                        |                       有父母时随机触发                       |
|                                                              |                   parent_meddling_outcome                    |                        父母干涉的结果                        |
|          关系-关系（relations/relation_on_actions）          |               relation_upgrade_quarterly_pulse               |                         关系升级事件                         |
|                                                              |                    lover_quarterly_pulse                     |                    恋人事件（一季度一次）                    |
|                                                              |                        lover_ongoing                         |                     恋人事件（一年一次）                     |
|                                                              |                    lover_ongoing_fallback                    |                        恋人事件的返回                        |
|                                                              |                    lover_ongoing_distance                    |                    异地恋事件（一年一次）                    |
|                                                              |               lover_ongoing_distance_fallback                |                        异地恋事件返回                        |
|                                                              |                     lover_status_changes                     |                         恋爱状态改变                         |
|                                                              |                    on_set_relation_lover                     |                         有恋人时触发                         |
|                                                              |                   on_remove_relation_lover                   |                        失去恋人时触发                        |
|                                                              |                  friendship_quarterly_pulse                  |                    朋友事件（一季度一次）                    |
|                                                              |                  friendship_status_changes                   |                         朋友状态改变                         |
|                                                              |                      friendship_ongoing                      |                           朋友事件                           |
|                                                              |                    on_set_relation_friend                    |                           得到朋友                           |
|                                                              |                  on_remove_relation_friend                   |     失去朋友（只和关系破裂有关，不会因为朋友凉了而触发）     |
|                                                              |                   on_death_relation_friend                   |                           朋友去世                           |
|                                                              |                    rival_quarterly_pulse                     |                    仇敌事件（一季度一次）                    |
|                                                              |                    rival_relation_changes                    |                         仇敌改变事件                         |
|                                                              |                       rivalry_ongoing                        |                     仇敌事件（一年一次）                     |
|                                                              |                   rivalry_ongoing_fallback                   |                         仇敌事件返回                         |
|                                                              |                    on_set_relation_rival                     |                           得到仇敌                           |
|                                                              |                   on_remove_relation_rival                   |                           失去仇敌                           |
|                                                              |                mentor_student_quarterly_pulse                |                           师生事件                           |
|                                                              |               mentor_student_relation_changes                |                         师生关系改变                         |
|                                                              |                 on_remove_relation_guardian                  |                  解除监护人时对被监护人触发                  |
|                                                              |                  on_death_relation_guardian                  |           有监护人的被监护人死亡时，对被监护人触发           |
|                                                              |                   on_remove_relation_ward                    |                   解除监护人时对监护人触发                   |
|                                                              |                    on_death_relation_ward                    |            有被监护人的监护人死亡时，对监护人触发            |
|          关系-兄弟（relations/sibling_on_actions）           |                   sibling_quarterly_pulse                    |                    兄弟事件（一季度一次）                    |
|                                                              |                       sibling_statuses                       |                           兄弟状态                           |
|                                                              |                       ongoing_sibling                        |                           兄弟事件                           |
|           关系-配偶（relations/spouse_on_actions）           |                        spouse_ongoing                        |                           配偶事件                           |
|                                                              |             spouse_councillor_default_on_action              |                         配偶内阁事件                         |
|                                                              |                     court_politics_setup                     | 接下来的几个应该都是指配偶和内阁其他人的互动事件，不太确定，因此不写了。 |
|                                                              |                court_politics_task_on_action                 |                                                              |
|                                                              |                        chivalry_setup                        |                                                              |
|                                                              |                   chivalry_task_on_action                    |                                                              |
|                                                              |                     court_intrigue_setup                     |                                                              |
|                                                              |                court_intrigue_task_on_action                 |                                                              |
|                                                              |                     manage_domain_setup                      |                                                              |
|                                                              |                 manage_domain_task_on_action                 |                                                              |
|                                                              |                       patronage_setup                        |                         配偶赞助开始                         |
|                                                              |                   patronage_task_on_action                   |                         配偶赞助任务                         |
|           关系-封臣（relations/vassal_on_actions）           |                  on_vassal_become_powerful                   |                       封臣变强力时触发                       |
|                                                              |                        vassal_ongoing                        |                         有封臣时触发                         |
|            计谋-绑架（schemes/abduct_on_actions）            |                         abduct_setup                         |                           绑架开始                           |
|                                                              |                 standard_abduct_method_setup                 |              绑架方式（？没绑架过，不清楚流程）              |
|                                                              |                       abduct_succeeded                       |                           绑架成功                           |
|                                                              |                        abduct_failed                         |                           绑架失败                           |
|                                                              |                        abduct_ongoing                        |                          绑架进行中                          |
|           计谋-拉拢（schemes/befriend_on_actions）           |                       befriend_ongoing                       |                          交友进行中                          |
|                                                              |                   befriend_dislike_ongoing                   |                       和不喜欢的人交友                       |
|                                                              |                    befriend_rival_ongoing                    |                          和仇敌交友                          |
|                                                              |                  befriend_critical_success                   |                  交友成功关键点（？不确定）                  |
|                                                              |                       befriend_success                       |                           交友成功                           |
|                                                              |                       befriend_failure                       |                           交友失败                           |
|                                                              |                  befriend_critical_failure                   |                  交友失败关键点（？不确定）                  |
|       计谋-宣称权位（schemes/claim_throne_on_actions）       |                      claim_throne_setup                      |                           宣称开始                           |
|                                                              |              standard_claim_throne_method_setup              |                           宣称方法                           |
|                                                              |                    claim_throne_succeeded                    |                           宣称成功                           |
|                                                              |                     claim_throne_failed                      |                           宣称失败                           |
|                                                              |                     claim_throne_ongoing                     |                          宣称进行中                          |
|            计谋-廷臣（schemes/court_on_actions）             |                        court_ongoing                         |                           廷臣事件                           |
|                                                              |                        court_outcome                         |                         廷臣事件结果                         |
|            计谋-私奔（schemes/elope_on_actions）             |                        elope_ongoing                         |                          私奔进行时                          |
|        计谋-牵制（schemes/fabricate_hook_on_actions）        |             abricate_hook_method_success_outcome             |                       伪造牵制成功结果                       |
|                                                              |                    fabricate_hook_ongoing                    |                        伪造牵制进行中                        |
|            计谋-谋杀（schemes/murder_on_actions）            |                   murder_method_selection                    |                  （dbq这部分我懒得写了...）                  |
|                                                              |                infant_murder_method_selection                |                                                              |
|                                                              |                child_murder_method_selection                 |                                                              |
|                                                              |              incapable_murder_method_selection               |                                                              |
|                                                              |               dungeon_murder_method_selection                |                                                              |
|                                                              |             house_arrest_murder_method_selection             |                                                              |
|                                                              |               standard_murder_method_selection               |                                                              |
|                                                              |                         murder_setup                         |                                                              |
|                                                              |                       murder_save_roll                       |                                                              |
|                                                              |                     murder_save_response                     |                                                              |
|                                                              |                       murder_succeeded                       |                                                              |
|                                                              |                        murder_failed                         |                                                              |
|                                                              |                        murder_ongoing                        |                                                              |
|            计谋-勾引（schemes/seduce_on_actions）            |                  seduce_ongoing_milestone_1                  |                          勾引阶段1                           |
|                                                              |                  seduce_ongoing_milestone_2                  |                          勾引阶段2                           |
|                                                              |                        seduce_success                        |                           勾引成功                           |
|                                                              |                    seduce_generic_success                    |                           勾引成功                           |
|                                                              |                        seduce_failure                        |                           勾引失败                           |
|             计谋-拉拢（schemes/sway_on_actions）             |                         sway_success                         |                           拉拢成功                           |
|                                                              |                         sway_failure                         |                           拉拢失败                           |
|                                                              |                         sway_ongoing                         |                          拉拢进行时                          |
|    周期故事-猫（story_cycles/story_cycle_cat_on_actions）    |                      ongoing_cat_events                      |                       有猫时触发的事件                       |
|                                                              |           story_cycle_cat_pet_decision_on_actions            |                           决议养猫                           |
|    周期故事-狗（story_cycles/story_cycle_dog_on_actions）    |                      ongoing_dog_events                      |                        有狗时触发事件                        |
|                                                              |           story_cycle_dog_pet_decision_on_actions            |                           决议养狗                           |
| 周期故事-打猎神秘动物（story_cycle_hunt_mystical_animal_on_actions） |             ongoing_hunt_mystical_animal_events              |                       打猎时的神秘动物                       |
| 周期故事-谋杀系列（story_cycles/story_cycle_murders_at_court_on_actions） |                   murders_at_court_events                    |  谋杀系列事件（就是那个很奇怪可怕的发现尸体等等一系列事件）  |
|                                                              |                murders_at_court_confrontation                |                           最终结果                           |
| 周期故事-宴会？（story_cycles/story_cycle_party_baron_on_actions） |                      party_baron_events                      |       看本地化文件是宴会上的事情，不知道这个要怎么解释       |
| 周期故事-农民（story_cycles/story_cycle_peasant_affair_on_actions） |               peasant_affair_shows_up_at_court               |                   农民来时触发（？不确定）                   |
|                                                              |              peasant_affair_looking_for_peasant              |                           寻找农民                           |
|                                                              |               peasant_affair_peasant_at_court                |            农民来找你时触发（比如想让你认个儿子）            |
| 周期故事-重建罗马（story_cycles/story_cycle_roman_restoration_on_actions） |              on_action_roman_restoration_pulse               |                         重建罗马触发                         |
|                                                              |              on_action_orthodox_pentarchy_pulse              |                     拜占庭恢复五大牧首区                     |
| 周期故事-受尊敬的领主（story_cycles/story_cycle_statecraft_lifestyle_respected_liege_on_actions） |                ongoing_respected_liege_events                |            受尊敬的领主（这块代码还没写，是空的）            |
| 周期故事-战马（story_cycles/story_cycle_warhorse_on_actions） |                   ongoing_warhorse_events                    |                       有战马时随机触发                       |
|                                                              |                        breed_warhorse                        |                         战马繁殖事件                         |