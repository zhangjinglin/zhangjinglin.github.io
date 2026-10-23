# 张景林的博客 —— 项目备忘

> 站点：https://zhangjinglin.github.io/
> 仓库：git@github.com:zhangjinglin/zhangjinglin.github.io.git
> 2026-10-23 从 Hexo + NexT.Muse 改造为 Hugo + FixIt

## 一句话架构

```
main 分支   = Hugo 源码（写文章、改配置都在这里）
master 分支 = 构建产物（GitHub Actions 自动写入，**不要手改**）
```

推送 `main` 后，Actions 自动构建并强推到 `master`，GitHub Pages 从 `master` 发布。
**部署成功后有 1~2 分钟的 CDN 延迟**，别急着判断失败。

## 发新文章（日常唯一要做的）

```bash
cd ~/blog/zhangjinglin.github.io

# 新建文件 content/posts/YYYY-MM-slug.md，格式：
# ---
# title: "标题"
# date: 2026-10-23T15:00:00+08:00
# url: "/2026/10/slug/"
# draft: false
# categories: ["随笔"]
# tags: ["标签1", "标签2"]
# ---

git add -A && git commit -m "新文章：xxx" && git push origin main
```

⚠️ **`date` 不能写未来时间**，Hugo 默认不构建未来日期的文章。

## 文章封面图（2026-10-23 做的，两条覆盖模板）

FixIt 原生**不支持**“标题压在封面上”——它的 `featured_image` 只是正文里的一张插图。
现在用两个项目级模板覆盖补上了，**日常只管写 front matter，不用碰代码**。

### 给文章加封面 = 三步

1. 图片放到 **`assets/images/`**（⚠️ 不是 `static/`，放错就没有 webp/srcset 优化）
2. front matter 加两行（**路径不带前导斜杠**，`resources.Get` 才认）：

```yaml
featured_image: "images/xxx.jpg"
featured_image_preview: "images/xxx.jpg"
```

3. 完事。效果：
   - 详情页 → 封面打头、标题/副标题白色压在左下
   - 首页卡片 → `fill 800x336` 裁切缩略图 + 标题压在图上（`home.posts.image_preview` 已开）
   - 自动出 srcset：详情页 800/1200/1600/2000w，卡片 800/1200/1600w，**webp 格式**

### 开关

front matter 写 `cover_overlay: false` → 退回主题原版版式（图在上、标题在下）。
不写就是默认的压图模式。详情页和首页卡片同时生效。

### 涉及的三个文件（升级主题时要同步！）

- `layouts/posts/single.html` — 覆盖主题同名文件，封面提到最前 + 标题入容器
- `layouts/summary.html` — 覆盖首页卡片，标题塞进 `.featured-image-preview`
- `assets/scss/custom.scss` — 浮层定位、渐变遮罩、字号断点

主题里这两个模板改了结构 → 这边要重新复制再改一遍，否则新功能不显示。
文件头都写了警告注释。

⚠️ **主题默认 `params.image.optimise = false`**，不改全局配置；优化是在这两个模板的
图片调用里用 `"Optimise" true` 单独开的。以后新增别的图片调用点，记得同样加。

## 本地预览

```bash
cd ~/blog/zhangjinglin.github.io
hugo server --bind 0.0.0.0 --port 1313 --baseURL http://192.168.1.151:1313/
# 访问 http://192.168.1.151:1313/
```

停止：`pkill -f "hugo server"`

## 依赖（本机）

- `hugo`（0.168.0 extended）— `yay -S hugo`
- `dart-sass` — `yay -S dart-sass`（**必需**，FixIt 的样式是 SCSS，没有它构建直接失败）
- 主题在 `themes/FixIt`，是 **git submodule**，拉仓库要 `git submodule update --init --recursive`

## URL 规则（重要，别破坏）

所有文章 front matter 里都有 **显式 `url`** 字段：

```yaml
url: "/2016/12/FMS01-brew/"
```

**原因**：Hugo 默认把路径转小写，`FMS01-brew` 会变成 `fms01-brew`，导致 2016 年的旧链接失效。
所以**新旧文章都保留显式 `url`**，路径保持 `/YYYY/MM/slug/` 格式。

旧站 36 篇的 URL 已逐一核对，与原站**完全一致**。

## GitHub Actions 的两个坑（已修复，别改回去）

1. **`npm install -g sass` 会崩**
   CI 上报 `got unexpected EOF when executing "sass"` / `connection is shut down`。
   必须用**官方 Dart Sass 二进制**（解压 + 软链到 `/usr/local/bin/sass`）。

2. **`peaceiris/actions-hugo` 的 `extended: true` 不保险**
   可能静默装成普通版，导致 `TOCSS-DART ... not available in your current Hugo version`。
   改成直接下载 `hugo_extended_${VERSION}_linux-amd64.tar.gz`。

## 排查部署失败

**Actions 日志 API 需要 admin 权限，沙箱里拿不到（403）。**
👉 **直接请用户打开 https://github.com/zhangjinglin/zhangjinglin.github.io/actions 贴报错**，不要自己瞎猜。

## 内容现状

- **37 篇**文章：36 篇旧文（2016-02 ~ 2017-05）+ 1 篇《重新开始》
- 旧文从 Hexo 渲染的 HTML 反解为 Markdown，**547 个代码块逐字校验零差异**
- 图片 26 张、PDF 8 个，原样迁移到 `static/`
- **线性代数 8 篇（xxds01-08）**：正文只有一句话，内容全在 PDF 里。
  用户明确说过：**那就是当时的想法，扔在那扔着就好，不要动它**。
- 原站备份：`/home/zjl/blog/backup-original/`（完整 27 条 git 历史）

## 迁移脚本

`/home/zjl/blog/_migrate/`（**不在仓库里**）

- `convert.py` — 单篇 HTML → Markdown
- `migrate_all.py` — 全量迁移 + 代码块数量校验

需要重新迁移时：`python3 migrate_all.py`，它会**覆盖** `content/posts/` 下的文件。
平时用不到，改文章直接在 `content/posts/` 里改 md。

## 站点配置

- `config/_default/hugo.toml` — 站点名、baseURL、时区、语言
- `config/_default/params.toml` — 主题全部参数（1548 行，**原样取自官方 starter**，只改了几处）
  - 已改：`description`、`keywords`、`author`、`header.title/subtitle`（打字机效果开）
  - `home.profile.enable = true`（首页个人简介块）
  - `footer.since = "2016"`
- `config/_default/menus.toml` — 顶部菜单：文章 / 归档 / 分类 / 标签 / 关于
- `config/_default/permalinks.toml` — `/ :year/:month/:slugorcontentbasename /`

## 还欠着的事

- 字体：新站已不再引用 `fonts.googleapis.com`（旧站的加载问题已解决），目前用系统字体栈
- favicon 缺失（`/favicon.ico`、`/favicon-32x32.png` 等），主题模板引用了但仓库里没有
- 评论系统未配置（FixIt 支持 giscus 等多种，需要时再开）
- 搜索：FixIt 内置 Pagefind，已生成 `search.json`，可用
