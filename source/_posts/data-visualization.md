---
title: 数据可视化技巧
date: 2026-06-28 10:00:00
categories:
  - 学习
  - 绘图
tags:
  - R
  - ggplot2
  - 可视化
cover: https://picsum.photos/seed/viz/800/450
description: 用 R 的 ggplot2 绘制出版级图表。
---

介绍 ggplot2 绘图技巧。

```r
library(ggplot2)
ggplot(mtcars, aes(mpg, wt)) + geom_point() + theme_minimal()
```

（示例内容）
