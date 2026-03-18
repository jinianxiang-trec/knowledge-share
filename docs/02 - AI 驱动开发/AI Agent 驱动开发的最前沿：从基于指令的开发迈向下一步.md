---
title: "AI Agent 驱动开发的最前沿：从基于指令的开发迈向下一步"
date: 2026-03-18
type: translation
tags: [主题/AI驱动开发, 类型/翻译, 来源/zenn]
source: "https://zenn.dev/ryohei_and_idea/articles/9610bd5e378074"
original: "[[AI エージェント駆動開発の最前線と指示ベースの開発から次に進むために]]"
author: siropaca
language: zh
status: done
related:
  - "[[关于支撑 AI Agent 开发的文档设计的思考]]"
  - "[[Claude Code Skills：让 AI 变身专业工匠]]"
  - "[[SDD 规格驱动开发]]"
---

# AI Agent 驱动开发的最前沿：从基于指令的开发迈向下一步

> 原文：[AI エージェント駆動開発の最前線と指示ベースの開発から次に進むために](https://zenn.dev/ryohei_and_idea/articles/9610bd5e378074)
> 作者：siropaca
> 标签：AI、AI Agent、开发效率化、Claude Code、Cursor

---

## 前言

最近，让 AI Agent 编写源代码进行开发的方式已经变得相当普遍了。大家的情况如何呢？

我也已经习惯了用自然语言向 AI Agent 下达指令来编写源代码，自己亲手写代码的机会越来越少了。
但最近读了一些文章后，我开始觉得，向 Agent 下达指令进行开发的方式，也就是"不再自己写源代码"这件事，或许只是站在了起跑线上而已。

为了迈向下一步需要做什么？世界上领先的科技企业在做什么？出于好奇，我做了一些调研和整理。

## 世界已经走到了哪里

调研发现，全球的软件开发正在从"人类编写代码的时代"向"**人类指挥 AI Agent 的时代**"迅速转变。

- Google 的新代码中约 **50%** 由 AI 生成
- Spotify 的资深工程师自 2025 年 12 月以来**一行代码都没写过**
- OpenAI 的一位工程师同时操控 **10～20 个并行 Agent**

这已经不再是少数领先企业的实验，而堪称产业结构的转折点。

OpenAI API 平台的工程负责人在 [Lenny's Podcast](https://www.lennysnewsletter.com/p/engineers-are-becoming-sorcerers) 中说："工程师正在变成魔术师（Sorcerers）"。
就像电影《魔法师的学徒》一样，指挥 AI Agent 这把"魔法扫帚"来工作，是工程师的新面貌。
他还说："未来 12～24 个月是在工程师角色完全改变之前能够先发制人的宝贵时期。"

AI 编码工具市场在 2025 年已达 73.7 亿美元规模，预计到 2032 年将增长至 301 亿美元。
GitHub Copilot 拥有超过 2,000 万用户，Cursor 成立仅 2 年就实现了 ARR 超过 10 亿美元，创下 SaaS 史上最快增长纪录。
84% 的开发者正在使用或计划引入 AI 工具，现在的问题已经不是"要不要用"，而是"**如何用好**"。

## 领先企业具体在做什么

数字的冲击力很明显，但更让人关心的是这些最前沿的企业"**具体在做什么**"。来看几个案例。

### Spotify — 通勤途中通过 Slack 生成代码

Spotify 构建了内部 AI 系统"Honk"。
这是一个通过 Slack 向 Anthropic 的 Claude Code 下达开发指令的机制，实现了以下工作流程：

1. 工程师在通勤途中通过智能手机下达 Bug 修复或功能添加指令
2. Claude Code 开始工作
3. 完成后在 Slack 中回复
4. 确认内容后合并到生产环境

联合 CEO Söderström 在 2026 年 2 月的财报发布会上说："我们的资深工程师自 12 月以来一行代码都没写过。他们现在只是生成代码并进行监督。"
2025 年发布了 50 多个新功能，他还表示"这只是革命的开始"。

> 参考：https://www.itmedia.co.jp/aiplus/articles/2602/13/news074.html

### OpenAI — 100% 由 AI 编写的百万行产品

OpenAI 工程师 Ryan Lopopolo 公开的"Harness Engineering"是一个特别令人印象深刻的案例。
3 名工程师在 5 个月内构建了包含约 100 万行代码的产品，但人类直接编写的代码为零行。全部由 Codex Agent 生成。
每人每天平均处理 3.5 个 PR，与传统手动开发相比，实现了约 **10 倍**的速度。

该团队提出的"Harness Engineering（驱动工程）"的核心思想是：工程师的工作不是编写代码，而是设计和构建让 AI Agent 能有效工作的"**环境（Harness）**"。具体由以下三大支柱构成：

- **上下文工程（Context Engineering）**：持续强化仓库内的文档，将 `AGENTS.md` 作为约 100 行的"目录"，详细内容结构化存放在 `docs/` 目录中。"即使团队在 Slack 讨论中就架构达成了共识，如果 Agent 无法发现，那就等于不存在"
- **架构约束（Architecture Constraints）**：强制执行不变条件（invariants），但不指定实现细节。通过自定义 Linter 和结构测试进行监控
- **垃圾回收（Garbage Collection）**：Agent 定期检测和修复文档不一致及架构约束违规

> 参考：https://openai.com/index/harness-engineering/

### Anthropic — 用数据证明 67% 的生产力提升

Anthropic 于 2025 年 12 月公开了一项内部调研，分析了 132 名工程师的调查问卷、53 次定性访谈和 20 万份 Claude Code 对话记录。
主要数据如下：

- 工程师在日常工作的 **59%** 中使用 Claude（一年前为 28%，翻了一倍）
- 自我报告的生产力提升为 **50%**（一年前为 20%）
- Claude Code 导入后，每人合并的 PR 增加了 **67%**
- Claude 辅助业务的 **27%** 是"没有 AI 就不会执行的任务"（仪表盘创建、探索性工作等）
- Claude Code 自身代码库的 **90%** 由 Claude Code 自己编写

另一方面，工程师能"完全委托"的仅占整体的 0～20%，80% 以上仍需要积极监督。
报告同时指出了 AI 是"常态化协作者"而非完全自主这一现实。

> 参考：https://anthropic.com/research/how-ai-is-transforming-work-at-anthropic

## 工程师的角色将如何变化

看了领先企业的案例后，可以说一个共同点是："编写代码"正在不再是工程师的核心工作。
那么，工程师的角色是如何变化的呢？

### Coder → Conductor → Orchestrator 的进化

ESLint 作者整理的工程师角色进化阶段非常清晰，这里介绍一下。
分为三个阶段：

| 阶段 | 时期 | 角色 |
| --- | --- | --- |
| 第一阶段 Coder | 2024 年上半年 | AI 是高级自动补全。用于编写无聊的样板代码 |
| 第二阶段 Conductor | 2024 年下半年～2025 年 | 工程师向 LLM 下达指令，审查输出并反馈 |
| 第三阶段 Orchestrator | 2025 年末～现在 | 将工作分配给多个自主后台 Agent，并组合其输出 |

由此可见，"向 AI Agent 下达指令让它写代码"的方式属于第二阶段。
接下来要瞄准的是第三阶段，也就是编排（Orchestrate）多个 Agent 的阶段。

> 参考：https://humanwhocodes.com/blog/2026/01/coder-orchestrator-future-software-engineering/

### 重要性上升和下降的技能

随着角色向 Orchestrator 转移，所需技能也在变化。
以下是今后被认为会更加重要的技能：

- 系统架构和设计（将问题分解为 AI 可实现形式的能力）
- AI 输出的审查与验证（发现微妙的逻辑错误、安全缺陷和架构反模式的能力）
- Prompt 工程与上下文管理
- 测试策略设计
- 领域知识与业务理解

Google 的 Addy Osmani 说过："最优秀的工程师不是最快的编码者，而是**知道何时该质疑 AI** 的人。"

另一方面，重要性下降的技能包括：

- 记忆语法和 API 规范
- 手写样板代码 / CRUD 代码
- 手动格式化和样式应用
- 常规性调试
- 编写例行文档

据说这些领域 Agent 几乎可以完全替代。

## 从单 Agent 到多 Agent

那么，实现第三阶段 Orchestrator 的具体方法是什么呢？
关键就在于"**多 Agent 开发**"。
实际上在 OpenAI 内部，一名工程师同时操控 10～20 个并行 Codex Agent，专注于代码审查和合并的工作流程已经定型。
据 OpenAI 的工程负责人称，PR 增加了 70%，审查时间从 10～15 分钟缩短到 2～3 分钟。

### 多 Agent 的主要模式

目前实践中的多 Agent 模式主要有 5 种：

| 模式 | 运作方式 | 适用场景 |
| --- | --- | --- |
| Leader-Worker | 编排者生成专门 Worker → 各自工作 → Leader 整合 | 全栈功能开发（后端、前端、测试各负责） |
| Task Queue | Leader 创建任务队列 → Worker 自行领取 | 大规模迁移、可并行的简单作业 |
| Pipeline | Agent A → Agent B → Agent C 依次处理 | 数据管线、有顺序依赖的处理 |
| Competition | 多个 Agent 处理同一任务 → 采用最佳方案 | 架构设计的方案探索 |
| Watchdog | Worker 工作 → 监控 Agent 检查 → 有问题时回滚 | 安全敏感的变更 |

### 具体工具与实践方法

**Claude Code Agent Teams（实验性功能）** 是多个 Claude Code 实例作为团队协同工作的机制。
Team Lead 分配任务，各 Teammate 在独立的上下文窗口中工作，通过点对点消息通信。
文件锁机制（自申领方式）防止冲突，可通过 tmux / iTerm2 拆分面板显示。

**Cursor 2.0** 通过 Git worktree 隔离环境，最多可同时运行 8 个 Agent。
使用 Background Agents，可在 AWS 隔离机器上克隆仓库，在独立分支上自主完成功能并推送。
开发者可管理 10 个以上的并行 Agent。

**Claude-Flow（开源）** 是一个面向 Claude Code 的编排框架，配备向量记忆、知识图谱和智能路由，实现 API 成本降低 30-50%。

### 多 Agent 适用和不适用的场景

适用于可并行的独立任务（前端/后端/测试同时开发、多假设 Bug 调查、大规模重构）。
相反，对于顺序依赖的任务、同一文件的编辑、依赖关系复杂的工作，编排的开销会超过收益。
我认为从 2～3 个并行开始，逐步熟悉是比较现实的做法。

## 陷阱与风险

到目前为止我们主要关注了 AI Agent 驱动开发的积极面，但自然也存在风险。
事实上，委托给 Agent 的范围越大，对风险的防备就越重要。

### "发布自己没读过的代码"时代的质量管理

OpenAI 内部有一句话："I ship code I don't read（我发布自己没读过的代码）"。
乍听之下很危险，但背后有严格的测试、Linter、结构约束和审查流程。
保障 AI 编写代码质量的，已经不仅仅是人工目视审查，而是**自动化的护栏体系**。

正如 OpenAI 的"Harness Engineering"所示，当 Agent 遇到困难时，"再努力一点"无法解决问题，而应该思考"缺少什么信息，如何让 Agent 能够读取并可强制执行？"——改善环境的思维非常重要。

### 安全性

Veracode 2025 年的调查评估了 80 项编码任务和 100 多个 LLM，结果显示 AI 生成代码中安全的仅有 **55%**。
而且这个数字在更新、更大的模型中也没有改善。
常见漏洞包括 SQL 注入、硬编码密钥、不安全的 Kubernetes 配置、以及幻觉出不存在的包（成为供应链攻击的温床）。

> 参考：https://www.veracode.com/blog/ai-generated-code-security-risks/

### 技术债务的加速

MIT Sloan Management Review 将 AI 生成代码称为 "**积累技术债务的新信用卡**"。
GitClear 的调查发现，使用 AI 工具后代码流失率（添加后很快被修改或删除的代码）翻倍，代码的重组和复用呈减少趋势。
OX Security 的报告识别出了 AI 生成代码常见的 10 种反模式（回避重构、过度定制化、重新发明已有库、无意义的测试注水等）。

> 参考：https://devops.com/ai-in-software-development-productivity-at-the-cost-of-code-quality-2/

### 橡皮图章问题

Clutch 2025 年 6 月的调查显示，**59%** 的开发者在未完全理解的情况下使用 AI 生成的代码。
Palo Alto Networks 的研究发现，有 AI 辅助的开发者编写的代码反而比没有 AI 的开发者更不安全，但却相信自己的代码是安全的（自动化偏见）。
将此视为不是个人问题而是组织性护栏设计问题的观点，我深表认同。

> 参考：https://clutch.co/resources/devs-use-ai-generated-code-they-dont-understand

### 技能退化

年轻工程师因依赖 AI 而无法掌握基础技能的风险，即使在 Anthropic 内部也备受关注。
Google 的工程师也警告说："如果完全切断初级工程师的培养通道，5-10 年后会出现领导力空白。"
我认为，不应仅将 AI 节省的时间用于"更快发布"，而应将其再投资于可扩展性、安全性和长期可维护性，这种态度非常重要。

> 参考：https://addyosmani.com/blog/next-two-years/

## 为了迈向下一步

列举风险可能令人不安，但换个角度来说，只要有意识地将这些对策纳入其中，就能更安全地享受 Agent 驱动开发的红利。
基于上述领先企业案例和风险，整理一下从指令式开发迈向下一步的阶段。

### Phase 1：实验 — 体验与基础建设

首先从体验和基础建设开始。从亲身体验 AI Agent 的能力和局限入手。

#### 整备 CLAUDE.md / AGENTS.md / .cursor/rules

在调研中，我觉得这是**最具即时效果**的做法。在仓库中记录项目结构、命名规范、架构不变条件、使用的库及其原因。
参考 OpenAI 的经验，设计为约 100 行的"目录"，详细内容结构化存放在 `docs/` 目录中。
将 Slack 和文档中的隐性知识集中到仓库中，是活用 Agent 的出发点。

#### 尝试"spec → plan → implement → test → review"工作流

Google Chrome 团队工程师提出的工作流中，首先与 AI 对话创建规格书（`spec.md`），然后生成分阶段实施计划（`plan.md`），按小块依次实施 → 测试 → 审查。
这也被称为"15 分钟瀑布"，比一次性投入大量指令的质量要高得多。

#### 从 2～3 个并行 Agent 开始

Claude Code 的 Sub-agents、Cursor 的 Background Agents、GitHub Copilot 的 Coding Agent 等，可在低风险任务（添加测试、生成文档、更新依赖）中尝试后台异步执行的 Agent。

### Phase 2：定着 — 工作流标准化

将工作流标准化的阶段。

#### 将 AI 代码审查纳入流水线

对所有 PR 自动执行 AI Agent 代码审查。
人工审查前由 AI 进行一次审查，让人类能够专注于更高层次的设计判断和业务逻辑。

#### 将测试强化为"安全网"

Agent 在测试套件健全的环境中性能会**大幅提升**。
首先让 AI 编写测试覆盖率低的区域，由人工审查确保质量。
测试越充实，能委托给 Agent 的范围就越大。

### Phase 3：优化 — 多 Agent 编排

多 Agent 编排阶段。

#### 正式引入多 Agent 开发

在一个功能开发中，并行运行后端担当 Agent、前端担当 Agent、测试担当 Agent、审查担当 Agent。
Git worktree 或独立分支的隔离是关键。

#### 以"Harness Engineering"理念设计仓库

将所有知识存放在仓库中，通过自定义 Linter 和架构测试约束 Agent 的输出，通过垃圾回收 Agent 定期检测不一致。
Agent 遇到困难的地方正是需要改善环境的信号——这种思维方式非常重要。

## 结语

向 AI Agent 下达指令编写代码——这件事本身正在变得理所当然。
但这次广泛调研之后，我了解到在那之后还有更广阔的世界。整备 Agent 容易工作的环境，编排多个 Agent，用护栏防范风险。从"下达指令的人"到"**设计环境的人**"的转变，就是下一步。

就我个人而言，文档整备在推进中，但感觉仍处于 Conductor 阶段。
接下来我想迈入多 Agent 开发，向 Orchestrator 进化。
