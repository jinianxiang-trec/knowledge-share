---
title: "从 Vibe Coding 到 Spec-Driven：AI 原生开发的五个觉醒"
source: "https://zhuanlan.zhihu.com/p/2009360065698763400"
author:
  - "[[夏凯文]]"
published:
created: 2026-03-18
description: "TL;DR：90-95% 的 Vibe Coding 项目活不过两周——代码能跑但没人敢改。问题不在 AI 的能力，在于\"人给 AI 的东西太随意\"。Spec-Driven Development（SDD）正在成为 2025-2026 年最重要的工程化趋势：五大…"
tags:
  - "clippings"
---
**TL;DR：**

1. 90-95% 的 [Vibe Coding](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=Vibe+Coding&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiJWaWJlIENvZGluZyIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI3MDU1MzEzNywiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.1SIfjDGOl7VekI-dGjfczKkUGLrSP5P-0MhPeVkZb7o&zhida_source=entity) 项目活不过两周——代码能跑但没人敢改。问题不在 AI 的能力，在于"人给 AI 的东西太随意"。
2. [Spec-Driven Development](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=Spec-Driven+Development&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiJTcGVjLURyaXZlbiBEZXZlbG9wbWVudCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI3MDU1MzEzNywiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.srNQHmSW7aSkbzkZqZUlircqYdvzppTiXh61RJavOTw&zhida_source=entity)（SDD）正在成为 2025-2026 年最重要的工程化趋势：五大框架争鸣、[Martin Fowler](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=Martin+Fowler&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiJNYXJ0aW4gRm93bGVyIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjcwNTUzMTM3LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.X0knIz5O8JKOO33dPA2H-_ztTwsg1gptt0IFf-1pz0U&zhida_source=entity) 发出警告、实战案例证明 0 行手写业务代码也能通过 230 个测试。
3. 这个系列五篇文章从全景到实战、从架构到反思，完整拆解了 SDD 的 What / How / Why / Why Not / What Else，每篇解决一个具体问题。

---

![](https://pic1.zhimg.com/v2-e9d71cba58e2c93916ba34d85fdcde5a_1440w.jpg)

### **一、一个让人兴奋又焦虑的数字**

我最近做了一个实验：用 [Claude Code](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=Claude+Code&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiJDbGF1ZGUgQ29kZSIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI3MDU1MzEzNywiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.i_3k0rOhYy_sg86JwGZSPRnrfbeAZWOVA9BmzeTxQSs&zhida_source=entity) 从零构建一个 Python 应用框架——[IoC 容器](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=IoC+%E5%AE%B9%E5%99%A8&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiJJb0Mg5a655ZmoIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjcwNTUzMTM3LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.d_9V8x5BiN0OLz85q3anFoWiC4YoEoxC05mkVg1wfow&zhida_source=entity)、[依赖注入](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiLkvp3otZbms6jlhaUiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNzA1NTMxMzcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.YxT0jflcr15aRn8MlhXX3f_U62cVqjepN80qPOCY35k&zhida_source=entity)、[路由引擎](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=%E8%B7%AF%E7%94%B1%E5%BC%95%E6%93%8E&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiLot6_nlLHlvJXmk44iLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNzA1NTMxMzcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.3yqvBnSTgJ-RGctkOJ8aHuTS4wgPes0gdv-joQdiCag&zhida_source=entity)、[中间件链](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=%E4%B8%AD%E9%97%B4%E4%BB%B6%E9%93%BE&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiLkuK3pl7Tku7bpk74iLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNzA1NTMxMzcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.I_njr8IjdL_eNRpCLffixMj5qcrp-_4t4kBnBUk1Crs&zhida_source=entity)、[配置管理](https://zhida.zhihu.com/search?content_id=270553137&content_type=Article&match_order=1&q=%E9%85%8D%E7%BD%AE%E7%AE%A1%E7%90%86&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU2NTYsInEiOiLphY3nva7nrqHnkIYiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNzA1NTMxMzcsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.LmWidgyvRjRaSBMWuephoJWIRNJR04dke6gFpGri4uY&zhida_source=entity)。最终结果：5500+ 行框架代码，230 个测试全部通过，整理为 5 次逻辑提交。

手写代码行数：**0**。

兴奋的部分很明显：这在以前需要一个工程师两周的工作量。焦虑的部分也很明显：**如果 AI 可以这样写代码，工程师的核心价值在哪里？**

答案藏在那个实验成功的关键里——不是 AI 有多聪明，而是**我写了一份非常精确的 Spec**。没有那份 Spec，同样的 AI、同样的模型，产出可能是一堆能跑但不可维护的意大利面代码。

这就是 Spec-Driven Development（SDD）的核心命题：**在 AI 原生开发中，Spec（规格说明）取代代码成为最重要的人工产出。**

### **二、Vibe Coding 的蜜月期结束了**

在 SDD 之前，大多数人用 AI 写代码的方式叫 **Vibe Coding**——给 AI 一个模糊的意图（"帮我写个用户管理系统"），让 AI 自由发挥，看结果觉得差不多就用。

这种方式在小项目上效率惊人。写个脚本、做个原型、改个 bug——AI 的"氛围编码"确实快。

但 Andrej Karpathy 给 Vibe Coding 定义的使用边界很清楚：**"用于不重要的代码"**。一旦项目超过原型阶段，Vibe Coding 的问题就开始爆发：

- **可维护性崩塌**：AI 生成的代码能跑，但下周修改时发现牵一发动全身
- **上下文丢失**：对话超过一定长度后 AI "失忆"，开始自相矛盾
- **质量不可控**：同一个 Prompt，跑三次得到三种完全不同的实现

有一个团队做了统计：Vibe Coding 模式下的项目，**90-95% 活不过两周**。不是功能不能用，是维护成本指数级上升，到两周时修改一个功能的成本已经和重写差不多。

SDD 就是在这个背景下出现的——它不否认 AI 写代码的效率，但主张**人必须用更结构化的方式告诉 AI "写什么"和"怎么约束"**。

![](https://picx.zhimg.com/v2-52b34099b9cf158e822dfb24f6eaa387_1440w.jpg)

### **三、五篇文章，五个核心问题**

### **3.1. 问题一：SDD 到底是什么？有哪些流派？**

**对应文章**：《95% 的 Vibe Coding 项目活不过两周——Spec-Driven Development 能拯救它吗？》

2025-2026 年间，至少有五个 SDD 框架在争夺这个新领域的定义权：

|框架|背后公司|核心理念|
|---|---|---|
|Spec Kit|GitHub/Microsoft|四阶段流水线，标准化 Spec 文件格式|
|OpenSpec|开源社区|Brownfield-First，兼容存量代码|
|Superpowers|Claude Code 生态|全栈工作流引擎，Skills 编排|
|Kiro|AWS|IDE 集成，务实路线|
|Tessl|Tessl|Spec-as-Source，激进实验|

Martin Fowler 团队也发出了警告：SDD 可能是 "Verschlimmbesserung"——一个德语词，意思是"越改越坏"。这篇文章完整横评了五大框架的取舍，分析了 Martin Fowler 的担忧是否成立，并给出了"什么时候该用 SDD、什么时候不该用"的判断。

**核心观点**：正确姿势是"渐进式 Spec"——小项目别折腾，中等项目用轻量框架，企业级才需要完整流水线。

### **3.2. 问题二：Spec 架构怎么设计？**

**对应文章**：《一份 CLAUDE.md 统治一个 AI 团队：Spec 架构设计的四层模型》

写一个 Spec 不难，难的是**设计一个 Spec 体系**——当你有 10 个模块、5 个 Skill、3 层配置时，怎么组织才能让 AI 既能理解全局，又不在上下文窗口中被淹没？

这篇文章提出了一个四层架构模型：

1. **宪法层**（CLAUDE.md）：项目级规则，全局生效
2. **技能层**（Skills）：可组合的执行模块，按需加载
3. **知识层**（Docs）：共享领域知识，被 Skill 引用
4. **状态层**（Data）：持久化记忆，跨会话保持

同时结合 Addy Osmani 的 Spec 写作五原则，给出了从"一个文件搞定一切"到"模块化多层分离"的三种组织模式对比和选择决策树。

**核心观点**：Spec 写作的核心原则是"少即是多"——200 行精心设计的 Markdown 远胜 2000 行面面俱到的文档。

### **3.3. 问题三：实际效果如何？**

**对应文章**：《230 个测试、5 次提交、0 行手写业务代码：一个 Python 框架的 AI 原生诞生记》

理论说得再好，不如一个真实案例。这篇文章完整记录了一次 AI 原生开发实验：

- **输入**：一份 50 行的"宪法"（CLAUDE.md）+ 200 字初始 Spec + 10 轮人机对话中演化出的数十条隐性约束
- **过程**：AI 分阶段完成 IoC 容器、依赖注入、路由引擎等核心模块，5 次逻辑提交，每次提交背后都有多轮迭代
- **产出**：5500+ 行框架代码、230 个测试、完整的安全加固——同时坦诚记录了 6 个 AI 完全忽略的安全盲区

文章不只记录成功的部分，也记录了**真实的"翻车"瞬间和 N=1 实验的局限性**——初始 Spec 的"200 字搞定一切"是不诚实的叙事，AI 的安全盲区是系统性的，230 个测试对于"生产级框架"仍然远远不够。

**核心观点**：AI 原生开发的效率瓶颈在人而非 AI——Spec 质量 × 任务分解粒度 × AI 能力，其中前两项都是人的工作。

### **3.4. 问题四：有哪些坑是违反直觉的？**

**对应文章**：《Martin Fowler 说"越改越坏"——Spec-Driven 开发的 12 个反直觉发现》

在实践 SDD 的过程中，有 12 个发现违反了大多数人的直觉。分三类：

**关于 Spec 设计**：200 行比 2000 行有效、可执行命令比描述性文字有效 10 倍、"不做什么"比"做什么"更重要、模糊的高层指令有时比精确的低层指令更好。

**关于工作流**：测试比规范更重要、Vibe Coding 不该被完全抛弃、审查 Spec 比审查代码更累、AI 的第一版通常最好。

**关于人机协作**：AI 会"偷懒"但不会"反抗"、安全是 AI 的盲区而非强项、最佳模式是"人机交替"而非"AI 自治"、写 Spec 的能力比选工具重要 10 倍。

**核心观点**：AI 原生开发的最佳实践不是"选对工具"，而是"建立正确的人机协作模式"——人做决策，AI 做执行，测试做验证。

### **3.5. 问题五：光有 Spec 够不够？**

**对应文章**：《MCP + Skills + Memory：AI 原生开发的基础设施三件套》

Spec 解决了"告诉 AI 做什么"的问题，但实际的 AI 原生开发还需要更多基础设施：

- **MCP**（Model Context Protocol）：AI 工具的"USB 协议"——一次接入，所有 Agent 都能用
- **Skills**：从"写 Prompt"到"编排工作流"——可组合、可复用的执行模块
- **Memory**：让 AI 不再"失忆"——跨会话的持久化记忆，支持冷热分离

这三者加上 Spec，构成了 AI 原生开发的完整基础设施栈。

**核心观点**：真正的竞争力不是用了多少工具，而是工具链的编排质量——6 个精心配置的 MCP Server 远胜 20 个随意堆叠的插件。

### **四、五篇文章的逻辑关系**

  

![](https://pic3.zhimg.com/v2-7be6ea222845551265741cb8f995fb70_1440w.jpg)

这五篇文章形成了一条完整的认知链路：

**SDD 全景横评** 回答 **What**——SDD 是什么，有哪些框架，各自的取舍。这是入口，先建立全局认知。

**Spec 架构设计** 回答 **How**——知道了什么是 SDD，具体怎么设计 Spec 体系。从单文件到四层架构，给出实操路径。

**AI 原生开发实战** 回答 **Proof**——理论和架构都有了，实际效果怎样？一个完整案例给出数据：230 个测试、5 次提交、0 行手写业务代码——同时坦诚记录了翻车瞬间和局限性。

**SDD 反直觉发现** 回答 **Why Not**——实战中哪些地方会踩坑？12 个反直觉发现帮你避开最常见的误区，包括 Martin Fowler 的警告。

**AI 开发基础设施** 回答 **What Else**——Spec 只是 AI 原生开发的一部分，完整的技术栈还需要 MCP + Skills + Memory。

简单说：**全景 → 架构 → 实证 → 反思 → 补全**。

### **五、三种阅读路径**

**路径 A：技术负责人（评估是否在团队中引入 SDD）**

推荐顺序：**全景横评 → 反直觉发现 → 架构设计 → 基础设施 → 实战案例**

先了解 SDD 的全貌和各框架差异，再看 Martin Fowler 的警告和实践中的坑，然后评估架构可行性和基础设施投入，最后看实战案例确认预期。

**路径 B：工程师（想立刻上手 SDD）**

推荐顺序：**实战案例 → 架构设计 → 反直觉发现 → 全景横评 → 基础设施**

从最"眼见为实"的案例开始激发信心，然后学架构设计，再看踩坑经验避雷，最后补全全景认知和基础设施知识。

**路径 C：快速了解（只有 10 分钟）**

读本文 + 全景横评。本文给你完整框架和核心结论，全景横评给你五大框架的对比和选型建议。

### **六、一个更大的图景**

SDD 不是一个孤立的概念。如果把视野拉高一层，它是**"Prompt as Plugin"范式**的一个应用。

Anthropic 在 2025 年底发布了 Agent Skills 开放标准——Microsoft、OpenAI、Google、Cursor 等 20+ 工具已采纳。这个标准的核心思想是：**Markdown 文件就是插件，LLM 就是运行时。** 没有编译、没有注册、没有 API 对接——一个 .md 文件即可定义完整的 Agent 能力模块。

SDD 中的 Spec、Skills、CLAUDE.md，本质上都是这个范式的具体实例。当你写一份 CLAUDE.md 时，你不只是在"配置一个工具"——你是在为一个 AI Agent 编写它的操作系统。

这对软件工程意味着什么？**代码不再是唯一的一等公民。** 在 AI 原生开发中，Spec（规格）、Skill（技能定义）、Memory（状态管理）和代码一样重要，甚至更重要——因为它们决定了代码的质量上限。

工程师的核心价值正在从"写代码"向"定义问题 + 设计约束 + 验证结果"迁移。这个迁移已经在发生，而 SDD 是它最具体的体现。

### **参考资料**

1. Google DORA 2025: State of AI-assisted Software Development — AI 编码的组织级效率数据
2. Anthropic: Eight Trends Defining How Software Gets Built in 2026 — Agent Skills 标准和 AI 开发趋势
3. Agent Skills Open Standard ([http://agentskills.io](https://link.zhihu.com/?target=http%3A//agentskills.io)) — Markdown-based 插件架构的行业标准
4. Martin Fowler: How far can we push AI autonomy in code generation? — AI 自主性边界和"越改越坏"警告
5. GitHub Spec Kit — Microsoft/GitHub 的 SDD 框架
6. Addy Osmani: How to Write a Good Spec for AI Agents — Spec 写作五原则
7. Tessl: The Most Valuable Developer Skill — Writing Code Specifications — Spec 写作能力的行业讨论
8. Kent Beck: TDD, AI Agents and Coding — XP 创始人：AI Agent 是"精灵"，TDD 是协作的超能力
9. Qodo: State of AI Code Quality in 2025 — AI 代码质量报告：信任度仅 29-46%
10. METR: Measuring AI Impact on Developer Productivity — 受控实验：资深开发者用 AI 后反而慢 19%
11. InfoQ: Spec-Driven Development — When Architecture Becomes Executable — SDD 五层执行模型
12. VentureBeat: The creator of Claude Code just revealed his workflow — Boris Cherny 的 Skills + Hooks 工作流

发布于 2026-02-23 21:13・辽宁

发布于 2026-02-23 21:13・辽宁