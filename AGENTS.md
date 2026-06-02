# Project Agents Guideline - Hexo Blog

本项目是一个基于 Hexo 框架搭建的静态博客系统，并使用了 Butterfly 主题。本文件旨在帮助 AI Agent 快速熟悉该项目并规范后续的代码修改与内容创作。

## 1. 项目概述

- **项目类型**：Hexo 静态博客系统
- **Hexo 版本**：`^8.0.0`
- **当前主题**：`butterfly`（存放于 `themes/butterfly`）
- **站点 URL**：https://blog.oyyp.top
- **默认语言**：英文 (`en`)

## 2. 目录结构

```text
blog/
├── _config.yml             # Hexo 全局配置文件（包含站点标题、作者、主题指定等）
├── package.json            # 项目依赖和脚本命令
├── scaffolds/              # 文章新建模板
│   ├── post.md             # 文章模板
│   ├── page.md             # 页面模板
│   └── draft.md            # 草稿模板
├── source/                 # 源文件目录
│   └── _posts/             # 存放 Markdown 格式的博客文章
└── themes/                 # 博客主题目录
    └── butterfly/          # 当前启用的 Butterfly 主题目录
```

## 3. 常用开发命令

在根目录下可执行以下 npm 脚本：

- **清理缓存与静态文件**：
  ```bash
  npm run clean
  ```
- **生成静态页面**（生成的内容会输出到 `public/` 目录）：
  ```bash
  npm run build
  ```
- **启动本地预览服务**（默认运行在 `http://localhost:4000`）：
  ```bash
  npm run server
  ```
- **部署站点**：
  ```bash
  npm run deploy
  ```

## 4. Agent 开发与维护指南

### 4.1 新增或修改文章
- 博客文章均存放在 `source/_posts/` 目录下，文件格式为 Markdown (`.md`)。
- 新建文章可以使用命令：
  ```bash
  npx hexo new post "文章标题"
  ```
- 文章头部包含 Front-matter（元数据），其格式由 `scaffolds/post.md` 定义。常见字段包括：
  - `title`：文章标题
  - `date`：创建时间
  - `tags`：标签列表
  - `categories`：分类列表

### 4.2 配置修改
- **全局配置**：若需修改网站名称、作者、语言、URL 等，请编辑根目录下的 `_config.yml`。
- **主题配置**：Butterfly 主题的个性化配置主要位于 `themes/butterfly` 主题目录下的配置文件中。如果根目录下存在 `_config.butterfly.yml`，Hexo 会优先合并该文件，建议在此进行自定义配置以避免直接修改主题源码。

### 4.3 渲染与插件依赖
- 本项目依赖了以下关键插件：
  - HTML 渲染：`hexo-renderer-ejs`，`hexo-renderer-pug`
  - CSS 渲染：`hexo-renderer-stylus`
  - Markdown 渲染：`hexo-renderer-marked`
- 引入新的插件前需评估其安全性，避免引入包含已知漏洞的第三方库。
