# WiNSQL v2.0.0 发布部署指引

> 本地构建已完成，以下是剩余的手动操作步骤。
> 预计操作时间: 30~60 分钟

---

## 已完成清单（本地自动构建）

- [x] 客户端 Windows 安装包构建（70.2 MB）
- [x] 服务端 Windows 安装包构建（73.2 MB）
- [x] 服务端 Linux 安装包构建（64.3 MB）
- [x] SHA256 校验值生成
- [x] 发布网站部署目录准备（index.html + 11张截图 + releases.json）
- [x] 下载链接配置（GitHub Releases 格式，含 GITHUB_USERNAME 占位符）
- [x] Git 仓库初始化 + 首次提交

### 安装包文件位置

```
e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\publish\packages\
├── WiNSQL-Client-2.0.0-windows-x64.zip       (70.2 MB)  SHA256: 6216fb40...
├── SqlQuick.Server-2.0.0-win-x64.zip          (73.2 MB)  SHA256: 5ffa4d5a...
└── SqlQuick.Server-2.0.0-linux-x64.tar.gz     (64.3 MB)  SHA256: 322c1a5f...
```

### 网站文件位置

```
e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\docs\website\
├── index.html                  ← 企业蓝风格发布网站
├── releases.json               ← 版本元数据（含SHA256）
├── .gitignore
└── assets/screenshots/         ← 11张UI截图
```

---

## 待完成步骤（手动操作）

### 步骤 1: 替换 GitHub 用户名

将 `index.html` 和 `releases.json` 中的 `GITHUB_USERNAME` 替换为你的实际 GitHub 用户名。

**方法一: PowerShell 全局替换**

```powershell
# 替换为你的实际 GitHub 用户名
$username = "你的用户名"

# 替换 index.html
$file = "e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\docs\website\index.html"
(Get-Content $file) -replace 'GITHUB_USERNAME', $username | Set-Content $file -Encoding UTF8

# 替换 releases.json
$file = "e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\docs\website\releases.json"
(Get-Content $file) -replace 'GITHUB_USERNAME', $username | Set-Content $file -Encoding UTF8

# 提交到 Git
cd "e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\docs\website"
git add .
git commit -m "替换 GitHub 用户名"
```

**方法二: 手动编辑**

用文本编辑器打开 `index.html` 和 `releases.json`，搜索 `GITHUB_USERNAME`，替换为你的实际用户名。

---

### 步骤 2: 创建 GitHub 仓库

登录 GitHub (https://github.com)，创建两个仓库:

**仓库 1: 网站仓库**
1. New repository → 名称: `winsql-website` → Public → Create
2. 不勾选 "Add a README file"（本地已有内容）

**仓库 2: 安装包仓库**
1. New repository → 名称: `winsql-releases` → Public → Create
2. 不勾选 "Add a README file"

---

### 步骤 3: 推送网站代码到 GitHub

```powershell
cd "e:\360MoveData\Users\lenovo\Desktop\SqlQuickSolution\docs\website"

# 添加远程仓库（替换 你的用户名）
git remote add origin https://github.com/你的用户名/winsql-website.git

# 推送
git push -u origin main
```

如果提示输入密码，使用 GitHub Personal Access Token（不是账号密码）:
1. GitHub → Settings → Developer settings → Personal access tokens → Generate new token
2. 勾选 `repo` 权限 → 生成 → 复制 token
3. 推送时密码处粘贴 token

---

### 步骤 4: 创建 GitHub Release 并上传安装包

1. 打开 `https://github.com/你的用户名/winsql-releases`
2. 点击右侧 "Releases" → "Create a new release"
3. 填写:
   - Tag: `v2.0.0`（点击 Create new tag）
   - Title: `WiNSQL v2.0.0`
   - Description:
     ```
     ## WiNSQL v2.0.0

     ### 新功能
     - 新增「我的资产」功能，支持 SQL 资产管理与分组
     - 新增跨库 SQL 函数翻译引擎（5 种数据库互转）
     - 新增 Postman / SoapUI 工作台
     - 新增服务端集群管理、熔断器、限流配置

     ### 系统要求
     - 客户端: Windows 10 1809+ / macOS 12+
     - 服务端: Windows Server 2019+ / Ubuntu 20.04+
     ```
4. 拖拽上传 3 个安装包文件:
   - `WiNSQL-Client-2.0.0-windows-x64.zip`
   - `SqlQuick.Server-2.0.0-win-x64.zip`
   - `SqlQuick.Server-2.0.0-linux-x64.tar.gz`
5. 点击 "Publish release"

---

### 步骤 5: 部署网站到 Cloudflare Pages

**方法一: Git 连接自动部署（推荐）**

1. 打开 https://dash.cloudflare.com → Workers & Pages → Create application → Pages
2. 选择 "Connect to Git"
3. 授权 GitHub → 选择 `winsql-website` 仓库
4. 配置:
   - Production branch: `main`
   - Framework preset: None
   - Build command: （留空）
   - Build output directory: `/`
5. Save and Deploy
6. 等待部署完成 → 获得地址: `https://winsql-website.pages.dev`

**方法二: 手动上传**

1. Workers & Pages → Create → Pages → Upload assets
2. 项目名: `winsql-website`
3. 拖拽 `docs/website/` 目录下所有文件上传
4. Deploy

---

### 步骤 6: 验证

打开浏览器访问 `https://winsql-website.pages.dev`:

- [ ] 网站正常加载，图片显示正常
- [ ] 点击「下载 Windows 版」→ 浏览器开始下载 zip 文件
- [ ] 点击「下载 Windows Server」→ 浏览器开始下载 zip 文件
- [ ] 点击「下载 Linux」→ 浏览器开始下载 tar.gz 文件
- [ ] 点击「macOS 版本」→ 弹出"即将发布"提示
- [ ] 导航栏各链接可正常跳转
- [ ] 移动端布局正常（手机浏览器测试）

---

### 后续: 绑定自定义域名（可选）

1. Cloudflare Pages → winsql-website → Custom domains → Set up
2. 输入域名 → 添加 DNS 记录
3. 等待生效 → HTTPS 自动配置

---

## 版本迭代流程（后续版本）

以 v2.1.0 为例:

```powershell
# 1. 构建新版本
dotnet publish src/Client.App/Client.App.csproj -c Release -r win-x64 --self-contained -o publish/client-windows /p:Version=2.1.0 /p:RunAnalyzers=false
Compress-Archive -Path publish/client-windows/* -DestinationPath publish/packages/WiNSQL-Client-2.1.0-windows-x64.zip -Force

# 2. 上传到 GitHub Release (tag: v2.1.0)

# 3. 更新网站下载链接
# 编辑 index.html 将 v2.0.0 改为 v2.1.0

# 4. 推送更新
cd docs/website
git add . && git commit -m "更新至 v2.1.0" && git push

# Cloudflare Pages 自动部署（如使用 Git 连接）
```
