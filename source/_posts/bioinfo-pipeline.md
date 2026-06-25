---
title: 生物信息学分析流程入门
date: 2024-02-15 10:00:00
categories:
  - 学习
  - 生信
tags:
  - 生物信息学
  - 流程
cover: https://picsum.photos/seed/bio/800/450
description: 介绍常见的生物信息学分析流程与工具。
swiper_index: 2
---

本文介绍生物信息学中常见的分析流程。

## 常用工具

- 序列比对：BWA、Bowtie2
- 变异检测：GATK、bcftools
- 注释：VEP、ANNOVAR

```bash
bwa mem ref.fa read1.fq read2.fq | samtools sort -o out.bam
```

（示例内容，后续替换为真实文章）
