
# 📦 自动部署 - 从指定目录解压并发布

> 🚀 一键自动化部署：从指定目录中提取最新的 ZIP 包，解压替换根目录内容，并自动提交更新。

这个 GitHub Action 可用于静态网站、博客、文档等项目的自动化部署流程。它会自动查找指定目录中的最新 ZIP 文件，解压后替换项目根目录内容（保留关键文件），并推送变更到仓库。

非常适合配合 CI/CD 打包流程（如构建并上传 zip 的 workflow）使用。

---

## ✅ 功能亮点

- 🔍 自动识别指定目录下最新的 `.zip` 文件  
- 📥 解压到临时目录并安全替换根目录内容  
- 🧹 智能清理：保留 `.git`, `.github`, 配置文件等重要目录/文件  
- 🛠 支持自定义保留文件列表  
- 🗑 可选删除原始 ZIP 目录  
- 💡 自动 Git 提交更改（无更改则跳过）  
- ⚙ 完全可配置，开箱即用  

---

## 🛠 使用方法

在你的 GitHub Workflow 文件中使用此 Action，并**务必配置写入权限**，否则 `git push` 会失败。

### ✅ 完整 Workflow 示例（含权限配置）

```yaml
name: Deploy Site

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write  # ✅ 必须：允许 Action 修改仓库内容
    steps:
      - name: 自动部署网站
        uses: naiyniB/WorkFlow-Unzip-Deploy@xxx
        with:
          File_directory: "public"
          exclude_files: "CNAME|.nojekyll"
          delete_zip_directory: "true"
```


---

## ⚙ 输入参数（Inputs）

| 参数名 | 描述 | 是否必填 | 默认值 |
|-------|------|--------|-------|
| `File_directory` | ZIP 文件所在的文件夹路径 | 否 | `public` |
| `exclude_files` | 在默认保留项基础上，额外保留的文件或文件夹（用 `|` 分隔）<br>例如：`CNAME|README.md` | 否 | `""`（空） |
| `delete_zip_directory` | 是否在部署后删除 ZIP 所在目录？`true` 或 `false` | 否 | `true` |

### 默认保留文件/目录：
`.git` \| `.github` \| `temp` \| `${{ inputs.File_directory }}`

> 这些文件和目录在清理阶段不会被删除。

---

## 🧪 工作流程步骤

1. 拉取代码  
2. 检查 `File_directory` 是否存在  
3. 查找该目录下最新的 `.zip` 文件  
4. 解压到 `temp/` 目录  
5. 清理根目录（保留指定项）  
6. 将 `temp/` 内容移动到根目录  
7. （可选）删除原始 ZIP 目录  
8. 提交更改并推送到原分支  

---

## 📝 注意事项

- ✅ **必须配置权限**：  
  由于该 Action 会执行 `git push`，你必须在 workflow 中为 `contents` 设置 `write` 权限：
  ```yaml
  permissions:
    contents: write
  ```
  否则会报错：`Permission to repo.git denied`。

- 确保 ZIP 包内包含你希望部署的所有文件（如 `index.html`, `assets/` 等）。
- 若无文件变更，提交步骤会自动跳过。
- 建议在 `.github/workflows/deploy.yml` 中使用此 Action。
- 如果你使用的是受保护分支（如 `main`），请在仓库设置中为 `github-actions[bot]` 配置 **允许绕过分支保护** 或 **允许 Actions 推送** 的权限（Settings → Branches → Branch protection rules）。

---

## 🤝 贡献与反馈

欢迎提交 Issue 或 Pull Request！如果你发现 Bug 或希望增加功能，请随时联系。

---

## 📜 许可证

MIT


