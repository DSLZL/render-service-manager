## Render 服务管理系统

这是一个基于 Cloudflare Workers 开发的 Render 服务管理系统，支持多账号管理和环境变量操作。前后端一体化设计，使用原生 HTML/CSS/JavaScript 实现。

### 项目描述

本项目旨在简化 Render 服务的管理，提供安全登录、服务列表展示、部署操作和环境变量管理功能。适合开发者和运维人员快速管理多个 Render 账号下的服务。

**主要特性：**
- 支持多个 Render 账号
- 安全认证和会话管理
- 服务信息展示和一键Deployment
- 环境变量的读取、添加、编辑和删除
- 现代化的 UI 设计，响应式布局

### 功能列表

- **登录系统**：使用用户名/密码认证，支持 24 小时会话保持。
- **服务概览**：统一网格布局展示所有服务，支持多账号标识。
- **部署操作**：一键触发 Render 服务部署。
- **环境变量管理**：内联编辑，支持默认隐藏值、搜索过滤和批量操作。
- **通知系统**：实时反馈操作结果。

### 要求

- Cloudflare 账户
- Render API Key (从 Render 仪表板获取)

### 安装和部署

#### 方式 1: 通过 Wrangler CLI 部署（推荐）

1. **克隆仓库**：
   ```
   git clone https://github.com/your-repo/render-manager.git
   cd render-manager
   ```

2. **安装 Wrangler CLI**：
   ```
   npm install -g wrangler
   ```

3. **配置 wrangler.toml**：
   - 编辑 `wrangler.toml` 文件，添加 KV 命名空间和环境变量。
   - 示例：
     ```
     name = "render-manager"
     compatibility_date = "2025-08-01"
     workers_dev = true
     [vars]
     ADMIN_USERNAME = "admin"
     ADMIN_PASSWORD = "your-strong-password"
     RENDER_ACCOUNTS = '[{"id": "account1", "name": "Account 1", "apiKey": "rnd_xxx"}]'
     SESSION_SECRET = "your-random-secret"
     kv_namespaces = [
       { binding = "RENDER_KV", id = "你的KV_ID" }
     ]
     ```

4. **创建 KV 命名空间**（如果尚未创建）：
   ```
   npx wrangler kv:namespace create RENDER_KV
   ```
   - 将返回的 ID 添加到 wrangler.toml 的 kv_namespaces 中。

5. **登录并部署**：
   ```
   npx wrangler login
   npx wrangler deploy
   ```

#### 方式 2: 手动部署（无需 Wrangler CLI）

如果您不想安装 CLI，可以直接在 Cloudflare 仪表盘中手动部署 Worker。

1. **登录 Cloudflare 账户**：
   - 访问 [Cloudflare Dashboard](https://dash.cloudflare.com/) 并登录。

2. **创建 KV 命名空间**（如果不存在）：
   - 导航到 "Workers" > "KV"。
   - 点击 "Create a namespace"。
   - 输入名称（如 "RENDER_KV"），复制生成的 ID 备用。

3. **创建 Worker**：
   - 导航到 "Workers" > "Overview"。
   - 点击 "Create a Worker"。
   - 输入 Worker 名称（如 "render-manager"）。

4. **编辑脚本文档**：
   - 在 Worker 编辑器中，复制并粘贴本项目的完整 JavaScript 代码（从仓库的 index.js 文件中获取）。

5. **配置环境变量**：
   - 点击 "Settings" > "Variables"。
   - 添加以下环境变量：
     - ADMIN_USERNAME: "admin"
     - ADMIN_PASSWORD: "your-strong-password"
     - RENDER_ACCOUNTS: '[{"id": "account1", "name": "Account 1", "apiKey": "rnd_xxx"}]'
     - SESSION_SECRET: "your-random-secret"

6. **绑定 KV 命名空间**：
   - 在 "Settings" > "Bindings" > "KV Namespace Bindings" 部分。
   - 点击 "Add binding"。
   - 输入变量名称 "RENDER_KV"（必须与代码中的绑定名匹配）。
   - 选择步骤 2 中创建的 KV 命名空间。

7. **部署 Worker**：
   - 点击 "Save and Deploy"。
   - Worker 将立即可用，您可以通过提供的 URL 访问（例如: render-manager.your-subdomain.workers.dev）。

**注意**：手动部署適合簡單場景，如果需要頻繁更新代碼，推薦使用 Wrangler CLI 以進行版本控制。

### 配置说明

- **ADMIN_USERNAME / ADMIN_PASSWORD**：管理系统登录凭据。
- **RENDER_ACCOUNTS**：JSON 数组，每个对象包含 `id`、`name` 和 `apiKey`。
- **SESSION_SECRET**：用于会话安全。
- **RENDER_KV**：KV 命名空间用于存储会话。

示例 RENDER_ACCOUNTS：
```json
[
  {"id": "account1", "name": "Main Account", "apiKey": "rnd_key1"},
  {"id": "account2", "name": "Test Account", "apiKey": "rnd_key2"}
]
```

### 使用指南

1. **登录**：访问根路径，输入用户名/密码登录。
2. **服务概览**：登录后查看所有服务信息。
3. **部署服务**：点击服务卡片上的 "部署" 按钮。
4. **管理环境变量**：
   - 点击 "环境变量" 打开模态框。
   - 值默认隐藏，点击眼睛图标显示。
   - 点击值区域编辑。
   - 添加新变量：填写键值后保存。
   - 点击服务卡片上的 "部署" 按钮重新部署使生效。

### 技术栈

- **后端**：Cloudflare Workers, Fetch API
- **前端**：原生 HTML/CSS/JavaScript
- **存储**：Cloudflare KV (会话管理)
- **API**：Render API v1

### 注意事项

- 确保 Render API Key 正确，具备所需权限。
- 系统支持多账号，但每个账号需单独配置 API Key。
- 环境变量值敏感，生产环境中避免泄露。

### 贡献

欢迎提 Issue 或 Pull Request。开发时请遵循原有代码规范。

### 许可证

MIT License. 版权所有 © 2025 [sfun](https://github.com/ssfun)
