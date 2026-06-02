---
title: 使用 GitHub Pages 自定义域名的完整指南
date: 2026-06-02 10:23:00
tags:
  - GitHub Pages
  - 域名配置
  - DNS
  - 博客搭建
categories:
  - 技术分享
cover: https://images.unsplash.com/photo-1544197150-b99a580bb7a8?w=800
description: 本文详细介绍了如何为 GitHub Pages 静态网站配置自定义域名，涵盖子域名（Subdomain）与顶级域名（Apex Domain）的 DNS 解析设置方法，以及 Hexo 部署中的 CNAME 防覆盖处理和 HTTPS 启用步骤。
---

在使用 GitHub Pages 托管博客或个人网站时，默认生成的域名通常是 `username.github.io`。虽然这个域名已经能够正常访问，但为了建立更专业的个人品牌，配置一个属于自己的自定义域名（如 `oyyp.top` 或 `blog.oyyp.top`）是必经之路。

本文将为您全面解析 GitHub Pages 自定义域名的配置方法，分为**子域名**和**顶级域名**两种场景进行实操说明。

---

## 1. 核心概念速览

在开始配置之前，我们先简单了解两个概念：
*   **顶级域名（Apex Domain / Naked Domain）**：例如 `oyyp.top`。这种域名没有 `www` 或其他前缀。
*   **子域名（Subdomain）**：例如 `blog.oyyp.top` 或 `www.oyyp.top`。它是在顶级域名前加上了二级前缀。

---

## 2. 场景一：配置子域名（如 `blog.oyyp.top`）

子域名的解析配置最为简单，推荐采用 `CNAME` 记录指向 GitHub 的默认域名。

### 第一步：在 DNS 服务商处添加解析记录
登录您购买域名的服务商控制台（如阿里云、腾讯云、Cloudflare、GoDaddy 等），进入 DNS 解析设置页面，添加以下记录：

| 记录类型 (Type) | 主机记录 (Name/Host) | 记录值 (Value/Target) | TTL |
| :--- | :--- | :--- | :--- |
| `CNAME` | `blog`（或您自定义的子域前缀） | `ouyangyuanpeng.github.io.` | 默认 / 600 |

> **注意**：记录值中的 `ouyangyuanpeng.github.io` 是您的 GitHub Pages 默认地址。部分 DNS 服务商要求记录值末尾加上点号 `.`，大部分直接填写即可。

### 第二步：在 GitHub 仓库中绑定域名
1. 登录 GitHub，进入托管您博客网页的源仓库（如 `ouyangyuanpeng.github.io` 或对应的主仓库）。
2. 点击仓库右上角的 **Settings** 选项卡。
3. 在左侧菜单栏中点击 **Pages**。
4. 在 **Custom domain** 输入框中，输入您的子域名（例如 `blog.oyyp.top`），然后点击 **Save**。

---

## 3. 场景二：配置顶级域名（如 `oyyp.top`）

配置顶级域名（即不带前缀的裸域名）时，因为 DNS 协议规范限制，一般**不能**在顶级域名上直接配置 `CNAME` 记录。我们需要将其指向 GitHub Pages 的官方 IP 地址。

### 第一步：在 DNS 服务商处配置 A 记录
登录域名解析控制台，为主机记录 `@` 分别添加以下 4 条 `A` 记录，将其指向 GitHub Pages 的 Anycast IP 地址列表：

| 记录类型 (Type) | 主机记录 (Name/Host) | 记录值 (Value/IP Address) |
| :--- | :--- | :--- |
| `A` | `@` | `185.199.108.153` |
| `A` | `@` | `185.199.109.153` |
| `A` | `@` | `185.199.110.153` |
| `A` | `@` | `185.199.111.153` |

### 第二步：添加辅助的 `www` 子域名解析（强烈建议）
为了让访客无论是输入 `oyyp.top` 还是 `www.oyyp.top` 都能成功访问您的网站，您需要再添加一条关于 `www` 的 `CNAME` 记录：

| 记录类型 (Type) | 主机记录 (Name/Host) | 记录值 (Value/Target) |
| :--- | :--- | :--- |
| `CNAME` | `www` | `ouyangyuanpeng.github.io` |

### 第三步：在 GitHub 仓库中绑定顶级域名
同样进入仓库的 **Settings** -> **Pages**，在 **Custom domain** 中填入您的顶级域名（例如 `oyyp.top`），然后点击 **Save**。GitHub Pages 会自动识别并配置 `www.oyyp.top` 到 `oyyp.top` 的安全重定向。

---

## 4. 关键避坑指南：Hexo 部署防覆盖机制（CNAME 文件）

当您在 GitHub 网页端的 Pages 设置中成功保存自定义域名后，GitHub 会在您的仓库根目录下自动创建一个名为 `CNAME` 的无后缀文本文件。

然而，**在 Hexo 静态博客中，如果直接运行 `hexo d` 重新部署，整个远程仓库的静态文件会被本地生成的 `public/` 目录全面替换，这也将导致 GitHub 自动创建的 `CNAME` 文件被彻底删除，致使您的自定义域名绑定失效。**

### 解决方案：
您需要将 `CNAME` 文件持久化在本地的 Hexo 源文件中：

1. 在本地 Hexo 项目的 `source` 目录下（绝对路径为 `blog/source/`），新建一个名为 `CNAME` 的纯文本文件（无后缀名）。
2. 打开该文件，在其中写入您的自定义域名（只写域名，不要加 `http://` 或任何路径），例如：
   ```text
   blog.oyyp.top
   ```
3. 保存文件。

这样，每次您在本地运行 `hexo g` (生成静态页面) 时，Hexo 编译器就会自动将 `source/CNAME` 完好地复制到编译生成目录 `public/CNAME` 中，并在 `hexo d` 部署时一同上传至 GitHub，从而永久保证域名绑定的稳定性。

---

## 5. 启用安全 HTTPS 连接

在完成域名解析并成功绑定后，您还需要为网站启用安全加密连接：

1. 在 GitHub 仓库的 **Settings** -> **Pages** 页面中，找到 **Enforce HTTPS** 选项。
2. 勾选 **Enforce HTTPS**。

> **注意**：当您刚绑定新域名时，这个复选框可能会呈灰色不可选中状态。这是因为 GitHub 正在后台为您向 Let's Encrypt 证书颁发机构申请免费的 SSL 证书。证书申请和 DNS 解析在全球的生效一般需要 5 分钟到几小时不等，耐心等待解析生效后刷新页面即可勾选。
