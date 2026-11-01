# 安全策略

## 支持的版本

本项目处于活跃开发阶段，安全修复只针对最新的 `main` 分支。

| 版本 | 支持状态 |
|------|---------|
| `main`（最新） | ✅ 支持 |
| 历史提交 | ❌ 不支持 |

---

## 报告安全漏洞

**请不要通过公开 Issue 报告安全漏洞。**

请使用 GitHub 的私密漏洞报告功能（仓库顶部的 **Security** 标签页 → **Report a vulnerability**），或在 Issue 中只说明「有一个安全问题需要私下沟通」并留下联系方式，维护者会主动联系你。

### 报告时请包含

- 漏洞类型与影响范围
- 复现步骤或 PoC
- 受影响的文件 / 接口 / 版本
- 你评估的严重程度
- 若有，附上修复建议

### 响应时间

- **48 小时内**确认收到
- **7 天内**给出初步评估与处理计划
- 修复发布后会在 Release Notes 中致谢（除非你希望匿名）

---

## 密钥与配置安全

本项目涉及多个第三方密钥，请务必遵守以下约定。

### 密钥存放位置

| 密钥 | 允许位置 | 禁止位置 |
|------|---------|---------|
| `DEEPSEEK_API_KEY` | Netlify 环境变量、本地 `.env` | 前端代码、`VITE_` 前缀变量、仓库 |
| `VITE_SUPABASE_ANON_KEY` | `.env`、Netlify 构建变量 | —— （anon key 设计为可公开，但仍由 RLS 保护） |

### 核心原则

1. **`.env` 永不提交**。已在 `.gitignore` 中，请勿用 `git add -f` 绕过。
2. **服务端密钥不加 `VITE_` 前缀**。带该前缀的变量会被 Vite 内联进前端产物，任何人都能从 `dist/assets/*.js` 里读出来。
3. **`VITE_SUPABASE_ANON_KEY` 不是秘密，但数据安全依赖 RLS**。请确认 Supabase 的 `user_data` 表已启用 Row Level Security 并配置了 `auth.uid() = user_id` 策略。**RLS 未启用等于所有用户数据公开可读写。**
4. **前端不接触任何 LLM 密钥**。所有第三方调用都经 Netlify Functions 代理（`/api/llm`、`/api/recipe`）。

### 如果你不小心提交了密钥

密钥一旦进入 Git 历史，删掉文件是**不够的** —— 它仍在历史提交里。请按顺序执行：

1. **立即到服务商后台吊销并重新生成密钥**（这是唯一真正有效的补救）
2. 更新 Netlify 环境变量与本地 `.env`
3. 如需清理历史，使用 [`git filter-repo`](https://github.com/newren/git-filter-repo) 或 [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)，然后强推并通知所有协作者重新克隆

---

## 已知安全设计说明

以下几点是**有意的设计取舍**，不是漏洞：

### CORS 使用 `Access-Control-Allow-Origin: *`

Netlify Functions 对 `/api/*` 返回通配符 CORS 头。原因是 Capacitor Android WebView 的 origin 是 `https://localhost`，需要在无法预知域名的情况下跨域访问后端。

风险由以下措施对冲：

- 接口不接受任何用户凭证，只接受食材名 / 菜谱候选等业务数据
- 所有第三方密钥仅在服务端读取
- 接口调用会产生 API 费用，因此**若你将本站点用于公开生产环境，建议把 CORS 收敛到明确的域名白名单**

### 菜谱推荐的「白名单校验」

`/api/recipe` 不接受 LLM 自由生成的菜谱，只允许从客户端送来的候选 ID 中挑选。这是防「编造菜谱」的机制性防线，不依赖 prompt 约束。

### 离线模式与数据可见性

未登录时数据仅存于浏览器 `localStorage`，不离开设备。登录后数据同步到 Supabase 的 `user_data` 表，由 RLS 保证用户间隔离。登出会清除客户端对云端数据的引用并回退本地模式。

---

## 依赖安全

```bash
npm audit
npm audit fix
```

建议在升级依赖后回归验证：

- `npm run lint && npm run build` 通过
- 智能录入链路可用（`POST /api/llm` 返回正确的逐项意图与食材）
- DeepSeek 模型名仍然有效（模型可能被下线，且下线时返回 **HTTP 200 + 错误体**而非 4xx）

---

感谢你帮助本项目保持安全。🔒
