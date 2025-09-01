# 部署说明

## GitHub Pages 自动部署

这个项目配置了 GitHub Actions 来自动构建和部署 Docusaurus 网站到 GitHub Pages。

### 设置步骤

1. **推送代码到 GitHub 仓库**
   ```bash
   git add .
   git commit -m "Add Docusaurus site"
   git push origin main
   ```

2. **在 GitHub 仓库中启用 GitHub Pages**
   - 进入仓库的 Settings > Pages
   - 在 Source 下选择 "Deploy from a branch"
   - 选择 `gh-pages` 分支
   - 点击 Save

3. **配置仓库权限**
   - 进入仓库的 Settings > Actions > General
   - 在 "Workflow permissions" 下选择 "Read and write permissions"
   - 勾选 "Allow GitHub Actions to create and approve pull requests"

### 部署触发条件

- 推送到 `main` 或 `master` 分支时自动部署
- Pull Request 时会进行构建测试（但不会部署）

### 访问网站

部署完成后，网站将在以下地址可用：
`https://wilinz.github.io/notebook/`

### 工作流文件

GitHub Actions 配置文件位于 `.github/workflows/deploy.yml`

### 手动构建和预览

```bash
# 安装依赖
npm install

# 开发服务器
npm run start

# 构建生产版本
npm run build

# 预览构建结果
npm run serve
```