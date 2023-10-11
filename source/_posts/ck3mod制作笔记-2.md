---
title: ck3mod制作笔记-2
date: 2020-10-04 10:42:04
tags:
  - 游戏相关
  - ck3
  - mod
categories: CK3
---
这次了解一下决议和事件的图标相关的事情。

<!--more-->

# 关于图片

1. 游戏中出现的过场图、背景图、事件图标等等基本存放在`...\Crusader Kings III\game\gfx\interface`文件夹中，比如`...\interface\bookmarks`里存着开场选人物时的界面贴图，而`...\interface\illustrations`文件夹里则存着很多尺寸大一些的图（例如过场图、决议说明界面的图等等）；
2. 这些贴图都是dds格式，不同使用场景的贴图有不同尺寸，可以给PS装dds插件然后打开查看；
3. 以决议为例，代码段落中会有`picture`这个选项，例如`create_cadet_branch_decision`中的是`picture = "gfx/interface/illustrations/decisions/decision_dynasty_house.dds"`，如果要自己写决议，或者想把某个决议的图像修改成其他样式，就在这里进行替换，注意dds文件的尺寸等；
4. 开场`bookmarks`的贴图和上面的不太一样，经过反复分析我发现，在bookmarks文件（`...\Crusader Kings III\game\common\bookmarks\00_bookmarks.txt`）中，预设的六个是`bm_867_northmen`、`bm_867_adventurers`等等，而在`...\Crusader Kings III\game\gfx\interface\bookmarks`中存在和这些名字相同的六个dds文件，每个可选的人物也类似，总之是dds文件的名字要和bookmarks文件里设定的选项保持一致，这样游戏里会自动对应起来。
5. 图片暂时就先看到这里。

# 关于文本

1. 游戏中出现的文本存放在`...\Crusader Kings III\game\localization`文件夹中，每个语言有一个文件夹；

2. 每个文本文件是`.yml`格式的，可以用记事本的编辑器打开修改；

3. 文本内容格式是：`文本名称:0 "text"`，这其中的0也见过写成1的，目前不知道是什么区别，也许和路径有关吧。文本名称的命名有固定格式，比如bookms的命名方式是`bm_year_bookmarkname`，而对应的描述则是`bm_year_bookmarkname_desc`，如下是一个例子；

   ```
   bm_1066_conqueror:0 "Norman England"
   bm_1066_conqueror_desc:0 "William has defeated the Anglo-Saxon king Harold at the Battle of Hastings and is crowned king of England, starting an age of Norman rule over the kingdom."
   ```

4. 在使用的时候，注意名称的统一即可，例如：

   在common文件夹的决议文件中，成立葡萄牙的决议如下：

   ```
   form_portugal_decision = {
   	picture = "gfx/interface/illustrations/decisions/decision_dynasty_house.dds"
   	major = yes
   	ai_check_interval = 60
   	desc = form_portugal_decision_desc
   ......
   ......
   ```

   那么对应的文本内容则是：

   ```
    form_portugal_decision:0 "成立葡萄牙"
    form_portugal_decision_desc:0 "一旦我控制了伊比利亚半岛的西海岸，我就能成立葡萄牙王国，并且建立自己独特的文化。"
   ```

5. 目前先看到这里，还有很多问题之后再说。