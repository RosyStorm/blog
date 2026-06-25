# 设计文档：仿 biojuse.com 的 Hexo + Butterfly 全功能博客

> 日期：2026-06-25
> 参考站点：https://biojuse.com/（Hexo 5.4.2 + Butterfly 主题）
> 部署目标：GitHub Pages（通过 GitHub Actions 自动构建）

---

## 1. 项目目标

仿照 biojuse.com 的**布局与功能**，搭建一个属于用户自己的、可长期维护的静态博客，通过 GitHub Pages 部署上线。首版即完成 biojuse 绝大部分功能与视觉的**高保真复刻**。

**版权底线**：biojuse 上的文章、文案为原作者 Juse 的创作，本项目**绝不搬运原文**。博客内容一律使用占位/示例文本与图片，由用户后续替换为自己的内容。

## 2. 已确认决策

| 维度 | 决策 |
|---|---|
| 生成器 | Hexo 6 |
| 主题 | Butterfly（最新稳定版，与 biojuse 同款） |
| 博客身份 | 用户自己的博客（标题/作者先用占位，见 §11） |
| 交付范围 | 全功能一次性高保真复刻 |
| 评论系统 | Giscus（基于 GitHub Discussions，零后端） |
| 背景人物 | 照搬 biojuse 同款：夜间崩坏3·朔夜观星，日间魔女之旅·伊蕾娜 |
| 访问统计 | 不蒜子 busuanzi |
| 搜索 | 本地搜索（hexo-generator-searchdb，无后端） |
| 部署 | GitHub Actions → GitHub Pages |

## 3. 整体架构

- **本地开发**：`hexo s` 启动本地预览（http://localhost:4000），`hexo clean && hexo g` 生成静态文件到 `public/`。
- **内容源**：Markdown 文章存放于 `source/_posts/`，分类/标签由 Front-matter 声明。
- **构建发布**：推送到 `main` 分支 → GitHub Actions 自动执行 `npm ci && hexo g` → 将 `public/` 推送到 `gh-pages` 分支 → GitHub Pages 提供访问。
- **CDN**：jsDelivr，与原站一致。

```
[Markdown 文章] → [Hexo + Butterfly 构建] → [public/ 静态文件]
                                              ↓
                          git push main → GitHub Actions → gh-pages → GitHub Pages
```

## 4. 页面路由（1:1 对齐 biojuse）

| 路径 | 页面 | 说明 |
|---|---|---|
| `/` | 首页 | Swiper 置顶轮播 + 双栏文章卡片 + 侧边栏 |
| `/archives/` | 归档 | 按时间线列出全部文章 |
| `/tags/` | 标签 | 标签云 |
| `/tags/:name/` | 标签文章 | 该标签下所有文章 |
| `/categories/` | 分类 | 分类树（支持多级） |
| `/categories/:name/` | 分类文章 | 该分类下所有文章 |
| `/messageboard/` | 留言板 | 评论墙（使用 Giscus） |
| `/link/` | 友链 | 友人链接卡片 |
| `/about/` | 关于 | 博主介绍、建站说明、联系方式 |
| `/post/:title/` 或 `/:title/` | 文章详情 | 目录/代码高亮/灯箱/评论等 |
| `/404.html` | 404 | 找不到页面（配 404 图） |
| `/atom.xml` | RSS | 站点订阅 |

导航菜单结构（仿 biojuse）：

```
首页
文章 ▾
  归档 / 标签 / 分类
留言板
友链
我自己
关于
杂项 ▾
  琐碎的日常 / 更新日志
```

> 「我自己」「杂项」下的页面用占位内容先行搭建，用户后续填充。

## 5. 布局与功能组件清单（全量复刻）

### 5.1 顶部
- **导航栏**：固定在顶部，滚动时收缩/毛玻璃效果；含站点标题、菜单、**搜索按钮**、**暗黑模式切换**按钮。
- **本地搜索**：点击搜索按钮弹出搜索框，实时检索文章标题与正文（前端实现，无后端）。

### 5.2 首页
- **Swiper 顶图轮播**：`hexo-butterfly-swiper`，展示置顶（top_img）文章，自动播放 + 左右切换。
- **双栏文章卡片**：`Zfour/Butterfly-double-row-display`，与 biojuse 完全一致的双列卡片瀑布流，含封面图、标题、日期、摘要。

### 5.3 侧边栏（右侧）
- 头像 + 站点名
- 文章 / 标签 / 分类 三项计数
- 公告板（占位文案，仿 biojuse「人生是旷野，不是轨道」风格）
- 分类树（多级展开）
- 标签云
- 归档（按月聚合）
- 网站资讯：文章数目、本站总字数、访客数、访问量、最后更新时间
- 想说的话（占位金句）
- 友人链接（小卡片）

### 5.4 文章详情页
- 文章标题、发布/更新时间、分类、标签
- **自动生成目录（TOC）**，滚动跟随高亮
- **阅读进度条**
- **代码块高亮 + 一键复制按钮**
- **Fancybox 图片灯箱**（点击放大）
- 文章末尾：版权声明、上下篇导航、相关推荐
- 评论区（Giscus）

### 5.5 全局
- **暗黑/夜间模式**：一键切换，记忆用户偏好（localStorage）
- **背景人物**：日间魔女之旅·伊蕾娜、夜间崩坏3·朔夜观星（固定在页面侧边，随模式切换）
- **返回顶部**按钮
- **页脚**：版权年份、框架/主题/部署/加速声明（仿 biojuse「框架 Hexo | 主题 Butterfly | 部署 GitPage | 加速 Cloudflare」格式，用占位）

## 6. 主题配置要点（_config.butterfly.yml）

关键配置项（实现时填入，此处记录设计意图）：

- `nav` 导航菜单：见 §4
- `menu` 同上
- `color`：主色 `#49b1f0`（Butterfly 默认蓝，与 biojuse 一致）
- `darkmode.enable: true`，`display_mode: switch`
- `aside` 侧边栏：开启上述全部 widget，移动端折叠
- `cover` 文章封面：默认占位图
- `top_img`：首页大图占位
- `background` 背景人物：日/夜分别配置（`#web_bg` + 自定义 CSS 定位人物图层）
- `search`：本地搜索 `local_search enable: true`
- `comments`：`giscus`
- `notice` 公告：占位文案
- `wordcount` 全站字数统计：开启（依赖 hexo-wordcount）
- `busuanzi` 访问统计：开启

## 7. 第三方服务与插件

| 用途 | 方案 | 备注 |
|---|---|---|
| 搜索 | hexo-generator-searchdb | 本地 JSON 索引，前端检索 |
| 评论 | Giscus | 需仓库 public + 开启 Discussions + 装 Giscus App |
| 访问统计 | 不蒜子 busuanzi | 引入 JS 即可，零配置 |
| 字数统计 | hexo-wordcount + wordcount 配置 | 站点/文章字数 |
| 双栏卡片 | Zfour/Butterfly-double-row-display | CSS 注入，与原站同款 |
| 顶图轮播 | hexo-butterfly-swiper | 置顶文章轮播 |
| 图标 | Font Awesome（CDN） | 与原站一致 |
| 图片灯箱 | Fancybox（CDN） | 与原站一致 |
| 代码高亮 | Prism / highlight.js（Butterfly 内置） | 一键复制 |

## 8. 内容策略

- **示例文章**：编写 **6-8 篇** Markdown 示例文章，分布在不同分类（如「学习/生信」「学习/杂项」「软件开发」）与多个标签下，便于直观验证分类树、标签云、归档、双栏卡片、轮播等效果。
- **Front-matter** 规范：title / date / categories / tags / cover / top_img / description。
- **占位资源**：头像、封面、背景人物、404 图等先用占位图（biojuse 背景人物为公开动漫图，可直接引用同款 URL）。
- **绝不搬运** biojuse 原文的任何文字段落。

## 9. 目录结构

```
Web/
├── _config.yml                 # 站点配置（Hexo）
├── _config.butterfly.yml       # 主题配置（Butterfly，复刻 biojuse）
├── package.json                # 依赖与脚本
├── .gitignore
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions 自动部署
├── source/
│   ├── _posts/                 # 示例文章（6-8 篇）
│   │   ├── hello-world.md
│   │   └── ...
│   ├── about/
│   │   └── index.md
│   ├── link/
│   │   └── index.md
│   ├── messageboard/
│   │   └── index.md
│   ├── daily/                  # 「琐碎的日常」占位
│   ├── changelog/              # 「更新日志」占位
│   ├── imgs/                   # 占位图资源
│   └── 404.html
├── themes/
│   └── butterfly/              # npm 安装（package.json 声明）
└── docs/
    └── superpowers/specs/      # 设计文档（不参与 Hexo 构建）
```

> `docs/` 位于根目录，Hexo 默认只构建 `source/` 下的内容，根目录文件不会进入 `public/`，故设计文档不会被部署到线上。

## 10. 部署方案（GitHub Actions）

- 仓库分支：`main`（源码）→ Actions 构建 → `gh-pages`（静态产物）。
- 仓库设置：Settings → Pages → Source 设为 `gh-pages` 分支 / root。
- Workflow 关键步骤：
  1. `actions/checkout@v4` 拉取 `main`
  2. `actions/setup-node@v4` 配置 Node.js（ LTS）
  3. `npm ci` 安装依赖（含 hexo-cli、butterfly、各插件）
  4. `hexo clean && hexo g` 生成
  5. 用 `peaceiris/actions-gh-pages` 将 `public/` 推到 `gh-pages`
- 触发：`push` 到 `main`，或手动 `workflow_dispatch`。

> 若使用自定义域名，在 `source/` 下放 `CNAME`；本项目默认使用 `https://<用户名>.github.io/<仓库名>/`，需在 `_config.yml` 正确配置 `url` 与 `root`。

## 11. 待用户提供字段清单（占位，需替换）

以下字段当前用占位值，实现后请用户替换为真实内容（在 `_config.yml` / `_config.butterfly.yml` 中）：

| 字段 | 值 | 状态 |
|---|---|---|
| GitHub 仓库名 | `blog` | ✅ 已确认 |
| 站点标题 | 颜浩轩的博客 | ✅ 已确认（实现时可改） |
| 站点副标题 | 记录与分享 | 占位，可改 |
| 站点描述 | 颜浩轩的个人技术博客 | 占位，SEO 用 |
| 作者昵称 | 颜浩轩 | ✅ 已确认 |
| 所属院校 | 清华大学 | ✅ 已确认（关于页） |
| 关键词 | 博客, 技术, 清华 | 占位，SEO |
| 头像 URL | 占位图 | 待提供 |
| 联系邮箱 | 1741426978@qq.com | ✅ 已确认 |
| Giscus 配置 | repo / repoId / category / categoryId | 仓库就绪后填 |
| 公告文案 | 占位 | 待提供 |
| 想说的话 | 占位金句 | 待提供 |

## 12. 不做范围（YAGNI）

为避免范围蔓延，以下**不在首版范围**，后续按需迭代：

- 后台管理系统（静态博客无需）
- 用户登录/账号体系
- 数据库/动态接口
- 自定义搜索引擎（Algolia 等，首版用本地搜索）
- 国际化多语言（首版仅中文）
- 原站「萌ICP备案」「Cloudflare 加速」等需第三方账号/资质的标识（用占位或不显示）

## 13. 验收标准

1. ✅ 本地 `hexo s` 可正常预览全部页面，无报错。
2. ✅ 首页：Swiper 轮播 + 双栏卡片 + 完整侧边栏均正常显示。
3. ✅ 暗黑模式可切换并记忆，背景人物随之切换。
4. ✅ 本地搜索可用，能检索到示例文章。
5. ✅ 分类/标签/归档页正确聚合示例文章。
6. ✅ 文章页：目录、代码高亮、图片灯箱、上下篇、版权声明正常。
7. ✅ 不蒜子访问统计显示（线上）。
8. ✅ Giscus 评论区显示（配置完成后）。
9. ✅ 推送 `main` 后 GitHub Actions 自动构建成功，`gh-pages` 分支更新，GitHub Pages 可访问。
10. ✅ 移动端响应式正常（导航折叠、侧边栏适配）。
11. ✅ 404 页面正常。
12. ✅ 全站不含任何 biojuse 原文内容。

---

## 附：技术风险与注意事项

- **Giscus 需要仓库已创建并 public**：首版即写入 Giscus 接入代码与配置占位；待用户在 GitHub 建好 public 仓库并开启 Discussions、在 https://giscus.app 生成配置后，将 `repo` / `repoId` / `category` / `categoryId` 填入 `_config.butterfly.yml` 即可生效，无需改动代码结构。
- **图片资源**：biojuse 背景人物为外部 URL，若链接失效需准备本地备份。
- **Hexo 与 Node 版本**：GitHub Actions 中使用 Node 18/20 LTS，确保与 Hexo 6 兼容。
- **子路径部署**：若 Pages 地址含仓库名（非 `<用户名>.github.io` 根仓库），`_config.yml` 的 `url`/`root` 必须正确，否则静态资源 404。
