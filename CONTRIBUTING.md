# 贡献指南

感谢你对 **智能冰箱厨师（FridgeChef）** 的关注！本文档说明如何参与开发。

---

## 目录

- [行为准则](#行为准则)
- [我能做什么](#我能做什么)
- [开发环境](#开发环境)
- [分支与提交规范](#分支与提交规范)
- [代码规范](#代码规范)
- [Pull Request 流程](#pull-request-流程)
- [特别提醒：几处容易踩的坑](#特别提醒几处容易踩的坑)

---

## 行为准则

参与本项目即表示你同意尊重所有贡献者，保持友善、专业的沟通。人身攻击、歧视性言论与骚扰行为不被接受。

---

## 我能做什么

### 🐛 报告 Bug

提交 Issue 时请尽量包含：

- **复现步骤**（越具体越好）
- **期望行为** 与 **实际行为**
- 运行环境：浏览器 / 设备型号、是否 PWA 安装、是否 Android App
- 是否登录（很多问题只在离线模式或只在云端同步路径上出现）
- 控制台报错、Network 面板中 `/api/*` 的响应体

### 💡 提功能建议

请说明**使用场景**而不是只给方案。这个项目的取舍原则是「库存驱动决策优先」—— 能帮用户少浪费食材、少纠结吃什么的建议最容易落地。

### 📚 补充数据

以下三类数据欢迎直接提 PR：

| 文件 | 内容 | 格式 |
|------|------|------|
| `src/data/recipes.ts` | 菜谱 | 见下方模板 |
| `src/data/foodKnowledge.ts` | 保鲜知识（保存方式 / 保质期） | `名: { method, shelfDays, tips? }` |
| `src/data/ingredientAliases.ts` | 食材别名 | `变体: '标准名'` |

### 🔧 提交代码

修 Bug、补文档、加测试、重构都欢迎。大改动建议先开 Issue 讨论方向，避免白做。

---

## 开发环境

```bash
git clone <your-fork-url>
cd fridge-chef
npm install

# 仅前端：http://localhost:5173
# ⚠️ /api/* 不可用，智能录入与菜谱推荐会降级或失败
npm run dev
```

需要联调智能录入 / 菜谱推荐时：

```bash
npm install -g netlify-cli   # 或用 npx netlify
netlify dev                  # http://localhost:8888，前端 + Functions 一体
```

环境变量见 [`.env.example`](.env.example)。**只做前端功能（冰箱、菜谱浏览、营养、健康）时不需要任何密钥。**

### 验证智能录入链路

`netlify dev` 后打开 <http://localhost:8888/intake-test.html>，这是不依赖登录与库存数据的独立测试页，只跑「文字 → `/api/llm`」，会打印请求体、HTTP 状态、耗时与原始响应。

---

## 分支与提交规范

### 分支命名

```
feat/简短描述        # 新功能
fix/简短描述         # Bug 修复
docs/简短描述        # 文档
refactor/简短描述    # 重构
data/简短描述        # 数据补充（菜谱 / 别名 / 保鲜）
```

### 提交信息

遵循 [Conventional Commits](https://www.conventionalcommits.org/)，**中文描述可以**：

```
feat(recipes): 添加食材模糊匹配功能
fix(intake): 修正消耗意图未提数量时的兜底
refactor(components): 拆分 App.tsx 为独立 Tab 组件
docs(guides): 补充 Android 打包流程
data(recipes): 新增 8 道粤菜
chore: 升级 vite 到 8.1
```

类型：`feat` `fix` `refactor` `style` `docs` `test` `chore` `perf` `data`

尽量一次提交只做一件事，便于审查与回滚。

---

## 代码规范

完整规范见 [`reference/trellis-spec/frontend.md`](reference/trellis-spec/frontend.md) 与 [`reference/trellis-spec/architecture.md`](reference/trellis-spec/architecture.md)。要点：

### 组件

- 单一职责，单个组件**不超过 300 行**（超出则提取子组件）
- 所有 Props 用 `interface` 定义，不用 `any`
- 页面组件 `export default`，工具函数命名导出
- 统一 Tailwind 原子类，**不写行内样式**；颜色用项目 CSS 变量（`bg-surface`、`text-text-muted` 等），不硬编码色值

### TypeScript

- 公共函数标注返回类型
- 对象类型用 `interface`，联合类型用 `type`
- 纯类型导入用 `import type`
- 禁止 `any`（唯一例外：Supabase session）

### 派生数据与状态

- 派生值用 `useMemo`，回调用 `useCallback`
- 跨组件共享走 Props，超过两层再考虑 Context
- 持久化统一走 `useLocalStorage` hook

### 命名与文件

```
src/components/   页面级与可复用 UI 组件
src/utils/        纯函数工具
src/data/         静态数据
src/lib/          外部服务封装
src/hooks/        自定义 Hooks
```

---

## Pull Request 流程

1. Fork 仓库，从 `main` 切出特性分支
2. 完成改动，**跑通全部检查**：

   ```bash
   npm run lint
   npm run build
   npx tsc -p tsconfig.netlify.json   # 若改动 netlify/**
   ```

3. 若改动涉及 UI，附上截图或录屏（移动端 375px 宽度也请确认）
4. 提交 PR，说明**改了什么、为什么改、怎么验证的**
5. 关联相关 Issue（`Closes #123`）

### 自查清单

- [ ] `npm run lint` 无错误
- [ ] `npm run build` 通过
- [ ] 移动端 375px 宽度显示正常
- [ ] 离线模式仍可用（断开网络 / 清空 `.env` 各测一次）
- [ ] 未提交任何真实密钥、`.env`、构建产物
- [ ] 涉及 `netlify/**` 的改动已**手动实测**

---

## 特别提醒：几处容易踩的坑

这些是本项目真实踩过的坑，改动相关代码前请务必先读对应文件的顶部注释。

### 1. `netlify/**` 不在默认类型检查范围内

`tsconfig.app.json` 只包含 `src`，`tsconfig.node.json` 只包含 `vite.config.ts`。`npm run build` 与 `oxlint` **都不会检查** `netlify/functions/*.ts` 和 `netlify/lib/*.ts`。

类型错误只在运行时暴露 —— 改动后必须手动实测，或用 `npx tsc -p tsconfig.netlify.json` 补检。

### 2. 调试接口别用 Git Bash 的 `curl`

Git Bash 的 `curl` 会把中文 body 按 **GBK** 编码送出，服务端收到乱码，LLM 一律返回 `intent: unknown` —— 看起来像后端故障，实际是测试工具的编码问题。**请用 Node 的 `fetch`。**

### 3. 智能录入的三个坑

见 `netlify/functions/llm.ts` 与 `src/components/SmartInputModal.tsx` 顶部注释：

- **意图是逐项的**（`items[].intent`），不是整段一个。一段话里「买了 X、用了 Y、删掉 Z」是常态，不要退回「整段一个 intent」的老写法；顶层 `intent` 只是给旧客户端兜底的主导意图摘要
- **消耗项没提数量时兜底为 `0`**，不是 `1`。猜成 1 会直接扣错库存；`0` 交给确认卡片提示用户填写，并且会让「确认应用」按钮禁用
- **应用时必须用「批内库存工作副本」**。同一库存条目被两行连续引用（连续采购合并 / 连续消耗扣减）时，若每行都从 props 里的原始数量起算，后写会覆盖前写

> 这三条都属于「不报错但结果不对」的类型，改这块之前先读 `evaluateRow` 与 `apply()` 的注释。

### 4. DeepSeek 的两个异常行为

- **JSON mode 有坑**：`deepseek-flash` 在 `response_format: json_object` 模式下会把内容写进 `reasoning_content`，`content` 返回空字符串。所以 `netlify/lib/deepseek.ts` **默认关闭** `jsonMode`，靠 prompt 约束 + 宽松解析容错。
- **无效模型名返回 HTTP 200 + 错误体**，不是 4xx。不检查 `data.error` 会把错误体当正常内容往下传，最终表现为解析出空对象。模型改名时症状极隐蔽。

### 5. Android 权限是一份「隐式清单」（录音权限已删除，坑仍在）

> 当前的「智能录入」是纯文字输入，App 内**不再调用麦克风**，原先为录音声明的 `RECORD_AUDIO` 与 `MODIFY_AUDIO_SETTINGS` 也已从 `AndroidManifest.xml` 删除。这一条留在这里，是因为「native 侧申请 + 清单未声明 + 全部通过才放行」这个陷阱与具体功能无关。

Capacitor 的 `BridgeWebChromeClient.onPermissionRequest` 会**同时**申请 `RECORD_AUDIO` 和 `MODIFY_AUDIO_SETTINGS`，且要求**全部授权**才放行。未声明的权限在 Android 上恒为 DENIED（不会有弹窗），于是录音请求被整体拒绝，报错却伪装成「当前设备不支持录音」。

**将来若重新引入任何需要 native 权限的能力，必须复查**这个方法申请了哪些权限，并与 `AndroidManifest.xml` 同步。

### 6. 食材名归一化必须收口在 `App` 层

写入 `Ingredient.name` 有三条路径：智能录入确认卡、手动添加表单、行内编辑。后两条是纯客户端的。所以归一化（`normalizeIngredientName`）与保质期补全统一放在 `App.addIngredient` / `App.updateIngredient`，**不要放到服务端** —— 否则手输「西红柿」会和菜谱里的「番茄」对不上，产生两套记录。

### 7. 别把密钥加 `VITE_` 前缀

带 `VITE_` 前缀的变量会被内联进前端产物。`DEEPSEEK_API_KEY` **只能**在 Netlify Functions 里通过 `process.env` 读取。

---

再次感谢你的贡献！🎉
