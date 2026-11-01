# 📦 GitHub 上传包 · 清单与安装

这个目录（`D:\冰箱管家\github`）是**为上传 GitHub 准备好的完整资料包**。

**它不是仓库本身** —— 请按下面的步骤把文件铺设到项目根目录，再上传整个项目。

---

## 目录里有什么

```
github/
│
│  ── 第 1 步：复制到项目根目录（仓库内容）─────────────────────
├── README.md                    项目主 README（覆盖模板 README）
├── LICENSE                      MIT 开源协议
├── .gitignore                   覆盖版（补充 Netlify / Android / Python 忽略规则）
├── .env.example                 环境变量模板（只有键名，无真实值）
├── CONTRIBUTING.md              贡献指南（含 7 个易踩的坑）
├── SECURITY.md                  安全策略与密钥管理规定
├── CODE_OF_CONDUCT.md           行为准则
├── CHANGELOG.md                 更新日志（含完整 v1.0.0）
├── .github/                     Issue / PR 模板（按 GitHub 要求的位置就位）
├── docs/                        项目文档 6 篇
├── reference/                   原始规格与内部规范归档
│
│  ── 第 2 步：本地参考，不上传 ────────────────────────────────
├── meta/                        操作手册（仓库简介、操作清单等）
└── MANIFEST.md                  本文件
```

### 各部分说明

| 路径 | 内容 | 上传？ |
|------|------|--------|
| [`README.md`](README.md) | 项目门面：简介、功能、架构图、快速开始、部署、质量检查 | ✅ 覆盖到项目根 |
| [`LICENSE`](LICENSE) | MIT 协议，版权署名「冰箱管家」，年份 2026 | ✅ |
| [`.gitignore`](.gitignore) | 已补强，覆盖 Netlify 运行时、Android 构建产物、Python 缓存 | ✅ 覆盖 |
| [`.env.example`](.env.example) | 环境变量模板，含获取链接与「别加 `VITE_` 前缀」的警示 | ✅ |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | 贡献流程 + 代码规范 + 7 条真实踩坑记录 | ✅ |
| [`SECURITY.md`](SECURITY.md) | 漏洞报告、密钥管理、已知安全设计取舍、CORS 说明 | ✅ |
| [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) | Contributor Covenant 2.1 中文版 | ✅ |
| [`CHANGELOG.md`](CHANGELOG.md) | Keep a Changelog 格式，v1.0.0 完整记录 | ✅ |
| [`.github/`](.github/) | Bug / 功能 / 数据补充 Issue 模板 + PR 模板 | ✅ |
| [`docs/`](docs/) | 功能说明、架构总览、部署指南、智能录入链路、数据维护 | ✅ |
| [`reference/`](reference/) | 原始需求规格（历史归档）、早期代码指南、Trellis 架构与规范（含 ADR-001~009） | ✅ |
| [`meta/`](meta/) | 仓库简介、Topics、Release 说明、徽章、上传清单、迁移说明 | ❌ 本地保留 |
| `MANIFEST.md` | 本文件 | ❌ 本地保留 |

---

## 怎么用

### 第 1 步：复制到项目根目录

```powershell
$src = "D:\冰箱管家\github"
$dst = "D:\冰箱管家"

# 顶层文件
"README.md","LICENSE",".gitignore",".env.example","CONTRIBUTING.md",
"SECURITY.md","CODE_OF_CONDUCT.md","CHANGELOG.md" |
  ForEach-Object { Copy-Item "$src\$_" "$dst\$_" -Force }

# 目录
Copy-Item "$src\docs"      "$dst\docs"      -Recurse -Force
Copy-Item "$src\reference" "$dst\reference" -Recurse -Force
Copy-Item "$src\.github"   "$dst\.github"   -Recurse -Force
```

> 覆盖前先备份原有的 `README.md`（它是 Vite 模板的默认内容）：
> ```powershell
> Copy-Item "D:\冰箱管家\README.md" "D:\冰箱管家\README.md.bak"
> ```

### 第 2 步：替换占位符

`github/.github/ISSUE_TEMPLATE/config.yml` 里有 3 处 `OWNER/REPO`，替换为你的实际路径（如 `zhangsan/fridge-chef`）。

```powershell
(Get-Content "$dst\.github\ISSUE_TEMPLATE\config.yml") -replace 'OWNER/REPO','你的用户名/仓库名' |
  Set-Content "$dst\.github\ISSUE_TEMPLATE\config.yml"
```

### 第 3 步：密钥扫描（**不要跳过**）

```bash
git status --short                          # .env 不应出现
git grep -nE "sk-[a-zA-Z0-9]{20,}|sk-ws-|sb_publishable_" -- . ':!package-lock.json'
```

详见 [`meta/upload-checklist.md`](meta/upload-checklist.md#2--密钥扫描必做)。

### 第 4 步：清理不应上传的产物

```bash
git rm --cached .trellis/scripts/__pycache__/task.cpython-311.pyc
git rm -r --cached node_modules dist releases .netlify/db .netlify/functions-serve 2>$null
git rm -r --cached android/app/build android/build android/.gradle 2>$null
git rm -r --cached android/capacitor-cordova-android-plugins/build 2>$null
git rm --cached android/local.properties 2>$null
```

### 第 5 步：验证后提交推送

```bash
npm ci && npm run lint && npm run build
git add -A
git status --short          # 最后确认没有 .env / 构建产物
git commit -m "docs: 补充 README、开源协议、贡献指南与项目文档"
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
git branch -M main
git push -u origin main
```

---

## 完整的逐步清单

上面是精简版。**包含密钥扫描命令、产物清理、仓库设置、Release 打包、克隆验收**的完整流程见：

👉 [`meta/upload-checklist.md`](meta/upload-checklist.md)

---

## 想知道为什么这么做

- [`meta/migration-guide.md`](meta/migration-guide.md) —— 原仓库的四个问题、做了什么调整、为什么不上传 `node_modules` 等
- [`meta/PATCHES.md`](meta/PATCHES.md) —— 派生文档中修正的链接、仍需手动处理的事项、核实过的数字与出处

---

## 三条重要提醒

### 1. `.env` 里有 3 个真实密钥

```
VITE_SUPABASE_URL / VITE_SUPABASE_ANON_KEY / DEEPSEEK_API_KEY
```

它已在 `.gitignore` 中且**未被跟踪**，但上传前请用上面的扫描命令再验证一次。**仓库一旦公开，密钥在几分钟内就会被爬虫扫到。**

### 2. `.pyc` 文件当前被 git 跟踪

`.trellis/scripts/__pycache__/task.cpython-311.pyc` 已被 git 跟踪（Python 字节码，不应进仓库）。新的 `.gitignore` 已包含 `__pycache__/`，但需要显式 `git rm --cached`（见第 4 步）。

### 3. 源码未做任何修改

本次只做了**文档、协议、元信息与忽略规则**的补充。项目代码一行没动，`npm run build` 的行为与之前完全一致。
