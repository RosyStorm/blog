# 颜浩轩的博客

基于 **Hexo + Butterfly 主题** 搭建的个人静态博客，仿 [biojuse.com](https://biojuse.com/) 的布局与功能，通过 GitHub Actions 自动部署到 GitHub Pages。

- 🌐 **线上地址**：https://rosystorm.github.io/blog/
- 📦 **仓库**：https://github.com/RosyStorm/blog
- ✍️ **作者**：颜浩轩（清华大学）

> 本文档面向零基础用户，按顺序读完即可独立写文章、改外观、发布上线。

---

## 目录

1. [目录结构（源文件在哪）](#1-目录结构源文件在哪)
2. [本地预览环境准备](#2-本地预览环境准备)
3. [写文章（最常用）](#3-写文章最常用)
4. [文章分类与标签](#4-文章分类与标签)
5. [在文章里插入图片](#5-在文章里插入图片)
6. [修改网页布局与外观](#6-修改网页布局与外观)
7. [发布上线（一键部署）](#7-发布上线一键部署)
8. [常见配置速查表](#8-常见配置速查表)
9. [常见问题与踩坑记录](#9-常见问题与踩坑记录)
10. [技术栈说明](#10-技术栈说明)

---

## 1. 目录结构（源文件在哪）

```
Web/                              ← 项目根目录（你在用的就是这个）
├── _config.yml                   ★ 站点总配置（标题/作者/网址/部署）
├── _config.butterfly.yml         ★ 主题配置（导航/侧边栏/配色/背景/评论）← 改外观主要改这里
├── package.json                  依赖清单（不用手动改）
├── .gitignore                    Git 忽略规则
│
├── source/                       ★ 所有"内容"都在这里
│   ├── _posts/                   ★★★ 你的文章都在这里（Markdown .md 文件）
│   │   ├── hello-blog.md
│   │   ├── nuclear-intro.md
│   │   └── ...
│   ├── about/index.md            关于页
│   ├── link/index.md             友链页
│   ├── messageboard/index.md     留言板
│   ├── daily/index.md            琐碎日常
│   ├── changelog/index.md        更新日志
│   ├── categories/index.md       分类总览页
│   ├── tags/index.md             标签总览页
│   ├── 404.html                  404 页面
│   ├── images/                   ★ 放文章图片的地方（见第 5 节，需自己建）
│   ├── css/custom.css            ★ 自定义样式（背景人物等）
│   └── pic/                      背景人物图片（day.png / night.png）
│
├── .github/workflows/deploy.yml  自动部署脚本（push 后自动构建上线）
└── docs/                         设计文档（不用管）
```

**一句话记忆**：
- 写文章 → `source/_posts/`
- 改外观 → `_config.butterfly.yml`
- 改站点信息（名字、网址）→ `_config.yml`

---

## 2. 本地预览环境准备

> 第一次使用需要装 Node.js。已装过可跳过。

### 2.1 安装 Node.js
到 https://nodejs.org 下载 **LTS 版**（20.x 或更高），一路下一步安装。

### 2.2 安装博客依赖（只需第一次）
打开终端（在 `Web/` 目录下），执行：
```bash
npm install
```
等待几分钟，装好后会生成 `node_modules/` 文件夹。

### 2.3 启动本地预览
```bash
npx hexo s
```
看到 `Hexo is running at http://localhost:4000` 后，浏览器打开：
👉 **http://localhost:4000/blog/**

修改任何文件后，**刷新浏览器**即可看到变化（文章/配置改动可能需 `Ctrl+C` 停止后重新 `npx hexo s`）。

---

## 3. 写文章（最常用）

### 方式 A：用命令创建（推荐新手）
在 `Web/` 目录执行：
```bash
npx hexo new "我的第一篇文章"
```
会在 `source/_posts/我的第一篇文章.md` 生成一个文件，自动带上基础 front-matter。

### 方式 B：手动新建
直接在 `source/_posts/` 里新建一个 `.md` 文件，比如 `my-post.md`。

### 文章的标准结构
用记事本/VS Code 打开 `.md` 文件，写成下面这样：

```markdown
---
title: 我的第一篇文章          # 文章标题（必填）
date: 2026-06-25 10:00:00       # 发布日期（必填）
categories:                     # 分类（影响分类页，见第 4 节）
  - 学习
  - 核工程
tags:                           # 标签（影响标签云）
  - 核工程学
  - 教程
cover: https://picsum.photos/seed/my/800/450   # 文章封面图（列表里显示的小图）
top_img: https://picsum.photos/seed/mytop/1920/600  # 文章顶部大图
description: 这是一句话摘要，会显示在首页卡片和轮播里。
swiper_index: 1                 # 可选：填了就会上首页顶部轮播（数字越大越靠前）
---

这里是正文，用 **Markdown** 语法写。

## 二级标题

普通段落。**加粗**、*斜体*、`行内代码`。

- 列表项 1
- 列表项 2

代码块：

\`\`\`bash
echo "hello"
\`\`\`

插入图片见第 5 节。
```

> ⚠️ 顶部 `---` 之间的部分叫 **front-matter**（前置配置），下面的才是正文。`---` 不能省。

### 让文章上首页轮播
在 front-matter 加 `swiper_index: 数字`（如 `swiper_index: 3`），数字越大排越前面。不填则不进轮播。

---

## 4. 文章分类与标签

### 分类（categories）
在 front-matter 写：
```yaml
categories:
  - 学习        # 一级分类
  - 核工程        # 二级分类（缩进表示层级）
```
多个层级就继续缩进。这样文章会进入「学习 > 核工程」分类树。

### 标签（tags）
```yaml
tags:
  - 核工程学
  - R
  - ggplot2
```
标签是扁平的，没有层级。

### 关于"在 _posts 下建子文件夹"
**可以建子文件夹来物理整理**，比如：
```
source/_posts/
├── 核工程/
│   ├── article-1.md
│   └── article-2.md
├── 杂项/
│   └── note.md
```
Hexo 会扫描所有子目录里的 `.md`，都能识别。

但是注意：
- **文件夹名 ≠ 分类**。即使在 `核工程/` 文件夹里，文章的分类仍由 front-matter 的 `categories` 决定。
- **建议两者结合**：用子文件夹归类整理文件，同时每篇文章 front-matter 里写好 `categories`。

---

## 5. 在文章里插入图片

### 方式 A：全局图片文件夹（最简单，推荐新手）

**第 1 步**：在 `source/` 下建一个 `images` 文件夹（如果没有的话），把图片放进去：
```
source/images/my-photo.jpg
```

**第 2 步**：在文章正文里这样引用（注意是 `/blog/images/...`，因为有子路径）：
```markdown
![图片描述](/blog/images/my-photo.jpg)
```

保存后本地预览或部署，图片就会显示。

### 方式 B：每篇文章独立图片文件夹（进阶，更整洁）

如果你希望每篇文章的图片和文章放一起，可以开启 `post_asset_folder`：

**第 1 步**：打开 `_config.yml`，找到这行，改成 `true`：
```yaml
post_asset_folder: true
```

**第 2 步**：以后用 `npx hexo new "标题"` 创建文章时，会自动生成一个同名文件夹：
```
source/_posts/
├── 我的第一篇文章.md
└── 我的第一篇文章/        ← 同名文件夹
    └── photo.jpg          ← 图片放这里
```

**第 3 步**：文章里用 Hexo 的 asset 标签引用（最可靠）：
```markdown
{% asset_img photo.jpg 图片描述 %}
```

> 两种方式选一种即可。新手建议先用**方式 A**。

---

## 6. 修改网页布局与外观

> 改完保存，本地 `npx hexo s` 预览，满意后 `git push` 上线（见第 7 节）。

所有外观相关配置都在 **`_config.butterfly.yml`**。下面是最常改的几项。

### 6.1 改导航菜单
找到 `menu:` 段：
```yaml
menu:
  首页: / || fas fa-home
  文章||fas fa-list:
    归档: /archives/ || fas fa-archive
    标签: /tags/ || fas fa-tags
    分类: /categories/ || fas fa-folder-open
  留言板: /messageboard/ || fas fa-comment-dots
  关于: /about/ || fas fa-info-circle
```
- 格式：`显示名: 路径 || 图标`
- 图标用 [Font Awesome](https://fontawesome.com/icons) 的名字（如 `fas fa-home`）
- 删除某行 = 删除该菜单项；缩进的项是子菜单

### 6.2 改侧边栏
找到 `aside:` 段，里面有 `card_announcement`（公告）、`card_categories`（分类）、`card_tags`（标签云）、`card_archives`（归档）、`card_webinfo`（网站资讯）等，每个都有 `enable: true/false` 开关。

改公告文字：
```yaml
card_announcement:
  enable: true
  content: 这里写你的公告内容。
```

### 6.3 改主题配色
找到 `theme_color:` 段，`main` 是主色调（链接、按钮）：
```yaml
theme_color:
  enable: true
  main: "#49B1F5"      # 改成你喜欢的颜色，例如红 "#FF6B6B"
```
颜色必须是 `#RRGGBB` 格式，**务必加双引号**。

### 6.4 换背景人物
背景图文件在 `source/pic/`：
- `day.png` = 日间背景（现在是魔女之旅·伊蕾娜）
- `night.png` = 夜间背景（崩坏3·朔夜观星）

**换图**：把你想要的图片命名成 `day.png` / `night.png`，覆盖 `source/pic/` 下同名文件即可（建议用 PNG/JPG，单张 < 1MB 加载更快）。

背景样式规则在 `source/css/custom.css`。

### 6.5 改头像 / 站点标题
- **头像**：`_config.butterfly.yml` 的 `avatar.img`（现在是你的 GitHub 头像 URL，可换成图片路径如 `/blog/images/avatar.jpg`）
- **站点标题/副标题/作者**：`_config.yml` 顶部的 `title` / `subtitle` / `author`

### 6.6 关掉某个功能
- 关评论：`comments.use:` 改空 或 文章 front-matter 加 `comments: false`
- 关访问统计：`busuanzi.enable: false`
- 关暗黑模式：`darkmode.enable: false`
- 关轮播：`_config.yml` 的 `swiper.enable: false`

---

## 7. 发布上线（一键部署）

博客已配好自动部署：**只要把改动 push 到 GitHub，1~2 分钟后线上自动更新。**

### 步骤
```bash
# 1. 在 Web/ 目录，保存好你改的文件
# 2. 添加所有改动
git add .

# 3. 提交（写一句话说明改了啥）
git commit -m "feat: 新增一篇核工程文章"

# 4. 推送到 GitHub（触发自动部署）
git push
```

push 后：
1. GitHub 自动运行 Actions（构建静态网站）
2. 1~2 分钟后，访问 https://rosystorm.github.io/blog/ 看到更新
3. 没看到变化？按 **Ctrl + Shift + R** 强制刷新清缓存

> 在 GitHub 仓库的 **Actions** 页面可以看到部署进度（绿色 ✓ = 成功，红色 ✗ = 失败）。

---

## 8. 常见配置速查表

| 我想…… | 改哪个文件 | 改哪里 |
|---|---|---|
| 改站点名/作者 | `_config.yml` | `title` / `author` |
| 改导航菜单 | `_config.butterfly.yml` | `menu:` |
| 改公告文字 | `_config.butterfly.yml` | `aside.card_announcement.content` |
| 改主色调 | `_config.butterfly.yml` | `theme_color.main` |
| 换背景人物 | `source/pic/` | 替换 `day.png` / `night.png` |
| 换头像 | `_config.butterfly.yml` | `avatar.img` |
| 关评论 | `_config.butterfly.yml` | `comments.use:` 留空 |
| 写新文章 | `source/_posts/` | 新建 `.md` |
| 文章配图 | `source/images/` | 放图，文章用 `![](/blog/images/x.png)` |

---

## 9. 常见问题与踩坑记录

**Q：改了东西 push 了，线上没变化？**
A：① 确认 Actions 是绿色 ✓（红色说明构建失败）；② 浏览器 `Ctrl + Shift + R` 强制刷新清缓存。

**Q：`git push` 报 "Failed to connect to github.com port 443"？**
A：国内 github.com 间歇性被阻断。多试几次，或开代理/VPN 后再 push。push 不上去时不要反复重试，等网络好再推。

**Q：本地预览 `npx hexo s` 报错？**
A：先 `npx hexo clean` 清缓存，再 `npx hexo s`。如果还报错，检查 `_config.yml` / `_config.butterfly.yml` 的 YAML 缩进（必须用空格，不能用 Tab）。

**Q：文章没出现在首页/分类里？**
A：检查文章 front-matter 的 `title` 和 `date` 是否填了，`---` 是否成对。

**Q：图片显示不出来？**
A：① 确认图片确实在 `source/images/` 下；② 路径写成 `/blog/images/xxx.png`（带 `/blog` 前缀）；③ 图片文件名别用中文或空格。

**Q：背景看不见？**
A：背景规则在 `source/css/custom.css`，`#web_bg` 的 `background` 必须带 `!important`（已配好），别误删。

**Q：评论（Giscus）不显示？**
A：评论需要用 GitHub 账号登录。确认 `_config.butterfly.yml` 的 `giscus.repo_id` 和 `category_id` 是真实值（已配好）。

---

## 10. 技术栈说明

| 组件 | 作用 |
|---|---|
| [Hexo 6](https://hexo.io/) | 静态博客生成器（把 Markdown 转成网页） |
| [Butterfly 主题](https://butterfly.js.org/) | 博客外观主题 |
| hexo-generator-* | 生成首页/归档/分类/标签/RSS |
| hexo-butterfly-swiper | 首页置顶轮播 |
| Zfour/Butterfly-double-row-display | 首页双栏卡片 |
| Giscus | 评论系统（基于 GitHub Discussions） |
| busuanzi | 访问统计 |
| GitHub Actions + Pages | 自动构建与托管 |

---

## 维护备忘

- **本地 git 与远程同步**：若曾因网络问题用其他方式提交，执行 `git pull origin main --ff-only` 同步。
- **更新依赖**：偶尔执行 `npm update` 保持插件最新（可选）。
- **备份**：所有源码都在 GitHub 仓库，本身就是备份。

---

> 有问题随时记录到「更新日志」页面（`source/changelog/index.md`），或提 GitHub Issue。
> 祝写作愉快！✨
