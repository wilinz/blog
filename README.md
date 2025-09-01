# 个人文档中心

个人学习笔记和技术文档的集中管理平台，基于 [Docusaurus](https://docusaurus.io/) 构建的现代化静态网站。

## 项目特色

- 📚 技术文档和学习笔记管理
- ✍️ 博客文章发布
- 🌓 支持浅色/深色主题切换
- 📱 响应式设计，支持移动端
- 🔍 全文搜索功能
- 🚀 快速部署到 GitHub Pages

## 安装依赖

```bash
npm install
# 或
yarn install
```

## 本地开发

```bash
npm start
# 或
yarn start
```

此命令启动本地开发服务器并在浏览器中打开网站。大多数更改会实时反映，无需重启服务器。

## 构建

```bash
npm run build
# 或
yarn build
```

此命令生成静态内容到 `build` 目录，可以使用任何静态内容托管服务进行部署。

## 部署

部署到 GitHub Pages：

```bash
npm run deploy
# 或
yarn deploy
```

如果使用 SSH：

```bash
USE_SSH=true npm run deploy
```

不使用 SSH：

```bash
GIT_USER=<你的GitHub用户名> npm run deploy
```

## 项目结构

```
my-docs/
├── blog/                 # 博客文章
├── docs/                 # 文档页面
├── src/                  # 源代码
│   ├── components/       # 自定义组件
│   ├── css/             # 样式文件
│   └── pages/           # 自定义页面
├── static/              # 静态资源
├── docusaurus.config.ts # 配置文件
└── sidebars.ts         # 侧边栏配置
```

## 技术栈

- **框架**: Docusaurus 3.8.1
- **语言**: TypeScript
- **UI**: React 19
- **样式**: CSS Modules
- **部署**: GitHub Pages

## 网站访问

- 🌐 主站地址: [https://wilinz.github.io/notebook/](https://wilinz.github.io/notebook/)
- 📝 博客地址: [https://wilinz.github.io/blog](https://wilinz.github.io/blog)
- 📚 文档地址: [https://wilinz.github.io/notebook/docs](https://wilinz.github.io/notebook/docs)
- 📱 支持移动端访问

## License

MIT License
