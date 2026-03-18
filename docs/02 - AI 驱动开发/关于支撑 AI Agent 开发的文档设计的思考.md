---
title: "关于支撑 AI Agent 开发的文档设计的思考"
date: 2026-03-18
type: translation
tags: [主题/AI驱动开发, 类型/翻译, 来源/zenn]
source: "https://zenn.dev/ryohei_and_idea/articles/9dc89343258cf6"
original: "[[AIエージェント開発を支えるドキュメント設計について考える]]"
author: siropaca
language: zh
status: done
related:
  - "[[AI Agent 驱动开发的最前沿：从基于指令的开发迈向下一步]]"
  - "[[Claude Code Skills：让 AI 变身专业工匠]]"
---

# 关于支撑 AI Agent 开发的文档设计的思考

> 原文：[AIエージェント開発を支えるドキュメント設計について考える](https://zenn.dev/ryohei_and_idea/articles/9dc89343258cf6)
> 作者：siropaca
> 标签：AI、AI Agent、Claude Code、开发效率化、文档

---

## 前言

在上一篇文章中，我介绍了"Harness Engineering（驱动工程）"的理念，阐述了在 AI Agent 驱动开发的下一阶段中，"设计环境"的重要性。

> 参考：https://zenn.dev/ryohei_and_idea/articles/9610bd5e378074

Harness Engineering 有三大支柱：

- **上下文工程（Context Engineering）**：持续强化仓库内的文档
- **架构约束（Architecture Constraints）**：通过 Linter 和结构测试以机械方式强制保障不变条件
- **垃圾回收（Garbage Collection）**：通过 Agent 定期检测和修复文档不一致及架构约束违规

其中最具即时效果、也最容易上手的就是"**上下文工程**"。
本文将聚焦于此，具体介绍如何设计和组织面向 AI Agent 的文档。

内容涵盖：`AGENTS.md` / `CLAUDE.md` 应该写什么，`docs/` 目录应该放置什么信息，AI 有效利用文档的技巧，以及具体的目录结构示例——力求让读者能够直接应用到自己的项目中。

## 为什么必须完善文档

"文档很重要"这句话虽然经常听到，但真正动手去做却不容易。
然而在 AI 驱动开发中，文档整备的重要性已经不是"有了更好"，而是"**没有就无法正常工作**"的级别。

原因有以下三点。

### AI Agent 的精度由上下文质量决定

AI Agent 仅根据上下文窗口中加载的信息来做判断。
无论模型多么优秀，如果不知道项目特有的设计方针、命名规范和业务规则，就只能靠猜测来编写代码。

"帮我自动处理一下"却得不到期望结果，原因不是 Agent 能力不足，而是**仓库中缺少必要的信息**。
Harness Engineering 的原则是：不要说"再努力一点"，而要问"**缺少了什么信息**"。
当 Agent 的输出质量不高时，应该改善的不是 Prompt，而是**环境（文档）**。

> 参考：https://openai.com/index/harness-engineering/

### 文档的完善程度直接影响 Agent 的规则遵守率

有分析显示，200 行以下的 `CLAUDE.md` 可以达到 **92% 以上**的规则应用率，而超过 400 行时则降至 **71%**。

也就是说，文档的关键不在于"量"，而在于"**结构和简洁性**"。
不应将所有内容塞进一个巨大的文件，而应将信息适当结构化，让 Agent 在需要时只读取需要的部分。
这种思路被称为"**渐进式披露（Progressive Disclosure）**"，后文将详细介绍。

> 参考：https://www.humanlayer.dev/blog/writing-a-good-claude-md

### 为人类写的文档也能成为 AI 的文档

整备文档，本质上是将团队的**隐性知识转化为显性知识**。
这不仅对 AI 有帮助，对人类新成员的入职引导（Onboarding）同样有效。

"缺乏整备文档的动力"是很多团队面临的问题，但 AI 活用这一强大激励可以成为突破这道障碍的原动力。
为 AI 编写了文档，结果整个团队的生产力都提升了——这样的良性循环必然会出现。

## 理解 AGENTS.md 和 CLAUDE.md

作为面向 AI Agent 的指令文件，最具代表性的是 `AGENTS.md` 和 `CLAUDE.md`。
首先来理清这两者的区别和使用方式。

### AGENTS.md 是什么

`AGENTS.md` 是由 Linux Foundation 旗下的 Agentic AI Foundation 管理的开放标准。
OpenAI Codex、GitHub Copilot、Cursor、Gemini CLI、Jules、Amp 等 20 多种工具已支持，被定位为不依赖于特定工具的"**通用语言**"。

将其放置在仓库根目录，支持的工具会自动读取该文件，并作为项目特定指令注入给 Agent。

> 参考：https://agents.md/

### CLAUDE.md 是什么

`CLAUDE.md` 是 Anthropic 的 Claude Code 专用记忆文件。
在会话启动时作为系统提示（System Prompt）自动注入。

Claude Code 提供了以下 6 层记忆层级。
优先级数字越小越优先，除 Managed CLAUDE.md（组织策略）外，更具体的文件会覆盖上层设置。

| 优先级 | 文件 | 用途 | 版本管理 |
| --- | --- | --- | --- |
| 1 | Managed CLAUDE.md | 组织策略（MDM 分发） | 不纳入 |
| 2 | `~/.claude/CLAUDE.md` | 用户个人设置 | 不纳入 |
| 3 | 项目根 `CLAUDE.md` | 团队共享规范 | 纳入 |
| 4 | `CLAUDE.local.md` | 个人的项目特定设置 | 不纳入 |
| 5 | 子目录 `CLAUDE.md` | 模块特定指令 | 纳入 |
| 6 | Auto Memory (`MEMORY.md`) | Claude 自身的会话备忘 | 自动管理 |

重要的是**延迟加载**机制。
启动时只加载根目录的 `CLAUDE.md`，子目录的 `CLAUDE.md` 只有在操作该目录下的文件时才被读取。
这样可以避免浪费上下文窗口。

> 参考：https://claude.com/blog/using-claude-md-files

### 两者并用策略

截至 2026 年 3 月，Claude Code 原生不支持 `AGENTS.md`，因此**两者并用**是最优策略。

推荐的做法是：以 `AGENTS.md` 为主体编写内容，通过 `CLAUDE.md` 引用：

```markdown
# CLAUDE.md
AGENTS.md

<!-- 以下仅记载 Claude Code 特有的设置 -->
```

这样即使团队中有人使用 Claude Code 以外的工具（Codex / Copilot / Cursor 等），也能应用相同的指令。

### 根文件应该写什么内容

根目录的 `AGENTS.md`（或 `CLAUDE.md`）**必须控制在 200 行以下**，这是铁律。
LLM 的系统提示中已经包含了大量指令，能额外添加的指令"预算"非常有限。
根文件应设计为"**路由器（地图）**"而非"全能手册"。

应该写的内容如下：

```markdown
# 项目名称
TypeScript / Node.js 项目。Backend(Express) + Frontend(React/Next.js)。

## 核心命令
- 类型检查 + Lint：`pnpm check`
- 测试执行：`pnpm test --run --no-color`
- 单个测试：`pnpm test --run <path>.test.ts`
- 构建：`pnpm build`

## 项目结构
├─ src/api/       → Express API 服务器
├─ src/frontend/  → React 前端
├─ docs/          → 项目文档

## 必须遵守的规则（Do）
- 所有函数添加类型注解
- 使用 named export（禁止 default export）
- 测试添加与代码修改同步进行

## 禁止事项（Never）
- 不手动编辑 /migrations/ 下的文件
- 不使用 moment.js（使用 date-fns）
- 不使用 any 类型（使用 unknown + 类型守卫）

## 需要确认（Ask First）
- 添加新的 npm 包
- 数据库 Schema 变更

## 文档参考
- 整体架构：参见 docs/architecture/overview.md
- API 设计：参见 docs/api/conventions.md
- 测试策略：参见 docs/testing/strategy.md
- DB 操作：参见 docs/database/schema-overview.md
```

需要注意的是，项目概要、命令、目录结构等信息通常已经写在 `README.md` 中。
AI Agent 也能读取 `README.md`，因此重复的信息不必写在 `AGENTS.md` 中，只需写"参见 README.md"即可节省上下文。
`AGENTS.md` 应专注于 Agent 特有的规则（Do / Never / Ask First）和文档路由。

> 参考：https://devcenter.upsun.com/posts/why-your-readme-matters-more-than-ai-configuration-files/

运营 `AGENTS.md` 时需要注意以下 7 个要点：

1. **通过路径引用而非复制代码示例**（`参见 docs/api/conventions.md`）
2. **不写 Linter 可检查的规则**（以 Linter 配置为准，防止重复管理）
3. **不直接使用 `/init` 的自动生成结果**（生成的 `CLAUDE.md` 需要手动调优）
4. **定期审查**（`wc -l AGENTS.md` 超过 200 行时考虑拆分）
5. **包含压缩指令**（指定上下文压缩时应保留的信息，如"压缩时保留已修改文件列表"）
6. **用 `CLAUDE.local.md` 分离个人设置**（加入 gitignore，不污染团队共享文件）
7. **用 `.claude/rules/*.md` 分离基于路径的规则**（防止根文件膨胀）

关于记述风格，将在后文"面向 AI 的文档编写原则"中统一整理。

## docs/ 目录应该放什么

从代码中可以读出"实现了什么"（What），但无法读出"**为什么这样做**"（Why）。
`docs/` 目录是提升 AI Agent 判断力的**知识库**。

具体来说，应放置以下文档：

- **架构概要**：系统整体的鸟瞰图和约束
- **ADR**：设计决策的记录和背景
- **领域知识**：术语表和业务规则
- **API 规范 / DB Schema / 编码规范**：实现前提的技术约定
- **测试策略**：测试的粒度和边界定义
- **实现模式（patterns）**：Linter 无法检查的设计方针和代码模式
- **功能规格（specs）**：按功能划分的需求和设计

逐一说明。

### 架构概要

`docs/architecture/overview.md` 放置系统整体的鸟瞰图。
Harness Engineering 中有一句话："**不要给一千页的手册，给一张地图**"。

这里重要的是**明确写出"不存在什么"**。

```markdown
## 架构约束
- 微服务之间不存在直接通信。全部通过消息队列
- 不存在前端直接访问数据库的路径
- 认证在 API Gateway 统一处理，各服务不单独处理
```

这种"不存在的事物"的描述能强力约束 Agent 的实现。
如果不写明，Agent 可能会判断"直接通信更高效"而自行采用。

### ADR（Architecture Decision Records，架构决策记录）

ADR 是记录"为什么选择了那项技术"、"为什么采用了那种设计"的文档。

AI Agent 不了解团队过去的讨论。
没有 ADR，就只能猜测"为什么不用 moment.js 而用 date-fns"、"为什么不用 REST 而用 GraphQL"，最坏的情况下会提议过去已经被否决的方案。

```markdown
# ADR-0003：采用基于 URL 路径的 API 版本管理

## Status: Accepted (2025-08-15)

## Context
需要 API 兼容性维护策略。讨论了 Header 方式 vs URL 路径方式两个方案。

## Decision
采用 URL 路径方式（/api/v1/...）。
理由：路由层分离清晰，与 CDN 缓存兼容性好，
调试时可视性高。

## Consequences
- 端点数量增加，但版本间差异可以明确管理
- 旧版本的废弃时间表记录在 docs/api/deprecation-policy.md
```

ADR 一旦编写就不修改。
如果决策发生变化，创建新的 ADR，并将旧 ADR 的状态更新为 `Superseded by ADR-XXXX`。

> 参考：https://adr.github.io/

### 领域知识

AI Agent 最难推测的就是领域知识。
将代码中没有直接写明的隐含业务规则明确记录下来非常重要。

特别有效的是**术语表**。
当同一概念在代码中的表达和业务上的称呼不同时，Agent 会产生混乱。

```markdown
# 领域术语表

| 术语 | 定义 | 代码中的表达 |
| --- | --- | --- |
| 会员 | 付费计划签约者 | `User` where `subscriptionStatus === 'active'` |
| 访客 | 未注册/未登录用户 | `null` session |
| 折扣适用期间 | 签约开始后 12 个月内 | `discountEligibleUntil` field |
```

业务规则和工作流程（用户注册流程、支付处理流程等）也应以自然语言写在 `docs/domain/` 下，这样 Agent 在实现时可以参考这些信息编写准确的逻辑。

### API 规范 / DB Schema / 编码规范

**API 规范**理想情况下用 OpenAPI Schema 管理。
关键是**包含真实的响应示例**。
不要使用 `"string"` 或 `123` 这样的占位符，而是写接近实际数据的示例，这样 AI 的代码生成质量会大幅提升。

**DB Schema** 仅靠迁移文件是不够的。
用自然语言补充说明实体间的关系和约束条件的意图非常重要。
特别是像"soft delete 的记录要从查询中排除"这样的隐含规则，如果不写在文档中，Agent 根本无从得知。

**编码规范**方面，Linter 可以机械检查的规则最好不写在文档中。
以 Linter 配置本身为准，防止重复管理。
文档中应只写 Linter 无法检查的设计方针。
具体的实现模式容易变得很多，建议分离到 `docs/patterns/` 中。

### 实现模式（patterns）

`docs/patterns/` 放置项目特有的实现模式。
对象是 Linter 无法强制执行，但团队希望统一的写法和架构模式。

```markdown
# 错误处理模式

## API 响应错误
- 业务逻辑错误使用 `AppError` 类，明确 HTTP 状态码和错误码
- 外部服务（Stripe、SendGrid 等）的错误用 `ExternalServiceError` 包装
- 验证错误不直接返回 Zod 错误，转换为 `ValidationError`

## 日志
- 错误日志必须包含请求 ID
- 不将外部服务的响应 Body 记录到日志（个人信息风险）
```

当模式增多时，按领域拆分（`api-patterns.md`、`db-patterns.md` 等）文件。

### 测试策略

在 `docs/testing/strategy.md` 中描述 Unit / Integration / E2E 的边界定义和测试模式。
Agent 在测试套件充实的环境中**性能会大幅提升**。
测试策略如果被明确记录，Agent 就能编写适当粒度的测试。

### 功能规格（specs）

在 `docs/specs/` 中放置按功能划分的需求和设计。
这种方法被称为 Spec-Driven Development（SDD，规格驱动开发），GitHub 的 [spec-kit](https://github.com/github/spec-kit) 和 AWS 的 [Kiro](https://kiro.dev/docs/specs/) 采用了这一理念。

与 `docs/` 其他目录处理系统横向共享知识不同，`specs/` 聚焦于**单个功能**。
在向 Agent 发出功能添加或修改指令时，引导其参照对应的 spec，可以大幅提升实现精度。

```markdown
# 支付功能

## 需求
- 支持信用卡、银行转账、便利店支付
- 支付完成后发送邮件通知
- 未支付状态持续 24 小时的订单自动取消

## 设计
- 支付处理委托给外部服务（Stripe）
- 通过 Webhook 接收支付结果，更新 orders 表的状态
- 自动取消通过 cron 作业执行（每小时第 0 分钟）
```

如果包含重要的设计决策，将其背景也记录为 ADR 放在 `docs/adr/` 中，可以避免知识分散。

> 参考：https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

## 让 AI Agent 有效利用文档的技巧

文档不仅要注重"内容"，在"**结构**"和"**传递方式**"上也需要下功夫。

### 渐进式披露（3 跳导航）

将所有文档加载到上下文中是不可能的。
解决方案是设计一个**3 跳以内即可到达目标文档的导航结构**。

```
AGENTS.md（路由器）
  → docs/INDEX.md（文档地图）
    → docs/adr/INDEX.md（ADR 索引）
      → docs/adr/records/adr-0003-api-versioning.md（具体文档）
```

在所有 `docs/` 子目录中放置 `INDEX.md`，让 AI 能首先读取该目录的"地图"。

```markdown
# 文档地图

操作时先阅读相关目录的 INDEX.md，
仅参考所需文件。

| 目录 | 内容 | 何时参考 |
| --- | --- | --- |
| architecture/ | 系统整体设计 | 新功能设计、理解结构时 |
| adr/ | 架构决策记录 | 想了解设计判断背景时 |
| api/ | API 规范、规约、错误码 | 添加/修改端点时 |
| database/ | Schema 设计、迁移步骤 | DB 操作时 |
| domain/ | 领域术语表、业务规则 | 实现业务逻辑时 |
| domain-models/ | 领域模型的行为和约束 | 实现/修改模型时 |
| patterns/ | 实现模式、设计方针 | 编写代码时 |
| specs/ | 按功能划分的需求/设计 | 添加/修改功能时 |
| testing/ | 测试策略、模式 | 添加/修改测试时 |
| standards/ | 编码规范、Git 运营 | 编写代码时全般 |
```

这里重要的是，使用**路径引用**（"参见 `docs/file.md`"）而非 `@docs/file.md` 这样的内联展开。
后者会每次将文件全文展开到上下文中，而前者是 Agent 在需要时才去读取的延迟加载，可节省上下文窗口。

### .claude/ 目录的活用

这是面向 Claude Code 用户的内容，`.claude/` 目录是防止 `AGENTS.md` 膨胀的强大"分流目标"。

| 目录 | 用途 | 加载时机 |
| --- | --- | --- |
| `.claude/rules/*.md` | 基于路径的条件规则 | 仅在操作匹配路径的文件时 |
| `.claude/skills/*/SKILL.md` | 参考知识（操作手册、模式集） | Agent 判断需要时 |
| `.claude/commands/*.md` | 自定义斜杠命令 | 用户执行命令时 |

特别强大的是 `.claude/rules/`。
在 YAML frontmatter 中指定路径模式，只有在操作该路径的文件时才会加载规则。

```markdown
# .claude/rules/database.md
---
paths:
  - "**/migrations/**"
  - "**/schema/**"
---

- 迁移文件必须通过 `bun run db:migrate:create` 生成
- 不手动编辑迁移文件
- 列删除分两步执行（1. 代码端停止使用 → 2. 下次发布时删除）
```

用基于路径的分离代替将所有规则塞进 `AGENTS.md`，可以轻松将其维持在 200 行以下。

### 面向 AI 的文档编写原则

这是包括 `AGENTS.md` 和 `docs/` 在内、AI 阅读的所有文档通用的编写原则。
AI 以 chunk 为单位处理文档，因此**每个章节必须能够独立理解**。

1. **使用祈使句和列表简洁书写**：避免长段落，使结构易于扫描
2. **将最重要的规则放在开头**：LLM 对开头和结尾的指令有偏向性
3. **禁止事项必须附带替代方案**：写成"不要用 X。用 Y"的形式
4. **使每个章节自成一体**：消除对其他章节的隐含依赖
5. **将相关信息集中放置**：不要将同一概念的信息分散在多个章节
6. **标题中包含具体关键字**：用"PostgreSQL 连接设置"而非"设置"
7. **消除隐含知识**：明确记述前提条件和前提知识
8. **使用一致的术语**：避免"用户名"和"登录ID"这样同义词的混用
9. **为图表提供文字替代**：Mermaid 图也要附带自然语言说明
10. **优先使用代码示例**：一个具体的代码片段比三段文字说明更有效

> 参考：
> - https://docs.kapa.ai/improving/writing-best-practices
> - https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/

### 维护文档新鲜度的机制

文档不是写完就结束，需要和代码一起持续更新。

CI 中的文档更新检查首先是有效的。
可以设置 GitHub Actions，在代码变更时如果相关 `docs/` 未更新就发出 Warning。

另外，在 `AGENTS.md` 中注明"**影响架构的变更必须创建 ADR**"，Agent 自身就会提示更新文档。

每月检查 `AGENTS.md` 的行数（`wc -l AGENTS.md`），超过 200 行时考虑向 `.claude/rules/` 或 `docs/` 拆分，这也是一个好做法。

## 具体目录结构示例

基于以上内容，介绍两种具体的目录结构。

### 单仓库（Single Repo）的情况

```shell
my-project/
├── AGENTS.md                    # 主路由器（200行以下）
├── CLAUDE.md                    # 引用 AGENTS.md + Claude 特有设置
│
├── .claude/
│   ├── settings.json            # 项目权限设置
│   ├── rules/
│   │   ├── api.md               # paths: ["src/api/**"]
│   │   ├── database.md          # paths: ["**/migrations/**"]
│   │   └── testing.md           # paths: ["**/*.test.ts"]
│   ├── skills/
│   │   └── api-design/SKILL.md  # API 设计模式参考知识
│   └── commands/
│       └── generate-adr.md      # /generate-adr 命令
│
├── docs/
│   ├── INDEX.md                 # 文档地图
│   ├── architecture/
│   │   └── overview.md          # 系统整体鸟瞰图
│   ├── adr/
│   │   ├── INDEX.md             # ADR 索引
│   │   ├── TEMPLATE.md          # ADR 模板
│   │   └── records/
│   │       ├── adr-0001-xxx.md
│   │       └── ...
│   ├── api/
│   │   ├── conventions.md       # 端点命名、响应格式
│   │   └── error-codes.md       # 错误码一览
│   ├── database/
│   │   ├── schema-overview.md   # ER 关系的自然语言说明
│   │   └── migration-guide.md   # 迁移步骤
│   ├── domain/
│   │   ├── glossary.md          # 领域术语表
│   │   └── business-rules.md    # 业务规则
│   ├── domain-models/
│   │   ├── INDEX.md             # 领域模型一览
│   │   ├── order.md             # 订单模型的行为和约束
│   │   ├── user.md              # 用户模型的行为和约束
│   │   └── ...
│   ├── patterns/
│   │   ├── error-handling.md    # 错误处理模式
│   │   └── ...
│   ├── testing/
│   │   └── strategy.md          # 测试策略
│   ├── specs/
│   │   ├── payment.md           # 支付功能的需求和设计
│   │   ├── notification.md      # 通知功能的需求和设计
│   │   └── ...
│   └── standards/
│       ├── code-style.md        # 编码规范
│       └── git-workflow.md      # 分支策略、提交规范
│
├── src/
├── package.json
└── ...
```

单仓库结构相对扁平，因此比较简单。
要点如下：

- `AGENTS.md` → `docs/INDEX.md` → 具体文档的 3 跳导航
- 通过 `.claude/rules/` 按文件路径分离条件规则，保持 `AGENTS.md` 轻量
- 即使是小型项目也提前结构化 `docs/`，方便后续扩展

### Monorepo 的情况

```shell
my-monorepo/
├── AGENTS.md                    # 主路由器（200行以下）
├── CLAUDE.md                    # 引用 AGENTS.md + Claude 特有设置
│
├── .claude/
│   ├── settings.json
│   ├── rules/
│   │   ├── frontend-react.md    # paths: ["apps/frontend/**/*.tsx"]
│   │   ├── backend-api.md       # paths: ["apps/backend/src/**"]
│   │   ├── database.md          # paths: ["**/migrations/**"]
│   │   └── testing.md           # paths: ["**/*.test.ts"]
│   └── ...
│
├── docs/                        # 横向/共享知识
│   ├── INDEX.md
│   ├── architecture/
│   │   └── overview.md          # 公共基础 + 系统整体鸟瞰图
│   ├── adr/
│   │   ├── INDEX.md             # 系统横向 ADR 索引
│   │   ├── TEMPLATE.md          # ADR 模板
│   │   └── records/
│   │       ├── adr-0001-monorepo-adoption.md
│   │       └── ...
│   ├── api/
│   ├── database/
│   ├── domain/
│   ├── patterns/                # 实现模式、设计方针
│   ├── specs/                   # 按功能划分的需求/设计
│   ├── testing/
│   └── standards/
│
├── apps/
│   ├── frontend/
│   │   ├── AGENTS.md            # 前端特有指令
│   │   └── docs/
│   │       ├── INDEX.md
│   │       ├── tech-stack.md    # Next.js, React, Tailwind 等
│   │       ├── components.md    # 组件设计模式
│   │       └── adr/
│   │           ├── INDEX.md     # Frontend 特有 ADR（adr-f001~）
│   │           ├── TEMPLATE.md
│   │           └── records/
│   │               └── ...
│   └── backend/
│       ├── AGENTS.md            # 后端特有指令
│       └── docs/
│           ├── INDEX.md
│           ├── tech-stack.md    # Express, Prisma, Redis 等
│           ├── endpoints.md     # 端点一览
│           ├── domain-models/
│           │   ├── INDEX.md     # 领域模型一览
│           │   ├── order.md     # 订单模型的行为和约束
│           │   ├── user.md      # 用户模型的行为和约束
│           │   └── ...
│           └── adr/
│               ├── INDEX.md     # Backend 特有 ADR（adr-b001~）
│               ├── TEMPLATE.md
│               └── records/
│                   └── ...
│
├── packages/
│   └── shared/
│       ├── AGENTS.md
│       └── docs/
│
└── ...
```

在 Monorepo 中，"**将什么信息放在哪里**"的判断至关重要。
原则很明确：

- **横向/共享知识放在根 `docs/`**：架构、ADR、API 规约、DB 设计、领域知识
- **组件特有知识放在 `apps/*/docs/`**：框架特有的模式、技术栈

根目录的 `AGENTS.md` 作为基于任务的路由器设计，引导到具体文档。

```markdown
## 按任务的文档路由
- 前端组件创建 → 参见 apps/frontend/AGENTS.md
- 后端 API 端点添加 → 参见 apps/backend/AGENTS.md
- 共享包修改 → 参见 packages/shared/AGENTS.md
- 数据库 Schema 变更 → 参见 docs/database/migration-guide.md
```

Monorepo 中的反模式是：**在各包中大量放置详细的 `CLAUDE.md` 会导致维护崩溃**。
根路由器文件 + `.claude/rules/` 的路径规则分离方式更易管理，也更现实。

> 参考：
> - https://engineering.mercari.com/en/blog/entry/20251030-taming-agents-in-the-mercari-web-monorepo/
> - https://dev.to/datadog-frontend-dev/steering-ai-agents-in-monorepos-with-agentsmd-13g0

## 结语

在 AI 驱动开发中，文档不再是代码的附属品，而是**最具杠杆效应的产出物**。
Agent 看不到的知识等于不存在。
反过来说，只要整备好文档，Agent 的输出质量就会发生翻天覆地的变化。

最后，总结一下本文介绍的要点：

- 根指令文件设计为"**路由器（地图）**"而非"全能手册"，保持在 200 行以下
- `docs/` 中集中代码无法读取的"**Why**"（架构概要、ADR、领域知识、实现模式等）
- `AGENTS.md` 和 `CLAUDE.md` 并用，分离工具无关的通用指令和特有设置
- 通过 3 跳导航和路径规则，实现仅延迟加载所需信息
- 内置文档鲜度维护机制（CI 检查、定期审查）

需要指出的是，在研究过程中我感到，这个领域尚未形成确立的最佳实践。
本文的内容也只是当前时点的一种范式，希望大家能结合自己的项目进行尝试和探索。

## 参考链接

- https://openai.com/index/harness-engineering/
- https://agents.md/
- https://claude.com/blog/using-claude-md-files
- https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/
- https://www.humanlayer.dev/blog/writing-a-good-claude-md
- https://docs.kapa.ai/improving/writing-best-practices
- https://adr.github.io/
- https://engineering.mercari.com/en/blog/entry/20251030-taming-agents-in-the-mercari-web-monorepo/
- https://dev.to/datadog-frontend-dev/steering-ai-agents-in-monorepos-with-agentsmd-13g0
- https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- https://code.claude.com/docs/en/memory
