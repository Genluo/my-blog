# 我的博客

这是基于 [Hugo](https://gohugo.io/) 构建的个人博客项目，使用了 [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 主题。

## 功能特点

- 响应式设计，适配各种设备
- 支持明/暗模式自动切换
- 文章阅读时间显示
- 文章分享按钮
- 文章导航链接
- 面包屑导航
- 支持 Mermaid 图表绘制

## 快速开始

### 前提条件

- 安装 [Hugo](https://gohugo.io/installation/)（推荐使用最新版本）
- Git

### 本地运行

1. 克隆此仓库
   ```bash
   git clone --recursive https://github.com/yourusername/my-blog.git
   cd my-blog
   ```

2. 启动本地服务器
   ```bash
   hugo server -D
   ```

3. 在浏览器中访问 `http://localhost:1313/` 查看博客

### 添加新文章

```bash
hugo new content/posts/my-new-post.md
```

然后编辑新创建的文件，添加您的内容。

## 项目结构

```
my-blog/
├── archetypes/        # 新内容的模板
├── assets/            # 需要处理的资源文件
├── content/           # 所有内容文件
│   ├── posts/         # 博客文章
│   └── about/         # 关于页面
├── data/              # 配置文件和数据文件
├── i18n/              # 国际化文件
├── layouts/           # 布局模板
├── public/            # 生成的静态网站（执行 hugo 命令后生成）
├── static/            # 静态文件，直接复制到 public/ 目录
├── themes/            # 主题文件
│   └── PaperMod/      # PaperMod 主题
└── hugo.toml          # Hugo 配置文件
```

## 部署

构建静态网站：

```bash
hugo --minify
```

生成的网站将位于 `public/` 目录中，您可以将此目录部署到任何静态网站托管服务，如 GitHub Pages、Netlify、Vercel 等。

## 自定义

- 修改 `hugo.toml` 文件以更新网站配置
- 在 `content/` 目录中添加或修改内容
- 在 `static/` 目录中添加静态资源文件
- 自定义主题可以修改 `layouts/` 目录中的模板

## 许可证

此项目采用 [MIT 许可证](LICENSE)。

## 致谢

- [Hugo](https://gohugo.io/) - 静态网站生成器
- [PaperMod](https://github.com/adityatelange/hugo-PaperMod) - Hugo主题
