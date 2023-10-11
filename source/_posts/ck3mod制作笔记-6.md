---
title: ck3mod制作笔记-6
date: 2020-12-16 14:35:13
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
description: 这部分学习特质相关的内容。
---
# 特质

## 基础格式

1. 特质相关的文件有两个，第一个是对特质的说明，存放在文件夹`Crusader Kings III\game\common\traits`中，文件名为`00_traits.txt`，同目录下有一个`_traits.info`是说明怎么写特质；第二个是本地化文本文件，`Crusader Kings III\game\localization\simp_chinese\traits_l_simp_chinese.yml`，其他语言的也是类似路径。

2. 目前游戏里有277个特质，在[wiki](https://ck3.parawikis.com/wiki/%E7%89%B9%E8%B4%A8)中已经有人整理过特质的分类和效果等信息。

3. 从`_traits.info`中我们可以看到，一个特质`trait_<key>`对应的描述在本地化文本中应该是`trait_<key>_desc`，而默认图标是`gfx/interface/icons/traits/<trait>.dds`文件。

4. 特质默认加给玩家角色，也就是`root`。

5. 显然，一个最基础的特质`test_trait`由3部分组成：

   1. 特质图标：`gfx/interface/icons/traits/test_trait.dds`

   2. 特质内容：

      ```
      test_trait = {
      	index = 278
      	desc = {
      		first_valid = {
      			triggered_desc = {
      				trigger = {
      					NOT = { exists = this }
      				}
      				desc = trait_test_trait_desc
      			}
      			desc = trait_test_trait_character_desc
      		}
      	}
      }
      ```

   3. 特质文本描述：

      ```
      l_simp_chinese:
       # test traits
       trait_test_trait:0 "XXX"
       trait_test_trait_desc:0 "XXX"
       trait_test_trait_character_desc:0 "XXX" 
      ```

   4. 需要注意的是，在特质内容中，为了保证触发特效时该角色是存在的，我们需要进行`first_valid`来验证`Root`存在。

   5. 文本描述中，如果需要出现角色的名字，可以通过`[ROOT.GetCharacter.GetFirstNameNoTooltip]`来获取角色名。

6. 当我们按照上一条中，准备好对应的图标，并在mod文件夹中建立了`traits`文件夹和相应的特质文件，以及本地化内容也新建了文件，就会得到如下文件结构：

   ```
   - test
   	- common
   		- traits
   			01_traits.txt
   	- gfx
   		- interface
   			- icons
   				- traits
   					test_trait.dds
   	- localization
   		- simp_chinese
   			- traits_test_l_simp_chinese.yml
   	descriptor.mod
   ```

   需要注意的是，特质文件和本地化文件的文件名本身并不是很重要，但是如果和CK3官方的那俩文件重名，则需要把那两个文件中的内容也原封不动复制过来，否则本身自带的特质就会都没法用。

7. 准备好一系列文件以后，打开游戏通过`add_traits`指令增加特质，就可以看到对应的特质了。

## 一些具体设置

1. 除了上述的文本描述以外，特质还可以增加很多具体设置，例如对角色的影响、获取的触发条件等等。以下内容是对`_traits.info`文件的翻译。
2. `health`：表示该特质对健康的修正，例如在特质身心俱悉（`whole_of_body`）中，有一条`health = 0.5`，表示有该特质的角色健康+0.5。
3. `fertility`：表示该特质对生育力的修正百分比，例如还是身心俱悉中，有一条`fertility = 0.2`表示该角色的生育力+25%。
4. `inherit_chance`：表示该特质遗传给孩子的概率，例如特质梅毒`great_pox`中有一条`inherit_chance = 10`，表示遗传概率是10%。
5. `birth`：表示有百分之多少的人会带着这个特质出生。
6. `random_creation`：表示有百分之多少的人会带着这个特质被创造出来。和出生不一样的是，创造是指通过命令行生成和修改人物。不过根据检索，`00_traits`文件里的`birth`和`random_creation`都是一起出现的，且值相等。
7. `triggered_opinion`：这一条应该是针对角色好感度修正特质的触发器，它包括以下内容：
   1. `opinion_modifier`：修正的好感度类型
   2. `parameter`：要检查的布尔值参数
   3. `check_missing`：检查参数是否没设置（或设置为 false），而不是设置为 true
   4. `same_faith`：相同信仰
   5. `same_dynasty`：相同宗族
   6. `ignore_opinion_value_if_same_trait`：设置为`yes`的时候，会对有相同特质的角色不进行好感度修正，但是惩罚理由还在
   7. `male_only`和`female_only`很显然分别是仅限男性和仅限女性
8. `compatibility`：这是用于`compatibility_modifier`和`trait_compatibility`这两个触发器的，是检查特质持有者与列出的特质的兼容性。感觉是指获取某一特质后，再获取其他特质的概率会受这个列表值的修正。具体怎么用还不太明白，之后补充。
9. `parent_inheritance_sex`：可以是`male/female/all`，表示该特质可以从父亲/母亲/双亲继承。
10. `child_inheritance_sex`：值同上，表示该特质可以遗传给男孩/女孩/所有孩子。
11. `genetic`：默认是`no`，如果被设置为`yes`，则表示特征可以是非活动状态。子级可以从活动和非活动父特征中继承该特征。活动特征继承为 100% 的机会，非活动特征为 50% 的机会。如果从父母两个成功继承该特征，则该特征将变为活动状态。如果仅从一个父级继承，则它是非活动状态。（可以理解成生物学基因的隐性显性）。
12. `inherit_from_real_father`：是否可以从亲生父亲那里继承，其实不太懂这个和前面`parent_inheritance_sex`有什么不一样的。
13. `enables_inbred`：概括来说这个标志为`yes`时意味着这个特质是近亲繁殖的。
14. `good`：特质标识为好的（默认值是`no`，表示坏的）。
15. `genetic_constraint_all`：这种遗传约束将在获得特征时应用。
16. `forced_portrait_age_index`：年龄索引，可以设置多个，但是我还不知道年龄索引是什么东西，以后补充。
17. `portrait_extremity_shift`：这个值是一个表示百分比的小数，角色获得对应特质时，每个变形基因都会按这个百分比向 0 或 1 移动，具体是0还是1看基因本身接近哪个，比如该值为0.25， 基因值为0.4，则0.4将向0偏移0.25，从而变成0.3。
18. `ugliness_portrait_extremity_shift`：和上一条一样，但是获得此特质时，与角色最极端的单一特征（在公共/基因中定义）相关的每个变形基因都会按此百分比向 0 或 1 移动。关于这里的特征、基因等词还不太了解，以后再补充。
19. `immortal`：设置为`yes`后，获得该特质的角色会停止视觉老化，使角色对自然死亡免疫。仍然可以被脚本杀死。生育能力将匹配视觉年龄。可以使用`set_immortal_age`更改视觉年龄。
20. `physical, health_trait, fame`：这三个设置为`yes`则分别表示该特质是和物理、健康、名誉相关。
21. `trait_exclusive_if_realm_contains`：这个值是`{}`，大括号里可以填东西，表示随机分配给指挥官时考虑的地形类型列表。只有当指挥官的文化包含具有列表中指定的地形类型之一的省时，才分配/考虑这些特征。
22. `blocks_from_claim_inheritance`：设置为`yes`时，有该特质的角色无法继承头衔宣称。
23. `blocks_from_claim_inheritance_from_dynasty`：同上，设置为`yes`时，有该特质的角色无法从宗族成员那里继承头衔宣称。
24. `shown_in_ruler_designer`：默认是`yes`，设置为`yes`时，可以在自定义角色的那个地方的特质列表中看到该特质。
25. `ruler_designer_cost`：默认是0，同上，是指自定义角色时该特质消耗的点数。
26. `valid_sex`：可以是`all/male/female`，默认是`all`，表示该特质可以由什么性别的角色获得。

## 如何使角色得到特质

和命令行一样，在`mod`里需要角色获取特质的时候，`add_trait = trait_ID`即可。