# my-blog

基于 GitHub Pages + Jekyll（minima 主题）的在线博客，使用 Obsidian 写作。

## 项目结构

```
_config.yml          # Jekyll 配置（站点标题、url、baseurl 等）
index.md             # 首页，通过 Liquid 循环自动生成文章列表
_posts/              # 所有文章（Jekyll 只扫描此目录，且不扫描子目录）
  2026-09-30-hello-world.md
  2026-09-30-ai-coding.md
  2026-09-30-设计机器学习应用系统.md
```

## 新增文章 → 发布流程

### 1. 新建文章文件

在 `_posts/` 目录下新建 Markdown 文件，文件名格式必须为：

```
YYYY-MM-DD-文章标题.md
```

例如 `2026-10-01-my-first-post.md`。日期即文章发布日期，同时决定首页列表顺序（越新越靠前）。

> 注意：文章必须直接放在 `_posts/` 根目录下，**不能放进子文件夹**，Jekyll 不会处理 `_posts` 子目录里的文件。

### 2. 添加 front matter

文件开头必须包含 front matter（两条 `---` 之间的 YAML）：

```markdown
---
layout: post
title: 文章标题
---

正文内容……
```

- `layout: post`：使用主题的文章页布局。
- `title`：文章标题，会显示在文章页和首页列表中。

### 3. 提交并推送

```bash
git add .
git commit -m "post: 新增文章《文章标题》"
git push
```

推送到 `main` 分支后，GitHub Pages 会自动构建并发布，通常 1~2 分钟生效。

### 4. 验证发布

打开 <https://holmes-pengge.github.io/my-blog/> 确认：

- 首页出现新文章链接；
- 点击链接能正常打开文章页。

## 说明

- 站点地址：<https://holmes-pengge.github.io/my-blog/>。仓库是 project page，`_config.yml` 中的 `baseurl: "/my-blog"` 必须保留，否则链接和样式会 404。
- 首页列表由 `index.md` 中的循环自动生成，无需手动维护：

  ```liquid
  {% for post in site.posts %}
  - [{{ post.title }}]({{ post.url | relative_url }})
  {% endfor %}
  ```

- 修改文章、删除文章同样只需编辑 / 删除 `_posts/` 中对应文件后，commit 并 push。
