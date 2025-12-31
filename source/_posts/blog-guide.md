---
title: 从零开始：基于 Hexo + GitHub + Cloudflare 搭建个人博客全攻略
date: 2025-12-31 20:00:00
categories: [技术分享]        
tags: [Hexo, 教程, 个人博客]  
---

这篇文章记录了我从零开始搭建本博客的全过程，涵盖了环境配置、框架安装、云端部署及主题美化，希望能帮到想拥有自己博客的你。

## 一、 环境准备

在开始之前，确保你的电脑已安装：

1. **Node.js**: 建议选择 LTS 版本。
2. **Git**: 用于代码版本管理和推送。

## 二、 核心步骤

### 1. 建立 GitHub 远程仓库

- 在 GitHub 新建名为 `username.github.io` 的仓库。
- 这一步是后续 Cloudflare 读取源码的基础。

### 2. Cloudflare Pages 关联

- 登录 Cloudflare 控制台，进入 Pages 选项。
- 关联你的 GitHub 仓库。
- 设置构建指令：`npx hexo generate`，输出目录：`public`。

### 3. 本地安装 Hexo

在本地文件夹中打开终端（管理员模式）：

```bash
npm install -g hexo-cli
hexo init My_Blog
cd My_Blog
npm install
```

### 4. 主题安装与美化（以 Fluid 为例）

为了让博客更好看，我们更换了 Fluid 主题：

```bash
npm install hexo-theme-fluid --save
```

修改根目录 `_config.yml` 中的 `theme: fluid`，并设置 `language: zh-CN`。

## 三、 常见坑点记录

1. **权限问题**: 在 Windows 下运行 `hexo` 命令报错时，需以管理员运行 PowerShell 并执行 `set-ExecutionPolicy RemoteSigned`。
2. **网络问题**: `hexo init` 失败时，可手动从 GitHub 下载 zip 包解压，再通过 `npm install` 补全依赖。
3. **忽略文件**: 务必检查 `.gitignore`，确保 `node_modules` 和 `public` 不会被推送到仓库。

## 四、 自动化工作流

现在的开发节奏非常舒服：

- 本地写稿：`hexo new "标题"`
- 本地预览：`hexo s`
- 一键发布：`git add .` -> `git commit` -> `git push`

Cloudflare 会在接收到推送后，自动完成后续的编译和全球分发
