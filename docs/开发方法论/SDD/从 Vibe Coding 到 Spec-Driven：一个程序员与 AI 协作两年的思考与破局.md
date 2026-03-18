---
title: "从 Vibe Coding 到 Spec-Driven：一个程序员与 AI 协作两年的思考与破局"
source: "https://zhuanlan.zhihu.com/p/1983169470227834131"
author:
  - "[[黑暗天使一个初出茅庐的IT人]]"
published:
created: 2026-03-18
description: "高手用AI如虎添翼，新手用AI可能只是换了个更会“胡说八道”的队友。两年前，当我第一次用上 GitHub Copilot 时，那种“代码自己往外蹦”的惊喜感，至今记忆犹新。 四年多来，AI 编程已经从最初的代码补全，发展到…"
tags:
  - "clippings"
---
> 高手用AI如虎添翼，新手用AI可能只是换了个更会“胡说八道”的队友。

两年前，当我第一次用上 [GitHub Copilot](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=GitHub+Copilot&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJHaXRIdWIgQ29waWxvdCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.Jlzz7YtwXHRo2a0vNOKRQZkSZ7tY5Xyx3V5iay4KGHo&zhida_source=entity) 时，那种“代码自己往外蹦”的惊喜感，至今记忆犹新。

四年多来，AI 编程已经从最初的代码补全，发展到今天能够端到端处理完整开发工作流的“主驾驶”角色。

从 [Vibe Coding](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=Vibe+Coding&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJWaWJlIENvZGluZyIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.cRypYVeprHLR5qoohCYzWWFn09bKlaF-EkkLzzblqj0&zhida_source=entity) 的随性挥洒，到 [Spec-Driven Development](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=Spec-Driven+Development&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJTcGVjLURyaXZlbiBEZXZlbG9wbWVudCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0._x3NYCvcAgpfQar0-hqM-7XprPCh_Ho9G7BzjBedUM0&zhida_source=entity) 的严谨工程化，我踩过坑，也尝过甜头。

今天，就和你聊聊我这两年与 AI 并肩 Coding 的心得与思考。

---

## 一、从 Vibe Coding 开始：高手更强，新手未必

**Vibe Coding** ，可以翻译为“氛围编程”或“感觉编程”。

它的核心是：开发者不再关注繁琐的语法细节，而是用自然语言描述需求和问题，由 AI 模型（LLM）来理解和生成代码。

简单说，就是你给 AI 定个“调子”（氛围），它负责把曲子（代码）写出来。

这听起来很酷，也确实能极大提升效率。我在做一些日常“玩具”项目 时，就曾全程用 AI 写文档、出设计图、写代码，速度惊人。

但 Vibe Coding 有个大前提： **你得是那个能控场的“指挥家”** 。

**首先，你必须对项目全局有宏观把控。**

AI 就像一个天赋极高但经验不足的实习生，你让它自由发挥，它很可能给你堆出一座华丽的“屎山”。

它倾向于把代码无组织地堆砌在几个文件里，缺乏合理的架构分层。

如果你自己都不知道代码该往哪放、模块之间如何交互，那 AI 生成的代码很快就会变得难以维护。

**其次，你必须懂得如何引导 AI，不能让它过分自由发挥。**

这涉及到 **Prompt 工程** （或者说现在更时髦的词：Context Engineering）。

和拒绝接受“一句话需求”的同事一样，你也不能给 AI 一句话需求。

我的经验是，使用 **结构化 Prompt** ，比如 [COSTAR 框架](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=COSTAR+%E6%A1%86%E6%9E%B6&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJDT1NUQVIg5qGG5p62IiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjY3NjA2NTAyLCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.wPrYcA4pEv0byWGlF_xKdNsdsi3Hl80lYuPcVVbtL2M&zhida_source=entity) （Context, Objective, Style, Tone, Audience, Response），或者用伪 XML 格式清晰地定义角色、任务、要求和输出格式。

更重要的是，要 **合理划分 AI 的任务边界** ：

1. **能力范围内的“搬砖”任务** ：逻辑清晰但实现繁琐的 CRUD、格式转换等，交给 AI，效率飞升。
2. **略超能力范围的任务** ：通过短期学习能掌握的陌生 SDK 调用、特定算法实现等，让 AI 去查阅文档和参考代码，事半功倍。
3. **远超能力范围的任务** ：对自己完全陌生的领域， **千万不要完全依赖 AI** ，否则后期调试和重构的成本会高到让你崩溃。

**最后，你必须懂得评价 AI 的输出，及时“拨乱反正”。**

AI 会产生“幻觉”，在代码领域表现为捏造不存在的 API、调用错误版本的函数，或者写出逻辑不通的代码。

因此， **Code Review 变得比以往任何时候都更重要** 。

我的态度已经从“部分信任”转向了“防御性编程”，对 AI 生成的每一行方案和代码，都要仔细审查，确保其正确性和与项目整体架构的契合度。

**总结一下：Vibe Coding 是一把绝世好剑，但只有真正的剑客才能发挥其威力。**

AI 只能让高手更强，因为它放大了高手对架构的掌控力、对问题的拆解能力和对代码质量的判断力。

但它很难把新手变成高手，因为新手缺乏的正是这些核心能力。没有这些，Vibe Coding 产出的很可能是一堆无法维护的技术负债。

---

## 二、让 AI 自己开发软件的尝试：Spec-Driven Development

为了克服 Vibe Coding 的随意性，更工程化的方法出现了—— **规范驱动开发** 。

简单说，SDD 就是把“规格”（Spec）从传统的需求文档，升级为 **可执行、机器可理解的首要输入** ，由 AI 直接把规格转换成代码、测试乃至部署管道。

在 SDD 中，规范成为“事实来源”，代码只是其表达方式。

我尝试过一些 SDD 工具（如 [Kiro](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=Kiro&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJLaXJvIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjY3NjA2NTAyLCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.vy9MePI4c15JYL9YLxS8H6Q819AdY_S7A6vXa-NCzPw&zhida_source=entity) ），也研究过 GitHub 的 [Spec Kit](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=Spec+Kit&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJTcGVjIEtpdCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.ey-i6P5hvwpjDk4Owe-BsQD5kN7KanWW-7-mwV9CnnM&zhida_source=entity) ，它的核心理念是： **写 Spec → 用 AI/工具实现 → 自动验证 → 监控并迭代** 。

**Spec 的优点很明显：**

1. **AI 自主分析与拆解** ：你提供一份结构化的规范（比如用 YAML 描述 API 接口），AI 可以自主分析，拆解成设计、任务，并逐步实现。这减少了大量人机对话成本。
2. **文档清晰完备** ：规范本身就成了最好的、与代码同步的活文档。这解决了传统开发中“文档与代码分居”的老大难问题。
3. **流程清晰，适合协作** ：SDD 提供了清晰的流程（如需求→设计→任务）和 Prompt 模板，非常适合团队统一协作规范，避免每个人用不同的“氛围”和 AI 沟通。

**但 Spec 的缺点和挑战也同样突出：**

1. **错误放大效应** ：前面步骤（如需求分析）的一个小错误或模糊点，会在后续的设计、实现中被 AI 放大，导致最终产物偏离预期。所谓“Garbage in, garbage out”在这里体现得淋漓尽致。
2. **极度依赖底层模型** ：最终生成代码的质量、对复杂业务逻辑的理解深度，完全取决于你所用 AI 模型的智能度。模型的天花板，就是你这个工作流的天花板。
3. **Token 消耗巨大** ：生成详细的规范、让 AI 进行多轮分析和拆解，会消耗大量的上下文长度和 Token，成本不容小觑。
4. **评估困难** ：将 SDD 引入现有的遗留项目异常困难，需要让 AI 理解大量现有代码的上下文，成本可能高于收益。

**我的总结是：SDD 属于 AI Coding 的“软件工程实践”，是未来一个重要的探索方向。**

对于全新的、逻辑相对标准的项目（如电商、CMS），它可以发挥巨大威力。我们可以积极尝试业界方案（如阿里 [Qoder](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=Qoder&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJRb2RlciIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.hNUK_bM2qdP3fakLSo-nyCXpaXh8wux_KWtc9Ctd0E8&zhida_source=entity) 的 Quest Mode、腾讯 [CloudBase AI Toolkit](https://zhida.zhihu.com/search?content_id=267606502&content_type=Article&match_order=1&q=CloudBase+AI+Toolkit&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM5ODU3MjAsInEiOiJDbG91ZEJhc2UgQUkgVG9vbGtpdCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjI2NzYwNjUwMiwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.aQuHFvtVBCPlKdW5X2BaNdnXW2JJyROpngv-qPfwt8s&zhida_source=entity) 的 Spec 工作流），但 **不要迷信** 。

它目前更像一个“理想框架”，在复杂、多变的真实业务场景中落地，还有很长的路要走。

---

## 三、我的思考：AI 革命与程序员的破局

AI 对编程领域的冲击，是革命性的。它正在重塑我们对“编程”这项技能的定义。

**首先，死磕算法和设计模式，可能不再那么重要。**

大部分标准的算法实现、设计模式的应用代码，AI 都能很好地生成。以前需要背诵和反复练习才能内化的“套路”，现在可以由 AI 快速提供。

但这不意味着它们没用，相反， **理解其原理和适用场景变得更重要** 。因为你需要判断 AI 生成的是否是最优解，以及在复杂业务场景下该如何调整。

**其次，AI 更偏爱简单、清晰的代码结构。**

复杂的、过度设计的“炫技”式架构，往往会让 AI 困惑，也增加了后期 AI 维护的难度。

简洁、模块化、高内聚低耦合的代码，不仅人类读起来舒服，AI 也更容易理解和续写。

**最后，也是最尖锐的问题：AI 替代了新手的工作，新手如何积累经验破局？**

这是一个现实困境。当 CRUD、基础页面、简单业务逻辑都能由 AI 快速生成时，初级程序员赖以成长的那些“重复性、基础性”工作就消失了。

我的看法是， **破局的关键在于“升维”** 。

1. **从“编码者”到“架构师与决策者”** ：你的核心价值不再是敲出每一行代码，而是 **搭建项目的骨架** ，定义模块、接口和数据结构，然后把具体的实现细节交给 AI 去填充。你要成为那个定义“规范”（Spec）的人。
2. **从“执行者”到“审查者与引导者”** ：你的时间会更多地花在 **需求分析、系统设计、代码审查（Review AI 的输出）和测试策略制定** 上。你需要一双能发现 AI 幻觉和设计缺陷的“火眼金睛”。
3. **强化“全栈”与“业务”能力** ：AI 让一个人覆盖前后端甚至运维变得更简单。越是对整个技术栈和业务逻辑有全局理解的人，越能高效地驾驭 AI，完成端到端的交付。同时，深刻理解业务，才能写出真正有价值的 Prompt 和 Spec，让 AI 为你生成真正解决业务问题的代码。
4. **培养“人机协作”的新工作流** ：将 AI 无缝嵌入你的开发流程，知道何时让它自由发挥，何时需要收紧缰绳；知道如何用小步快跑、及时 Git 提交和验证的方式来管理 AI 的开发过程，避免陷入“越改越乱”的泥潭。

扎克伯格说，2025 年 AI 将达到中级软件工程师的水平。

这或许不是程序员的末日，而是一个分工洗牌的开始。 **未来的程序员，可能不再是“代码的工人”，而是“问题的架构师”和“AI 的指挥官”。**

这场革命，淘汰的不是编程，而是旧的编程方式。而我们，需要在这场变革中，找到自己新的位置。

发布于 2025-12-13 14:47・广东[程序员](https://www.zhihu.com/topic/23639523)[人工智能工程师](https://www.zhihu.com/topic/26218535)