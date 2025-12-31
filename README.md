# My Hexo Blog Source

这是我的个人博客源码仓库。

## 项目架构
- **框架**: [Hexo](https://hexo.io/)
- **主题**: [Fluid](https://github.com/fluid-dev/hexo-theme-fluid)
- **部署**: [Cloudflare Pages](https://pages.cloudflare.com/) + [Github Pages](https://github.com/Runner-8/)
- **环境**: Node.js

## 本地开发调试
1. 安装依赖：`npm install`
2. 本地预览：`hexo s`
3. 新建文章：`hexo new "title"`
4. 清理缓存：`hexo clean`

## 自动化部署
本项目已关联 Cloudflare Pages，只需将修改 `git push` 到 `main` 分支，即可触发全自动构建与发布。