# 仿 biojuse.com 博客 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: 用 superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 逐任务实现。步骤用 `- [ ]` 复选框跟踪。

**Goal:** 用 Hexo + Butterfly 主题搭建一个属于颜浩轩的、仿 biojuse.com 布局与功能的全功能静态博客，并通过 GitHub Actions 部署到 GitHub Pages。

**Architecture:** Hexo 6 作为静态站点生成器，Markdown 文章存放于 `source/_posts/`；Butterfly 主题提供布局与组件；双栏卡片/Swiper 轮播/Giscus 评论/busuanzi 统计等通过插件与 CDN 注入；推送到 `main` 触发 GitHub Actions 构建，`peaceiris/actions-gh-pages` 将 `public/` 推到 `gh-pages` 分支供 Pages 访问。

**Tech Stack:** Hexo 6、hexo-theme-butterfly（4.x/5.x）、hexo-renderer-pug/stylus、hexo-wordcount、hexo-generator-searchdb、hexo-butterfly-swiper、hexo-deployer-git、hexo-server、Giscus、busuanzi、Font Awesome、Fancybox、GitHub Actions（Node 20 + peaceiris/actions-gh-pages@v4）。

## Global Constraints

- 博主身份：作者「颜浩轩」、院校「清华大学」、邮箱 `1741426978@qq.com`、GitHub `RosyStorm`、仓库 `blog`。
- Pages 地址：`https://rosystorm.github.io/blog/`（子路径，`url` 不带尾斜杠，`root: /blog/` 带首尾斜杠）。
- 版权底线：**绝不搬运** biojuse.com 原文，内容一律占位/示例。
- 语言：`zh-CN`；时区：`Asia/Shanghai`；主色 `#49B1F5`（Butterfly 默认蓝，与 biojuse 一致）。
- 提交规范：conventional commits（feat/docs/chore/...），不加 attribution。
- 所有配置字段必须基于真实 Butterfly 字段名；`code_blocks`（带 s）、颜色值必须用双引号。
- `package-lock.json` 必须入库（保证 `npm ci` 可重复构建）。

> **关于"测试"的说明**：本项目为配置驱动的静态站点，无传统单元测试。每个任务的"测试"适配为**本地构建/预览验证**（`hexo clean && hexo g` 无报错、`hexo s` 预览检查目标元素）。保持 TDD 的小步、可验证、频繁提交精神。

---

## File Structure

| 文件 | 职责 | 创建任务 |
|---|---|---|
| `package.json` | 依赖声明与脚本 | Task 1 |
| `_config.yml` | Hexo 站点配置（标题/作者/url/permalink/search/swiper） | Task 1 |
| `.gitignore` | 忽略 node_modules/public/db.json 等 | Task 1 |
| `_config.butterfly.yml` | 主题配置（菜单/暗黑/侧边栏/封面/inject/comments/busuanzi） | Task 2/3/7 |
| `source/css/custom.css` | 背景人物 + 半透明卡片自定义样式 | Task 6 |
| `source/pic/*.png` | 背景人物图片（日/夜） | Task 6 |
| `source/about/index.md` | 关于页 | Task 4 |
| `source/link/index.md` | 友链页 | Task 4 |
| `source/messageboard/index.md` | 留言板 | Task 4 |
| `source/daily/index.md` | 琐碎日常（占位） | Task 4 |
| `source/changelog/index.md` | 更新日志 | Task 4 |
| `source/404.html` | 404 页 | Task 4 |
| `source/_posts/*.md` | 6-8 篇示例文章 | Task 5 |
| `.github/workflows/deploy.yml` | 自动部署 | Task 8 |

---

## Task 1: Hexo 项目骨架与依赖

**Files:**
- Create: `package.json`
- Create: `_config.yml`
- Create: `.gitignore`

**Interfaces:**
- Produces: 可运行的 Hexo 站点骨架（`hexo g` 能生成 `public/`），供后续任务填充主题与内容。

- [ ] **Step 1: 创建 `package.json`**

写入以下内容（依赖锁定 Hexo 6 + 主题 + 各插件）：

```json
{
  "name": "blog",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "build": "hexo clean && hexo g",
    "server": "hexo s",
    "deploy": "hexo clean && hexo g && hexo d"
  },
  "hexo": {
    "version": "6.3.0"
  },
  "dependencies": {
    "hexo": "^6.3.0",
    "hexo-theme-butterfly": "^5.3.3",
    "hexo-renderer-pug": "^3.0.0",
    "hexo-renderer-stylus": "^3.0.0",
    "hexo-renderer-marked": "^6.0.0",
    "hexo-wordcount": "^6.0.1",
    "hexo-generator-searchdb": "^1.4.1",
    "hexo-butterfly-swiper": "^1.0.12",
    "hexo-deployer-git": "^4.0.0",
    "hexo-server": "^3.0.0"
  }
}
```

- [ ] **Step 2: 创建 `.gitignore`**

```gitignore
node_modules/
public/
.deploy_git/
db.json
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.DS_Store
Thumbs.db
.idea/
.vscode/
*.swp
```

- [ ] **Step 3: 创建 `_config.yml`（站点配置）**

```yaml
# Hexo 站点配置
title: 颜浩轩的博客
subtitle: 记录与分享
description: 颜浩轩的个人技术博客
keywords: 博客,技术,清华,生信
author: 颜浩轩
language: zh-CN
timezone: Asia/Shanghai

# 网址（子路径部署，root 必须带首尾斜杠）
url: https://rosystorm.github.io/blog
root: /blog/
permalink: :year/:month/:day/:title/
permalink_defaults:
  lang: zh-cn

# 目录
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# 写作
new_post_name: :title.md
default_layout: post
titlecase: false
external_link:
  enable: true
  field: site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
syntax_highlighter: highlight.js

# 分类与标签
default_category: uncategorized
category_map:
tag_map:

# 日期/时间格式
metadata:
  author_id: default
  language: ''
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# 分页
per_page: 10
pagination_dir: page

# 主题
theme: butterfly

# 部署（本地 hexo d 备用；正式上线用 GitHub Actions）
deploy:
  type: git
  repo: https://github.com/RosyStorm/blog.git
  branch: gh-pages

# 本地搜索（hexo-generator-searchdb）
search:
  path: search.xml
  field: post
  content: true
  format: html
  limit: 10000

# Swiper 首页置顶轮播（hexo-butterfly-swiper）
swiper:
  enable: true
  priority: 5
  enable_page: /
  timemode: date
  layout:
    type: id
    name: recent-posts
    index: 0
  error_img: /img/default.png
  insertposition: afterbegin
  default_descr: 这是一篇示例文章，后续将替换为真实内容。
  swiper_css: https://npm.elemecdn.com/hexo-butterfly-swiper/lib/swiper.min.css
  swiper_js: https://npm.elemecdn.com/hexo-butterfly-swiper/lib/swiper.min.js
  custom_css: https://npm.elemecdn.com/hexo-butterfly-swiper/lib/swiperstyle.css
  custom_js: https://npm.elemecdn.com/hexo-butterfly-swiper/lib/swiper_init.js

# 包含/排除
include:
exclude:
ignore:
```

- [ ] **Step 4: 安装依赖**

Run: `cd "c:/Users/颜浩轩/OneDrive/桌面/GitHub/MDBOOK/Web" && npm install`
Expected: 安装完成，生成 `node_modules/` 与 `package-lock.json`，无 ERROR。

- [ ] **Step 5: 验证 Hexo 可生成**

Run: `npx hexo clean && npx hexo g`
Expected: 生成 `public/` 目录，输出 `INFO  Files generated in ...`，无报错。（此时是默认 landscape 主题，下一任务换 butterfly。）

- [ ] **Step 6: 提交**

```bash
git add package.json package-lock.json _config.yml .gitignore
git commit -m "feat: 初始化 Hexo 站点骨架与依赖"
```

---

## Task 2: Butterfly 主题完整配置

**Files:**
- Create: `_config.butterfly.yml`

**Interfaces:**
- Consumes: Task 1 的 `theme: butterfly`。
- Produces: 完整主题外观（导航/暗黑/侧边栏/封面/字数统计/版权/社交），供 Task 3 注入插件、Task 6 注入背景。

- [ ] **Step 1: 创建 `_config.butterfly.yml`**

```yaml
# ============================================================
# _config.butterfly.yml  仿 biojuse.com 主题配置
# 字段名严格对齐 butterfly 4.x/5.x
# ============================================================

# ---------- 导航菜单 ----------
menu:
  首页: / || fas fa-home
  文章||fas fa-list:
    归档: /archives/ || fas fa-archive
    标签: /tags/ || fas fa-tags
    分类: /categories/ || fas fa-folder-open
  留言板: /messageboard/ || fas fa-comment-dots
  友链: /link/ || fas fa-link
  我自己: https://github.com/RosyStorm || fas fa-user
  关于: /about/ || fas fa-info-circle
  杂项||fas fa-th-large:
    琐碎日常: /daily/ || fas fa-coffee
    更新日志: /changelog/ || fas fa-history

nav:
  display_title: true
  display_post_title: true
  fixed: false

# ---------- 代码块 ----------
code_blocks:
  theme: light
  macStyle: false
  height_limit: false
  word_wrap: false
  copy: true
  language: true
  shrink: false
  fullpage: false

# ---------- 社交 ----------
social:
  fab fa-github: https://github.com/RosyStorm || Github || '#24292e'
  fas fa-envelope: mailto:1741426978@qq.com || Email || '#4a7dbe'
  fas fa-rss: /blog/atom.xml || RSS || '#FFA500'

# ---------- 暗黑模式 ----------
darkmode:
  enable: true
  button: true
  autoChangeMode: 1
display_mode: light

# ---------- 主题色（颜色值必须加双引号）----------
theme_color:
  enable: true
  main: "#49B1F5"
  paginator: "#00c4b6"
  button_hover: "#FF7242"
  text_selection: "#00c4b6"
  link_color: "#99a9bf"
  meta_color: "#858585"
  hr_color: "#A4D8FA"
  code_foreground: "#F47466"
  code_background: "rgba(27, 31, 35, .05)"
  toc_color: "#00c4b6"
  blockquote_padding_color: "#49b1f5"
  blockquote_background_color: "#49b1f5"
  scrollbar_color: "#49b1f5"
  meta_theme_color_light: "ffffff"
  meta_theme_color_dark: "#0d0d0d"

background: "var(--global-bg)"

# ---------- 侧边栏 ----------
aside:
  enable: true
  hide: false
  button: true
  mobile: true
  position: right
  display:
    archive: true
    tag: true
    category: true
  card_author:
    enable: true
    description: 清华大学 · 保持热爱，奔赴山海
    button:
      enable: true
      icon: fab fa-github
      text: Follow Me
      link: https://github.com/RosyStorm
  card_announcement:
    enable: true
    content: 欢迎来到颜浩轩的博客！人生是旷野，不是轨道。请保持批判思维，先思考后行动。
  card_recent_post:
    enable: true
    limit: 5
    sort: date
  card_newest_comments:
    enable: false
    limit: 6
    storage: 10
  card_categories:
    enable: true
    limit: 8
    expand: none
  card_tags:
    enable: true
    limit: 40
    color: false
    orderby: random
    order: 1
  card_archives:
    enable: true
    type: monthly
    format: YYYY年MM月
    order: -1
    limit: 8
  card_webinfo:
    enable: true
    post_count: true
    last_push_date: true
    runtime_date: 2024/01/01 00:00:00

# ---------- 封面 ----------
cover:
  index_enable: true
  aside_enable: true
  archives_enable: true
  default_cover:
    - https://picsum.photos/seed/blog1/800/450
    - https://picsum.photos/seed/blog2/800/450
    - https://picsum.photos/seed/blog3/800/450

# ---------- 顶部图 ----------
default_top_img: https://picsum.photos/seed/topimg/1920/1080
index_img: https://picsum.photos/seed/indextop/1920/600
disable_top_img: false

# ---------- 头像 ----------
avatar:
  img: https://avatars.githubusercontent.com/u/185143493?v=4
  effect: false

favicon: https://avatars.githubusercontent.com/u/185143493?v=4

# ---------- 文章 ----------
post_meta:
  page:
    date_type: created
    date_format: date
    categories: true
    tags: false
    label: true
  post:
    position: left
    date_type: both
    date_format: date
    categories: true
    tags: true
    label: true

post_copyright:
  enable: true
  decode: false
  license: CC BY-NC-SA 4.0
  license_url: https://creativecommons.org/licenses/by-nc-sa/4.0/

related_post:
  enable: true
  limit: 6
  date_type: created

post_pagination: 1

# ---------- 字数统计 ----------
wordcount:
  enable: true
  post_wordcount: true
  min2read: true
  total_wordcount: true

# ---------- 右下角 ----------
readmode: true
rightside_scroll_percent: true

# ---------- 本地搜索 ----------
search:
  enable: true
  path: search.xml
  field: post
  placeholder: 请输入关键字

# ---------- 访问统计 ----------
busuanzi:
  enable: true
  site_uv: true
  site_pv: true
  page_pv: true

# ---------- inject（Task 3/6 会在此追加）----------
inject:
  head:
  bottom:
```

- [ ] **Step 2: 验证主题加载**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected: 启动 `http://localhost:4000/blog/`，浏览器打开能看到 Butterfly 主题外观（顶部导航、首页文章列表、右侧侧边栏、暗黑切换按钮）。`Ctrl+C` 停止。

- [ ] **Step 3: 提交**

```bash
git add _config.butterfly.yml
git commit -m "feat: 配置 Butterfly 主题（菜单/暗黑/侧边栏/封面/字数统计）"
```

---

## Task 3: 双栏文章卡片 + Swiper 轮播顶图

**Files:**
- Modify: `_config.butterfly.yml`（inject 段）

**Interfaces:**
- Consumes: Task 1 的 `swiper` 配置、Task 2 的 `inject` 段。
- Produces: 首页双栏卡片瀑布流 + 置顶轮播（依赖 Task 5 文章的 `swiper_index` 才会显示轮播内容）。

- [ ] **Step 1: 在 `_config.butterfly.yml` 的 `inject.bottom` 注入双栏 CSS**

把 Task 2 的 `inject` 段改为：

```yaml
inject:
  head:
  bottom:
    - <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/Zfour/Butterfly-double-row-display@1.00/cardlistpost.min.css">
```

- [ ] **Step 2: 验证双栏生效**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected: 首页文章列表变为**双列卡片**布局（需有文章，可临时新建一篇测试；正式文章在 Task 5）。`Ctrl+C` 停止。

- [ ] **Step 3: 提交**

```bash
git add _config.butterfly.yml
git commit -m "feat: 启用首页双栏文章卡片（Zfour double-row-display）"
```

> 说明：Swiper 轮播配置已在 Task 1 的 `_config.yml`（`swiper:` 段）完成，无需额外步骤；轮播内容依赖 Task 5 文章的 `swiper_index` 字段，将在 Task 5 验证。

---

## Task 4: 自定义页面（关于/友链/留言板/404/日常/更新日志）

**Files:**
- Create: `source/about/index.md`
- Create: `source/link/index.md`
- Create: `source/messageboard/index.md`
- Create: `source/daily/index.md`
- Create: `source/changelog/index.md`
- Create: `source/404.html`

**Interfaces:**
- Consumes: Task 2 导航菜单指向的路径。
- Produces: 导航菜单所有链接可达。

- [ ] **Step 1: 创建 `source/about/index.md`**

```markdown
---
title: 关于
date: 2024-01-01 00:00:00
type: about
layout: about
top_img: https://picsum.photos/seed/about/1920/600
comments: false
---

## 关于博主

我是**颜浩轩**，就读于**清华大学**。这里是我的个人博客，用来记录学习与生活。

- GitHub：[@RosyStorm](https://github.com/RosyStorm)
- 邮箱：1741426978@qq.com

## 关于本站

本站基于 [Hexo](https://hexo.io/) + [Butterfly](https://butterfly.js.org/) 主题搭建，部署在 GitHub Pages。

## 声明

本站所有内容仅代表个人观点，不一定适用所有人。世界是运动的，部分内容可能随时间过时，阅读时请保持批判思维。
```

- [ ] **Step 2: 创建 `source/link/index.md`**

```markdown
---
title: 友情链接
date: 2024-01-01 00:00:00
type: link
top_img: https://picsum.photos/seed/link/1920/600
comments: true
---

## 友人链接

> 欢迎互换友链！可在下方留言或邮件联系。

{% linklist %}
- name: 颜浩轩的博客
  desc: 记录与分享
  avatar: https://avatars.githubusercontent.com/u/185143493?v=4
  link: https://rosystorm.github.io/blog/
{% endlinklist %}
```

> 注：Butterfly 友链默认用 flink 写法，若 `{% linklist %}` 不识别，改为在 `_config.butterfly.yml` 的 `flink_list` 配置项写友链数据。验证时若渲染异常，按 gotcha 切换。

- [ ] **Step 3: 创建 `source/messageboard/index.md`**

```markdown
---
title: 留言板
date: 2024-01-01 00:00:00
type: messageboard
top_img: https://picsum.photos/seed/msg/1920/600
comments: true
---

## 留言板

欢迎在这里留下你想说的话！留言使用 GitHub Discussions，登录 GitHub 账号即可评论。
```

- [ ] **Step 4: 创建 `source/daily/index.md`**

```markdown
---
title: 琐碎日常
date: 2024-01-01 00:00:00
type: daily
top_img: https://picsum.photos/seed/daily/1920/600
comments: true
---

## 琐碎日常

记录生活中的点滴。（占位页面，后续填充）
```

- [ ] **Step 5: 创建 `source/changelog/index.md`**

```markdown
---
title: 更新日志
date: 2024-01-01 00:00:00
type: changelog
top_img: https://picsum.photos/seed/changelog/1920/600
comments: false
---

## 更新日志

### 2024-01-01
- 博客首次上线 🎉
- 基础框架搭建完成
```

- [ ] **Step 6: 创建 `source/404.html`**

```html
---
permalink: /404.html
---

<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>404 - 页面走丢了</title>
  <style>
    body{margin:0;height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;background:#f5f5f5;font-family:sans-serif;color:#333;text-align:center}
    h1{font-size:120px;margin:0;color:#49B1F5}
    p{font-size:18px}
    a{display:inline-block;margin-top:20px;padding:10px 24px;background:#49B1F5;color:#fff;text-decoration:none;border-radius:4px}
  </style>
</head>
<body>
  <h1>404</h1>
  <p>抱歉，你访问的页面不存在～</p>
  <a href="/blog/">返回首页</a>
</body>
</html>
```

- [ ] **Step 7: 验证各页面可达**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected: 浏览器访问 `/blog/about/`、`/blog/link/`、`/blog/messageboard/`、`/blog/daily/`、`/blog/changelog/`、`/blog/404.html` 均正常显示。`Ctrl+C` 停止。

- [ ] **Step 8: 提交**

```bash
git add source/about source/link source/messageboard source/daily source/changelog source/404.html
git commit -m "feat: 新增关于/友链/留言板/日常/更新日志/404 页面"
```

---

## Task 5: 示例文章（含 Swiper 轮播）

**Files:**
- Create: `source/_posts/hello-blog.md`
- Create: `source/_posts/bioinfo-pipeline.md`
- Create: `source/_posts/linux-tips.md`
- Create: `source/_posts/data-visualization.md`
- Create: `source/_posts/paper-notes.md`
- Create: `source/_posts/dev-notes.md`

**Interfaces:**
- Consumes: Task 1 `swiper` 配置、Task 2 `cover`/分类/标签、Task 3 双栏布局。
- Produces: 6 篇覆盖多分类/标签的示例文章，其中前 3 篇进入首页轮播。

- [ ] **Step 1: 创建 `source/_posts/hello-blog.md`（置顶轮播 #1）**

```markdown
---
title: 你好，欢迎来到我的博客
date: 2024-01-01 10:00:00
updated: 2024-01-02 10:00:00
categories:
  - 博客
tags:
  - 公告
  - 随笔
cover: https://picsum.photos/seed/hello/800/450
top_img: https://picsum.photos/seed/hellotop/1920/600
description: 这是博客的第一篇文章，介绍本站的搭建与规划。
swiper_index: 3
---

欢迎来到**颜浩轩的博客**！本站基于 Hexo + Butterfly 主题搭建，记录学习与生活。

## 本站使用的技术

- 生成器：Hexo 6
- 主题：Butterfly
- 部署：GitHub Pages + GitHub Actions

## 代码示例

\`\`\`bash
hexo new "我的新文章"
hexo server   # 本地预览
hexo g        # 生成静态文件
\`\`\`

后续会持续更新，敬请期待！
```

- [ ] **Step 2: 创建 `source/_posts/bioinfo-pipeline.md`（置顶轮播 #2）**

```markdown
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

\`\`\`bash
bwa mem ref.fa read1.fq read2.fq | samtools sort -o out.bam
\`\`\`

（示例内容，后续替换为真实文章）
```

- [ ] **Step 3: 创建 `source/_posts/linux-tips.md`（置顶轮播 #3）**

```markdown
---
title: Linux 常用命令速查
date: 2024-03-10 10:00:00
categories:
  - 学习
  - 杂项
tags:
  - linux
  - 运维
cover: https://picsum.photos/seed/linux/800/450
description: 整理日常高频使用的 Linux 命令。
swiper_index: 1
---

记录日常高频使用的 Linux 命令。

## 文件与目录

\`\`\`bash
ls -lah        # 列出文件
du -sh *       # 查看目录大小
df -h          # 查看磁盘
\`\`\`

（示例内容）
```

- [ ] **Step 4: 创建 `source/_posts/data-visualization.md`**

```markdown
---
title: 数据可视化技巧
date: 2024-04-05 10:00:00
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

\`\`\`r
library(ggplot2)
ggplot(mtcars, aes(mpg, wt)) + geom_point() + theme_minimal()
\`\`\`

（示例内容）
```

- [ ] **Step 5: 创建 `source/_posts/paper-notes.md`**

```markdown
---
title: 文献阅读笔记：基因组学方向
date: 2024-05-12 10:00:00
categories:
  - 学习
  - 文献阅读
tags:
  - 基因组学
  - 文献
cover: https://picsum.photos/seed/paper/800/450
description: 记录近期阅读的基因组学文献要点。
---

记录近期阅读的文献。

## 要点

- 群体遗传变异模式
- 选择信号检测方法

（示例内容）
```

- [ ] **Step 6: 创建 `source/_posts/dev-notes.md`**

```markdown
---
title: 软件开发杂记
date: 2024-06-01 10:00:00
categories:
  - 软件开发
tags:
  - Python
  - Git
cover: https://picsum.photos/seed/dev/800/450
description: 开发过程中的一些经验与技巧。
---

记录开发中的经验。

## Git 工作流

\`\`\`bash
git checkout -b feature
git commit -m "feat: ..."
\`\`\`

（示例内容）
```

- [ ] **Step 7: 验证分类/标签/归档/轮播/双栏**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected:
- 首页：顶部 **Swiper 轮播** 显示 3 篇置顶文章（按 `swiper_index` 降序：hello-blog=3 在最前）；下方文章为**双栏卡片**。
- `/blog/categories/`：显示「博客/学习/软件开发」分类树（学习下含 生信/杂项/绘图/文献阅读）。
- `/blog/tags/`：标签云显示公告/随笔/生物信息学/linux 等。
- `/blog/archives/`：按月归档。
`Ctrl+C` 停止。

- [ ] **Step 8: 提交**

```bash
git add source/_posts
git commit -m "feat: 新增 6 篇示例文章（覆盖分类/标签/轮播）"
```

---

## Task 6: 背景人物（日/夜切换）自定义 CSS

**Files:**
- Create: `source/css/custom.css`
- Create: `source/pic/day.png`（下载自 biojuse）
- Create: `source/pic/night.png`（下载自 biojuse）
- Modify: `_config.butterfly.yml`（inject.head 引入 custom.css）

**Interfaces:**
- Consumes: Task 2 `inject` 段、Butterfly 暗黑模式（`html[data-theme="dark"]`）。
- Produces: 页面背景人物（日间伊蕾娜 / 夜间朔夜观星），随暗黑模式切换；卡片半透明露出背景。

- [ ] **Step 1: 下载 biojuse 背景人物图到本地**

Run:
```bash
cd "c:/Users/颜浩轩/OneDrive/桌面/GitHub/MDBOOK/Web"
mkdir -p source/pic
curl -sL "https://biojuse.com/pic/1121393.png" -o source/pic/day.png
curl -sL "https://biojuse.com/pic/sygx.png" -o source/pic/night.png
ls -lh source/pic/
```
Expected: 生成 `source/pic/day.png` 与 `source/pic/night.png`，文件大小 > 0。

- [ ] **Step 2: 创建 `source/css/custom.css`**

```css
/* ============================================================
 * Butterfly 主题：页面背景人物（日/夜随暗黑模式切换）
 * 复用主题自带的 #web_bg 全屏固定背景层
 * 日间：魔女之旅·伊蕾娜  夜间：崩坏3·朔夜观星
 * ============================================================ */

#web_bg {
  position: fixed;
  z-index: -999;
  width: 100%;
  height: 100%;
  background: url(/blog/pic/day.png);
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

[data-theme="dark"] #web_bg {
  background: url(/blog/pic/night.png);
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

/* 卡片半透明，露出背景人物 */
#recent-posts .recent-post-item,
#aside-content .card-widget,
#page-header {
  background: rgba(255, 255, 255, 0.9);
}
[data-theme="dark"] #recent-posts .recent-post-item,
[data-theme="dark"] #aside-content .card-widget,
[data-theme="dark"] #page-header {
  background: rgba(0, 0, 0, 0.8);
}
#page-header {
  background: transparent !important;
}

/* 移动端不强制背景，避免遮挡 */
@media screen and (max-width: 768px) {
  #web_bg { background-size: cover; }
}
```

> 注：图片路径用 `/blog/pic/...`（子路径 `root: /blog/`）。若改用根仓库部署，去掉 `/blog` 前缀。

- [ ] **Step 3: 在 `_config.butterfly.yml` 的 `inject.head` 引入 custom.css**

把 Task 3 的 `inject` 段改为：

```yaml
inject:
  head:
    - <link rel="stylesheet" href="/blog/css/custom.css">
  bottom:
    - <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/Zfour/Butterfly-double-row-display@1.00/cardlistpost.min.css">
```

- [ ] **Step 4: 验证背景人物与暗黑切换**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected:
- 首页能看到日间背景人物（伊蕾娜），卡片半透明。
- 点击右下角暗黑切换按钮 → 背景切换为夜间人物（朔夜观星），卡片变深色半透明。
`Ctrl+C` 停止。

- [ ] **Step 5: 提交**

```bash
git add source/css/custom.css source/pic _config.butterfly.yml
git commit -m "feat: 添加日/夜背景人物与半透明卡片样式"
```

---

## Task 7: Giscus 评论 + 验证访问统计

**Files:**
- Modify: `_config.butterfly.yml`（追加 comments/giscus 段；busuanzi 已在 Task 2）

**Interfaces:**
- Consumes: Task 2 `busuanzi`（已开启）。
- Produces: 文章页/留言板评论区（Giscus 接入代码就绪，`repo_id`/`category_id` 待用户在 giscus.app 生成后填入即生效）。

- [ ] **Step 1: 在 `_config.butterfly.yml` 末尾追加 comments 与 giscus 段**

```yaml
# ---------- 评论 ----------
comments:
  use: Giscus
  text: true
  lazyload: false
  count: false
  card_post_count: false

# ---------- Giscus ----------
# repo_id 与 category_id 需在 https://giscus.app 生成后填入
# 生成步骤：仓库 public + 开启 Discussions + 安装 Giscus App 后，
# 打开 giscus.app 输入 RosyStorm/blog，复制 data-repo-id / data-category-id
giscus:
  repo: RosyStorm/blog
  repo_id: R_kgDOTEt_hA
  category_id: DIC_kwDOTEt_hM4CgAAAA
  light_theme: light
  dark_theme: dark
  js:
  option:
    data-category: Announcements
    data-mapping: pathname
    data-strict: '0'
    data-reactions-enabled: '1'
    data-emit-metadata: '0'
    data-input-position: bottom
    data-lang: zh-CN
    data-loading: lazy
```

> 注：`repo_id` 暂填仓库创建时返回的 node_id（`R_kgDOTEt_hA`，来自创建仓库 API）。`category_id` 为占位，需用户在 giscus.app 选定 Discussion 分类后用真实值替换。这两步在 Task 8 上线后、仓库就绪时由用户完成（giscus.app 会实时生成）。

- [ ] **Step 2: 验证评论区与统计占位**

Run: `npx hexo clean && npx hexo g && npx hexo s`
Expected:
- 文章页底部出现评论区容器（Giscus，因 id 未生效可能显示「加载中」或空白，属正常）。
- 侧边栏「网站资讯」显示文章数目、总字数；线上访问量统计（busuanzi）需部署后才显示数字。
`Ctrl+C` 停止。

- [ ] **Step 3: 提交**

```bash
git add _config.butterfly.yml
git commit -m "feat: 接入 Giscus 评论与 busuanzi 访问统计"
```

---

## Task 8: GitHub Actions 自动部署 + 上线

**Files:**
- Create: `.github/workflows/deploy.yml`
- Then: GitHub 仓库 Settings 配置 + push 触发

**Interfaces:**
- Consumes: Task 1-7 的全部源码。
- Produces: 推送 `main` 自动构建并发布到 `gh-pages`，GitHub Pages 可访问。

- [ ] **Step 1: 创建 `.github/workflows/deploy.yml`**

```yaml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Build
        run: |
          npx hexo clean
          npx hexo g

      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: gh-pages
          user_name: 'github-actions[bot]'
          user_email: 'github-actions[bot]@users.noreply.github.com'
          commit_message: ${{ github.event.head_commit.message }}
```

- [ ] **Step 2: 提交 workflow**

```bash
git add .github/workflows/deploy.yml
git commit -m "ci: 新增 GitHub Actions 自动部署到 GitHub Pages"
```

- [ ] **Step 3: 推送到 GitHub 触发部署**

Run: `git push origin main`
Expected: 推送成功；GitHub 仓库 Actions 页出现一次 `Deploy Hexo to GitHub Pages` 运行。

- [ ] **Step 4: 配置 GitHub Pages（用 MCP）**

调用 `mcp__github__create_or_update_file` 或在网页设置：仓库 Settings → Pages → Source = `Deploy from a branch` → 分支 `gh-pages` / `/ (root)` → Save。
（首次需等 Actions 跑完生成 `gh-pages` 分支后再设置。）

- [ ] **Step 5: 验证线上访问**

等待 Actions 运行完成（绿色 ✓）+ Pages 生效（1-2 分钟）。
Run: `curl -sI https://rosystorm.github.io/blog/ | head -3`
Expected: HTTP/1.1 200，首页可访问。浏览器打开能看到完整博客。

> Giscus 收尾：仓库就绪后，打开 https://giscus.app ，输入 `RosyStorm/blog`，选择 Discussion 分类（推荐 Announcements），复制 `data-repo-id` 与 `data-category-id`，更新 `_config.butterfly.yml` 的 `giscus.repo_id` / `category_id`，push 即生效。

---

## Task 9: 验收清单

**Files:** 无新建，逐项验证 spec §13 的 12 条标准。

- [ ] **Step 1: 跑完整本地验证**

Run: `npx hexo clean && npx hexo g && npx hexo s`
逐项核对：
1. 全部页面无报错 ✓
2. 首页 Swiper 轮播 + 双栏卡片 + 完整侧边栏 ✓
3. 暗黑模式可切换并记忆，背景人物随之切换 ✓
4. 本地搜索能检索到示例文章 ✓
5. 分类/标签/归档正确聚合 ✓
6. 文章页目录、代码高亮、灯箱、上下篇、版权声明 ✓
7. busuanzi 统计（线上）✓
8. Giscus 评论区（id 填入后）✓
9. GitHub Actions 构建成功，Pages 可访问 ✓
10. 移动端响应式正常 ✓
11. 404 页面正常 ✓
12. 全站无 biojuse 原文 ✓

- [ ] **Step 2: 修复发现的问题（若有）并提交**

```bash
git add -A
git commit -m "fix: 验收阶段修复"
git push origin main
```

- [ ] **Step 3: 最终确认**

Run: `git log --oneline`
Expected: 看到完整的提交历史；线上 https://rosystorm.github.io/blog/ 可正常访问。

---

## Self-Review（计划自检）

**1. Spec 覆盖：**
- §3 架构 → Task 1/8 ✓
- §4 页面路由 → Task 4（about/link/messageboard/daily/changelog/404）+ Hexo 内置 archives/tags/categories ✓
- §5 组件（导航/搜索/双栏/暗黑/侧边栏/背景/字数/统计/评论/灯箱/目录）→ Task 2/3/6/7 ✓
- §6 主题配置 → Task 2 ✓
- §7 第三方服务 → Task 1(search)/2(busuanzi)/3(swiper/double-row)/7(giscus) ✓
- §8 内容策略（6 篇示例，不搬运原文）→ Task 5 ✓
- §9 目录结构 → 全任务 ✓
- §10 部署 → Task 8 ✓
- §11 待提供字段 → 已回填（颜浩轩/清华/邮箱/仓库名）；Giscus id 待线上生成（Task 8 收尾）✓
- §12 不做范围 → 未涉及后台/登录/数据库 ✓
- §13 验收 → Task 9 ✓

**2. 占位扫描：** Giscus `category_id` 为占位，但已说明该值只能用户在 giscus.app 生成（客观依赖外部数据），并在 Task 8 给出获取步骤——非计划缺陷。其余代码均完整。

**3. 类型/字段一致性：** `swiper_index`（Task 5 文章）↔ Task 1 swiper 配置读取一致；`inject` 段在 Task 2→3→6 渐进追加，最终态一致；背景图路径 `/blog/pic/` 与 `root: /blog/`（Task 1）一致；导航路径（Task 2）与页面（Task 4）一致。

**4. 已知风险：** Task 4 友链 `{% linklist %}` 若主题版本不识别需改用 `_config.butterfly.yml` 的 `flink_list` 配置（已加注）；Task 1 依赖版本以写时最新稳定版为准，`npm install` 时若有更高版本可能浮动，但 `^` 范围内兼容。
