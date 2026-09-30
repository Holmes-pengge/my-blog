# my-blog

基于 GitHub Pages + Jekyll（minima 主题）的在线博客，使用 Obsidian 写作。

## 项目结构

```
_config.yml          # Jekyll 配置（theme、baseurl、plugins、exclude）
index.md             # 首页，自动生成文章列表（跳过 hidden 文章）
_posts/              # 文章 + 需要被链接打开的文字笔记（Jekyll 只扫描此目录，且不扫描子目录）
assets/              # 图片等静态资源（如 Excalidraw 导出的 SVG）
<其他文件/目录>       # Obsidian 库内容，git 版本管理，但不发布到站点
```

> 发布范围：只有 `_posts/` 和 `assets/` 中的内容会构建到站点；其余内容通过 `_config.yml` 的 `exclude` 排除，仅保留在 git 仓库中。

## 一次性设置（Obsidian）

设置 → 文件与链接：

- 关闭「使用 [[Wikilinks]]」
- 新建链接格式选「相对路径」

这样笔记互链写成 `[文字](../路径/笔记.md)`，构建时 `jekyll-relative-links` 插件会自动转换成线上正确 URL（含 `/my-blog` 前缀，文章页和静态文件都支持）。

## 新增文章 → 发布流程

### 1. 新建文章文件

在 `_posts/` 目录下新建 Markdown 文件，文件名格式必须为：

```
YYYY-MM-DD-文章标题.md
```

日期即发布日期，同时决定首页顺序（越新越靠前）。

> 文章必须直接放在 `_posts/` 根目录下，**不能放进子文件夹**，Jekyll 不会处理子目录里的文件。

### 2. 添加 front matter

```markdown
---
layout: post
title: 文章标题
---

正文内容……
```

### 3. 链接其他文章 / 图片

一律使用相对路径（Obsidian 会自动生成）：

```markdown
[另一篇文章](../_posts/2026-09-29-机器学习.md)
![图片说明](../assets/xxx.svg)
```

构建时这些相对路径会被自动改写为线上 URL，无需手动处理 `/my-blog` 前缀。

### 4. 提交并推送

```bash
git add .
git commit -m "post: 新增文章《文章标题》"
git push
```

推送到 `main` 后 GitHub Pages 自动构建发布，通常 1~2 分钟生效。

### 5. 验证发布

打开 <https://holmes-pengge.github.io/my-blog/> 确认：首页出现新文章、图片正常显示、站内链接能打开。

## 文字笔记（作为页面被链接打开）

- 与文章同样放进 `_posts/`，命名 `YYYY-MM-DD-标题.md`，front matter 使用 `layout: post`
- 不想出现在首页列表 → 加 `hidden: true`
- URL 形如 `/2026/09/29/标题.html`，其他文章用相对路径链接即可

## Excalidraw 手绘图

1. 在 Excalidraw 中导出 SVG（可在插件设置开启 Auto-export SVG）
2. 把 `.svg` 放进 `assets/`（建议与源文件同名）
3. 文章中用 `![说明](../assets/xxx.svg)` 引用

注意：

- 原始 `.excalidraw.md` 留在库里即可，不会发布
- 不要用 `[[...]]` / `![[...]]`，Jekyll 不识别 wikilink
- 不要用 `[![...](img)](img)` 嵌套写法（插件无法完整重写内层图片路径）；需要「点击打开原图」时，另起一行写 `[打开原图](../assets/xxx.svg)`

## 发布范围与 exclude

- 站点只发布 `_posts/` 和 `assets/` 的内容
- 新增不想发布的顶层目录/文件时，把名字加进 `_config.yml` 的 `exclude`（目录名即可，其下内容会一起排除）
- Jekyll 的 `*` 通配符会跨目录匹配，**不要**用 `*.md` 这类过宽模式，否则 `_posts` 里的文章也会被排除

## 说明

- 站点地址：<https://holmes-pengge.github.io/my-blog/>。仓库是 project page，`_config.yml` 中的 `baseurl: "/my-blog"` 必须保留，否则链接和样式会 404。
- 首页列表由 `index.md` 自动生成，`hidden: true` 的文章不会出现在首页：

  ```liquid
  {% assign visible_posts = site.posts | where_exp: "post", "post.hidden != true" %}
  {% for post in visible_posts %}
  - [{{ post.title }}]({{ post.url | relative_url }})
  {% endfor %}
  ```

- 修改 / 删除文章：编辑或删除 `_posts/` 中对应文件后 commit + push 即可。
