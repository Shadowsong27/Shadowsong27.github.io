---
title: "RT Log 5: 关于Mac系统中文打字卡顿问题"
metaAlignment: center
coverMeta: out
date: 2021-08-13
categories:
- random thoughts
---

这个问题说实话有点烦。
<!--more-->

本来是在写一个随想的，结果在notion里写中文打着打着就感觉卡顿十分明显，之前特别卡的时候已经把touchbar联想关闭了，所以我想如果没关应该会更糟糕。但是基本来说，我发现主要的问题应该是有好几层。

1. notion本身输入中文的时候是有一定卡顿的。这个主要是通过对比sublime text输入的时候， 很明显作为一个轻量级的文字编辑器，sublime对中文输入的反应更快一些
2. filevault加密也会影响到输入卡顿。一个显著的特征就是当我把电源拔掉之后，似乎就不那么卡了，虽然backspace依旧会有一丢丢卡顿，但是比连着电源的效果好一些。按照网上的说法是连着电源会自动开启filevault，用电池会自动关
3. 最后我觉得就是系统级别的问题了，我工作的时候用microsoft teams，那个中文输入法加上microsoft teams本身就足够垃圾，我经常卡的动都动不了。所以哪怕是跟中国同事感觉用英文交流还舒畅一些，尤其是那种需要中英切换的时候，microsoft teams能直接给你卡死 （当然这个锅主要是在MS而不是在Mac）

解决方案我感觉目前没有，只能等系统update了。回滚Catalina可能也有用但是我不太确认提升有多大，总之这个事情确实还是蛮影响心情和效率。想想用英文在Terminal里健步如飞的酸爽，再看看现在中文输入的步履蹒跚，哎。

我这个电脑还是32Gb ram，我都觉得有时候开多了pycharm windows会卡，可能是时候转战vscode了。