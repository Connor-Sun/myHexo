---
title: Hexo 报错 can not read a block mapping entry
tags:
  - Hexo
  - 折腾
categories:
  - 工具
description: 在写博文的时候，Hexo频繁报错："can not read a block mapping entry; a multiline key may not be an implicit key at:"。网络上能查到的说法都是冒号后未添加空格的问题，但仔细排查了空格的格式后依然报错，最后发现是标题中的英文符号"[ ]"被识别成了 YAML 的数组引起的问题。
abbrlink: 29326
date: 2021-04-07 21:45:54
---

今天在编辑 LeetCode 4月打卡记录的帖子的时候，`hexo generate` 的过程中不断报出如下错误：

```error
  err: YAMLException: can not read a block mapping entry; a multiline key may not be an implicit key at line 2, column 5:
      tags: 算法
          ^
```

搜了下大多数人遇到这个报错是因为文章头中冒号后未加空格，`YAML` 中键值对使用冒号结构表示 `key: value`，且冒号后一定要添加空格。

报错时的文件头如下

```yaml
---
title: [LeetCode]2021-04打卡
tags: 算法
categories: 技术
description: LeetCode每日一题 2021年4月打卡记录
date: 2021-04-07 18:03:49
---
```

但仔细排查了几遍空格的格式仍然报错，试了很多方法，文件头改来改去最后发现是标题中的英文符号 `[]` 导致的，替换成中文的方括号就可以正常生成 `html` 了，猜测是英文符号 `[]` 会被识别成 `YAML数组`，导致格式不正确。