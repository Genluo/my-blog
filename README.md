# Genluo的个人博客

这是一个基于Hugo框架和PaperMod主题的个人博客项目，用于分享个人经历、技术文章和见解。

## 项目特点

- 🚀 基于Hugo静态网站生成器，高效快速
- 🎨 使用PaperMod主题，简洁美观
- 🔍 内置搜索功能，方便查找内容
- 📂 文章归档功能，便于内容管理
- 🏷️ 标签系统，便于内容分类
- 📱 响应式设计，适配各种设备
- 🌓 支持自动/深色/浅色主题切换
- 📊 阅读时间和字数统计

## 技术栈

- [Hugo](https://gohugo.io/) - 静态网站生成器
- [PaperMod](https://github.com/adityatelange/hugo-PaperMod) - 主题

## 开始使用

### 前提条件

- 安装 [Hugo](https://gohugo.io/installation/)（推荐使用扩展版本）
- Git

### 本地开发

1. 克隆仓库：

```bash
git clone https://github.com/Genluo/my-blog.git
cd my-blog
```

2. 初始化子模块（主题）：

```bash
git submodule update --init --recursive
```

3. 启动本地开发服务器：

```bash
hugo server -D
```

4. 在浏览器中访问 `http://localhost:1313/my-blog/` 预览博客

### 创建新文章

```bash
hugo new content/posts/my-new-post.md
```

### 构建静态网站

```bash
hugo --minify
```

构建后的静态文件会生成在 `public/` 目录下。

## 部署

本项目配置为通过GitHub Actions自动部署到GitHub Pages。每次推送到主分支时，会自动构建并部署网站。

## 目录结构

```
my-blog/
├── archetypes/        # 文章模板
├── content/           # 博客内容
│   ├── posts/         # 博文
│   ├── archives/      # 归档页
│   ├── search/        # 搜索页
│   └── about/         # 关于页
├── layouts/           # 自定义布局
├── static/            # 静态资源
├── themes/            # 主题
│   └── PaperMod/      # PaperMod主题
└── hugo.toml          # Hugo配置文件
```

## 自定义

您可以通过修改 `hugo.toml` 文件自定义博客的各种设置，包括标题、描述、社交媒体链接等。

## 许可证

[MIT](LICENSE)
