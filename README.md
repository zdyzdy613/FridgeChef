# 🧊 智能冰箱厨师 · FridgeChef

> **把冰箱里的食材，变成今晚的一桌菜。**
> 一个会「看库存、记保质期、推菜谱、算营养、管健康」的中文智能冰箱管家 —— 支持一句话智能录入，能装成 PWA，也能打包成 Android App。

---

## 📖 简介

**FridgeChef（智能冰箱厨师）** 是一个面向中文家庭场景的冰箱管理与饮食助手。它把「冰箱里有什么」这件事结构化下来，再围绕这份库存串起菜谱推荐、营养分析、购物清单和健康目标管理。

与常见的菜谱 App 不同，它的核心不是「菜谱库有多大」，而是**库存驱动的决策**：

- 冰箱里现有 **160 道** 家常菜谱，每道都带食材、步骤与营养数据
- 录入食材时自动补全**保存方式与保质期**（内置 199 条食材保鲜知识），临期食材会在菜谱推荐中被**优先消耗**
- 支持**一句话智能录入**：「今天买了三根黄瓜两盒牛奶还有一斤半排骨」，自动逐项识别意图（采购 / 消耗 / 删除）并抽取结构化食材
- 没有后端也能用：未登录时数据存在浏览器本地，登录后自动同步到 Supabase

> 项目起源于一个很具体的问题：散装蔬菜、肉类没有条形码，手动录入又慢又烦。所以「一句话录入」和「临期优先」是这套系统的第一等公民。

---

## ✨ 功能特性

### 🧊 冰箱管理
- 按 **9 大分类**（蔬菜 / 肉类 / 海鲜 / 豆制品 / 蛋奶 / 主食 / 水果 / 调料 / 其他）分组展示库存
- 添加、编辑、删除食材；数量支持 `+ / −` 快捷增减（自动识别单位步长，如 `500g` 按 50g 递增）
- **保质期与临期提醒**：可选到期日 + 提前 1/2/3/5/7 天提醒，临期与过期食材高亮标注
- **食材名自动归一**：内置 211 条别名词表，「西红柿 / 蕃茄 / 洋柿子」统一收敛为「番茄」，保证与菜谱对得上
- **保存知识库**：新增食材时按名自动带出保存方式（冷藏 / 冷冻 / 常温 / 阴凉干燥）与建议保质期

### ✍️ 智能录入（LLM 逐项意图抽取）
- 冰箱页右上角点「✍️ 智能录入」，在文本框里写下冰箱里的变化 —— 可以直接打字，也可以点输入法键盘上的 🎤 用**自带语音听写**把文字说进去
- 一次调用同时完成**逐项意图识别**与**实体抽取**，一段话里可以混着说：
  - `purchase` 采购入库 —— 「买了两斤猪肉和三个西红柿」
  - `consume` 消耗扣减 —— 「用了两个鸡蛋」「猪肉切了一半」
  - `delete` 删除 —— 「把牛奶删掉」
- 数量与单位智能归一：范围取中值（「两三个」→ 2.5），相对量（「一半」→ 比例 0.5），重量单位互转（斤 / 两 / 克 / kg）
- 没提数量也有兜底：采购默认 `1`，消耗给 `0` 并在确认卡片上提示你补填
- **确认卡片二次校对**：分析结果先预览，逐行可改意图 / 名称 / 数量 / 单位 / 分类 / 保质期 / 要扣减或删除的库存条目，点「确认应用」才真正写库
- 每一行都给出**效果预览**（并入 / 新增 / 扣减到多少 / 删除哪一条）；只要有一行还不能应用，「确认应用」按钮就是禁用的
- 采购命中同名同单位库存时并入既有条目，且**只补空缺的保质期，绝不覆盖你已设的值**

### 🍳 菜谱推荐
- 160 道菜谱，支持菜名 / 食材 / 标签 / 描述多字段搜索与 9 类标签筛选
- **模糊匹配算法**：精确匹配 > 去括号匹配 > 包含匹配 > 去量词匹配，逐条计算匹配度
- 排序优先级：**临期食材覆盖数 → 加权匹配分 → 匹配食材总数**
- **「今晚吃什么」智能推荐**：把本地已排好序的候选交给 DeepSeek 挑 3 道并给出理由（匹配了哪些、还缺什么）
- **服务端白名单校验**：LLM 返回的菜谱 ID 必须落在客户端送来的候选列表内，从机制上杜绝「编造菜谱」
- **离线自动降级**：接口不可用时无缝回退到本地匹配结果，不会停在转圈或报错态
- 菜谱详情弹窗展示所需食材（标注 ✓ 已有 / ✗ 缺少）、分步做法与完整营养数据

### 💪 健康管理
- 健康档案：身高 / 体重 / 年龄 / 性别 / 目标（减脂·增肌·维持·健康）/ 活动量 / 饮食忌口
- 基于 **Mifflin-St Jeor** 公式计算基础代谢（BMR）、每日消耗（TDEE）与目标摄入热量
- 训练计划：训练类型 + 按周选择训练日，首页实时显示「今天是训练日 / 休息日」
- **心情记录**：每天一条（重复记录自动覆盖），6 种心情 + 一句话备注 + 「想吃的东西」，支持历史回看

### 🥗 营养分析 & 🛒 购物清单
- 按食材分类估算总热量、蛋白质、碳水、脂肪、膳食纤维与维生素 / 矿物质覆盖
- 展示 9 类食材的种类覆盖情况，直观看出饮食结构缺口
- 基于营养缺口生成购物建议，按 **优先 / 建议 / 可选** 三档标注推荐理由，可勾选完成

### 📱 多端与离线
- **PWA**：Service Worker 预缓存静态资源，可添加到手机主屏离线使用
- **Android App**：Capacitor 打包，复用同一套 Web 产物
- **账户同步**：Supabase 邮箱注册 / 登录 + Row Level Security；未登录可「跳过登录，离线使用」
- **优雅降级**：Supabase 未配置 / 不可用时自动切到离线模式，本地功能完整可用


### 智能录入链路

```
在冰箱页点「✍️ 智能录入」→ 文本框（可直接打字，或用输入法自带的语音听写）
        → POST /api/llm  { text }
        → DeepSeek 逐项意图 + 实体抽取 (JSON)
        → 确认卡片（逐行可编辑 / 可删行）
        → 确认应用
        → 写库（归一化 + 保质期补全）
```

### 数据流

```
用户操作 → React state → localStorage（立即写入）
                      → Supabase（防抖 1s upsert）
```

> 未登录：仅 localStorage。登录后：拉取云端覆盖本地，之后双写；登出回退本地模式。同步策略为「最后写入胜出」。

---

## 🧰 技术栈

| 分类 | 技术 | 说明 |
|------|------|------|
| UI | React 19 | 函数组件 + Hooks，无路由（Tab state 切换） |
| 语言 | TypeScript 6 | `strict` 风格，禁止 `any`（Supabase session 例外） |
| 构建 | Vite 8 | HMR + 快速生产构建 |
| 样式 | Tailwind CSS 4 | 原子类 + 项目 CSS 变量主题（主色 `#10b981`） |
| 离线 | vite-plugin-pwa 1 | Workbox 预缓存 + Web Manifest |
| 移动端 | Capacitor 8 | Android WebView 封装 |
| 后端服务 | Supabase 2 | 邮箱认证 + `user_data` 表 + RLS |
| LLM | DeepSeek | `deepseek-flash`，负责逐项意图抽取与菜谱推荐 |
| Serverless | Netlify Functions | esbuild 打包 Node 函数 |
| 代码检查 | Oxlint 1 | React / TypeScript / Oxc 规则 |

---

## 📁 项目结构

```
fridge-chef/
├── src/
│   ├── App.tsx                    # 主应用：认证门禁 + Tab 路由 + 全局状态 + 匹配/营养计算
│   ├── main.tsx                   # React 挂载入口
│   ├── index.css                  # 全局样式 + Tailwind 主题变量
│   ├── types.ts                   # 全局类型（Ingredient / Recipe / HealthProfile / MoodEntry）
│   ├── env.d.ts                   # 构建期注入的全局常量类型
│   ├── components/
│   │   ├── Auth.tsx               # 登录 / 注册 / 跳过登录
│   │   ├── FridgeTab.tsx          # 冰箱库存管理
│   │   ├── RecipesTab.tsx         # 菜谱搜索 + 推荐
│   │   ├── TonightSuggest.tsx     # 今晚吃什么（LLM 推荐，含离线降级）
│   │   ├── RecipeModal.tsx        # 菜谱详情弹窗
│   │   ├── SmartInputModal.tsx    # 智能录入 + 确认卡片
│   │   ├── HealthTab.tsx          # 健康档案 + 训练计划 + 心情记录
│   │   ├── NutritionTab.tsx       # 营养分析
│   │   └── ShoppingTab.tsx        # 购物清单
│   ├── data/
│   │   ├── recipes.ts             # 菜谱数据库（160 道）
│   │   ├── foodKnowledge.ts       # 保鲜知识库（199 条）+ 归一化/保质期助手
│   │   └── ingredientAliases.ts   # 食材别名词表（211 条）
│   ├── hooks/
│   │   └── useLocalStorage.ts     # 本地持久化 Hook
│   ├── lib/
│   │   └── supabase.ts            # Supabase 客户端（含离线降级代理）
│   └── utils/
│       ├── apiBase.ts             # API 入口 + JSON POST 助手
│       ├── matchUtils.ts          # 食材名称模糊匹配
│       ├── quantityUtils.ts       # 数量字符串解析与增减
│       ├── stockQuantity.ts       # 数量归一 + 库存扣减
│       ├── intakeApi.ts           # 智能录入接口客户端
│       └── recipeApi.ts           # 菜谱推荐接口客户端
├── netlify/
│   ├── functions/
│   │   ├── llm.ts                 # 逐项意图 + 实体抽取
│   │   └── recipe.ts              # 菜谱推荐排序
│   └── lib/
│       └── deepseek.ts            # DeepSeek 调用 + CORS 响应助手
├── public/                        # PWA 图标、favicon、智能录入测试页
├── assets/                        # Capacitor 图标源图
├── scripts/
│   └── gen_icons.py               # 生成 Android 图标
├── android/                       # Capacitor Android 工程
├── .trellis/spec/                 # 架构 / 前端 / 开发规范与 ADR
├── netlify.toml                   # Netlify 站点配置
├── capacitor.config.ts            # Capacitor 配置
├── vite.config.ts                 # Vite + PWA 配置
└── package.json
```

---

## 🚀 快速开始

### 环境要求

- **Node.js** 20+
- **npm** 10+
- 可选：[Netlify CLI](https://docs.netlify.com/cli/get-started/)（需要联调智能录入 / 菜谱推荐时）
- 可选：JDK 17+ 与 Android SDK（需要打包 APK 时）

### 安装与运行

```bash
git clone <your-repo-url>
cd fridge-chef
npm install

# 仅前端（http://localhost:5173）
# ⚠️ /api/* 不可用 —— 智能录入与菜谱推荐会降级或失败
npm run dev
```

需要联调后端接口时，用 Netlify CLI 同时起前端 + Functions：

```bash
netlify dev
# http://localhost:8888，/api/* 由 netlify.toml 的 redirects 转发到 netlify/functions/*
```

> **建议**：只想体验冰箱管理、菜谱浏览、营养分析、健康记录，用 `npm run dev` 就够了，无需任何密钥。

### 单独验证智能录入链路

启动 `netlify dev` 后访问 **<http://localhost:8888/intake-test.html>**。

这个独立测试页不依赖登录与库存数据，只跑「文字 → `/api/llm` 意图抽取」一段链路，并打印请求体、HTTP 状态、耗时与原始响应，是排查解析问题最快的入口。也可用 `?api=https://<site>.netlify.app` 覆盖接口地址做跨域调试。

---

## ⚙️ 环境变量

复制 `.env.example` 为 `.env` 并填写。

```bash
cp .env.example .env
```

| 变量 | 作用域 | 是否必需 | 用途 |
|------|--------|---------|------|
| `VITE_SUPABASE_URL` | 构建期（前端） | 可选 | Supabase 项目 URL，不填则走纯离线模式 |
| `VITE_SUPABASE_ANON_KEY` | 构建期（前端） | 可选 | Supabase anon key |
| `DEEPSEEK_API_KEY` | 服务端 Functions | 智能录入 / 推荐需要 | DeepSeek API Key |
| `DEEPSEEK_MODEL` | 服务端 Functions | 可选 | 覆盖默认模型名（默认 `deepseek-flash`） |
| `VITE_API_BASE` | 构建期（前端） | 仅 Android | 打包 App 时注入的后端绝对地址 |

> ⚠️ **安全**：只有 `VITE_` 前缀的变量会被内联进前端产物，因此**不要**给服务端密钥加 `VITE_` 前缀。`.env` 已在 `.gitignore` 中，切勿提交真实密钥。
>
> 注意 Vite 的 `VITE_API_BASE` 取自 `process.env`，必须用环境变量前缀方式传入，`vite build --define VITE_API_BASE=...` 是无效用法（`--define` 不是 Vite CLI 参数）。

---

## 🗄️ 数据库初始化

不想用云同步可以跳过本节，应用会自动运行在离线模式。

### 1. 创建 Supabase 项目

访问 [supabase.com](https://supabase.com) 新建项目，在 **Project Settings → API** 找到 URL 与 anon key，填入 `.env`。

### 2. 建表

在 SQL Editor 中执行：

```sql
CREATE TABLE user_data (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  ingredients JSONB DEFAULT '[]'::jsonb,
  health_profile JSONB DEFAULT '{}'::jsonb,
  mood_data JSONB DEFAULT '[]'::jsonb,
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE INDEX idx_user_data_user_id ON user_data(user_id);

-- 行级安全：每个用户只能访问自己的数据
ALTER TABLE user_data ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can only see their own data"
  ON user_data
  FOR ALL
  USING (auth.uid() = user_id);

-- 自动维护 updated_at
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = now();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER user_data_updated_at
  BEFORE UPDATE ON user_data
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at();
```

> `upsert` 依赖 `user_id` 上的唯一约束，请确认已建索引 / 唯一键。

---

## 📦 构建与部署

### 生产构建

```bash
npm run build      # tsc -b && vite build → dist/
npm run preview    # 本地预览构建产物
npm run lint       # Oxlint 检查
```

### 部署到 Netlify

`netlify.toml` 已配好构建命令、发布目录与 `/api/*` 重写规则。首次部署需在 Netlify 侧配置环境变量：

```bash
netlify env:set DEEPSEEK_API_KEY "<值>"
netlify env:set VITE_SUPABASE_URL "<值>"
netlify env:set VITE_SUPABASE_ANON_KEY "<值>"

netlify deploy --prod --build
```

部署后冒烟一次 `POST /api/llm` 即可确认 Functions 与密钥都到位。

### 打包 Android APK

App 内的页面 origin 是 `https://localhost`，**相对路径 `/api` 会打到 WebView 自己身上**，所以构建时必须注入线上后端地址：

```bash
# 1) 构建前端并注入后端地址
VITE_API_BASE=https://<your-site>.netlify.app npx vite build

# 2) 校验注入生效（应输出命中的域名）
grep -ro "<your-site>" dist/assets/*.js | head -1

# 3) 同步 web 产物到 Android 工程，然后打包
npx cap sync android
cd android && ./gradlew assembleDebug --no-daemon && cd ..
```

产物位于 `android/app/build/outputs/apk/debug/app-debug.apk`。

```bash
adb install -r android/app/build/outputs/apk/debug/app-debug.apk
```

> **先部署后端再打包**：APK 指向的后端没上线时，App 能打开但所有 `/api/*` 调用都会失败。

---

## 🧪 质量检查

```bash
npm run lint                      # Oxlint：React / TS / Oxc 规则
npm run build                     # TypeScript 全量类型检查 + 生产构建
npx tsc -p tsconfig.netlify.json  # 补检 netlify/**（见下方「已知局限」）
```

提交前建议跑完整流程：

```bash
npm run lint && npm run build
```

### 已知局限

- **`netlify/**` 不在默认类型检查范围内**。`tsconfig.app.json` 只包含 `src`，`tsconfig.node.json` 只包含 `vite.config.ts`，因此 Netlify Functions 的类型错误只在运行时暴露。改动这些文件后必须手动实测，或用上面的 `tsconfig.netlify.json` 补检。
- **`netlify/functions/*.ts` 里的注释是排查记录**，记录了逐项意图的设计取舍与 DeepSeek 的返回体异常行为。改动前请先读注释，不要顺手删除 —— 每一条都对应一次真实故障。
- **数据同步是「最后写入胜出」**，没有做冲突合并；多设备同时编辑可能互相覆盖。
- **营养分析是基于食材分类的粗略估算**（按克重 × 分类系数），用于趋势参考，不作为医学或营养学建议。
- **单组件 300 行上限**：`SmartInputModal.tsx` 等文件已明显超过该约定，后续拆分时请留意。

---

## 🤝 贡献

欢迎 Issue 与 PR。开始之前请先阅读 [CONTRIBUTING.md](CONTRIBUTING.md)。

提交信息遵循 [Conventional Commits](https://www.conventionalcommits.org/)：

```
feat(recipes): 添加食材模糊匹配功能
fix(auth): 修复登录后数据不同步的问题
refactor(components): 拆分 App.tsx 为独立 Tab 组件
docs: 更新部署说明
```

---

## 📄 开源协议

本项目基于 [MIT License](LICENSE) 开源。

---

## 🙏 致谢

- [React](https://react.dev) · [Vite](https://vite.dev) · [Tailwind CSS](https://tailwindcss.com)
- [Supabase](https://supabase.com) 提供认证与数据存储
- [DeepSeek](https://api-docs.deepseek.com) 提供智能录入的逐项意图抽取与菜谱推荐
- [Netlify](https://www.netlify.com) 提供托管与 Serverless Functions
- [Capacitor](https://capacitorjs.com) 提供 Android 打包能力
