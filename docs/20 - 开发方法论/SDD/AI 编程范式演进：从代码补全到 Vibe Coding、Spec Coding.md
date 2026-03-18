---
title: "AI 编程范式演进：从代码补全到 Vibe Coding、Spec Coding"
source: "https://mp.weixin.qq.com/s/X_hawaL1K_fBvF_KzjsA4w"
author:
  - "[[皮皮鲁呀鲁西西]]"
published:
created: 2026-03-18
description: "AI 编程范式演进：从代码补全到 Vibe Coding、Spec Coding"
tags:
  - "clippings"
---
原创 皮皮鲁呀鲁西西 *2026年2月4日 11:34*

> **公众号** ：AIGC 生活实验室  
> **简介** ：探索 AI 如何改变工作与生活  
> **作者** ：皮皮鲁呀鲁西西

本文从技术开发者视角深入剖析 AI 编程范式的演进历程，从最早的代码补全工具，到 Chat 编程、Agent 编程，再到最新的 Vibe Coding 和 Spec Coding。通过实际案例和原理解析，帮助开发者理解每种范式的适用场景，并提供工具选型和最佳实践指导，让大家在不同开发场景下选择合适的 AI 编程方式。

![图片](https://mmbiz.qpic.cn/mmbiz_png/gR0JlSzSWbgEepZ5LClTq3ySoEmGGV1y4Hu6g5ycoibIeFvia7pSU0byiacOX4avt3LibicsCCMeibL8AjN0qtVFIUvg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

还记得最早使用 Copilot 时候，已经非常惊艳了，代码补全，选中代码可以进行问答解释。到现在整个 Coding 过程已经离不开 Claude Code、Codex等，甚至说已经离不开 AI。我们写代码的方式这 5 年一直在改变。代码补全时代的惊艳，到 Chat 编程，再到现在的 Vibe Coding 和 Spec Coding，每一次演进都在重新定义"编程"这件事。

## 从代码补全开始

2021 年，GitHub Copilot 横空出世。那时候大家还在争论 AI 写代码会不会取代程序员。

说实话，刚开始用的时候，我有点抗拒。键盘还没敲完，屏幕上就跳出一整段补全建议，搞得我好像失去了对代码的控制权。但后来习惯了，发现它确实能省不少时间。

这就是最早的 AI 编程范式 - 代码补全。

核心逻辑很简单：你写一部分代码，AI 基于上下文自动补全剩下的。比如你在写一个 Python 函数：

def fetch\_user\_data(user\_id)：  
return db.query("SELECT \* FROM users WHERE id =?"， user\_id)

这种模式的特点是：

- 需要你先写出框架
- AI 做的是"填空题"
- 实时、就地补全

但问题也很明显：AI 只能补全它见过的模式，遇到新的逻辑或复杂的业务场景，它就傻了。而且，你得知道要写什么，它才能补全。本质上，你还是那个写代码的人，AI 只是帮你少敲几下键盘。

GitHub Copilot、TabNine、Kite 这些工具都属于这个时代。它们让编程速度变快了，但没有改变编程的本质。

## Chat 编程：英语成了编程语言

2022 年底，ChatGPT 火了。

那时候突然冒出来一句话："英语是最热门的编程语言。"这句话不是我说的，是 Andrej Karpathy 在 2023 年说的。

Chat 编程模式彻底改变了交互方式。你不再是"写代码然后等补全"，而是直接和 AI 对话。

"写一个函数，用二分查找在有序数组中找到目标值，如果找不到返回 -1"  
  
def binary\_search(arr， target)：  
left， right = 0， len(arr) - 1  
while left <= right：  
mid = (left + right) // 2  
if arr\[mid\] == target：  
return mid  
elif arr\[mid\] < target：  
left = mid + 1  
else：  
right = mid - 1  
return -1

这个模式的关键变化：

- 你用自然语言描述意图
- AI 理解并生成完整代码
- 多轮对话，可以不断调整

但问题来了：代码生成出来后，你还得复制粘贴到编辑器里。而且，如果你不懂编程，根本不知道 AI 生成的代码对不对。

ChatGPT、Claude 都属于这个时代。它们让不懂编程的人也能生成代码，但生产级的代码，还得靠开发者把关。

## Agent 编程：从打字员到项目经理

2023 年底，Agent 编程开始兴起。

Cursor、Replit Agent、Windsurf 这些工具的出现，标志着 AI 不再只是"补全"或"生成"代码，而是能真正"干活"了。

想象一下这个场景：你对 AI 说："给这个项目加一个用户注册功能，需要邮箱验证和密码强度检查。"

然后 AI 就开始：

- 扫描项目结构
- 找到合适的位置
- 创建必要的文件
- 修改配置
- 编写测试代码
- 运行测试并修复错误

整个过程，你可能只需要喝杯咖啡的时间。

这就是 Agent 编程的核心：AI 有了"一定自主执行"的能力。

Cursor 的最佳实践文档里提到了几个关键点：

1. **Plan Mode** ：先规划再执行
	按 Shift+Tab 切换到规划模式Agent 会先研究代码库询问你一些澄清问题创建详细计划等你批准后再开始写代码
2. **上下文管理** ：AI 能自动发现相关文件 不需要你手动选择，Agent 会自己找到需要修改的代码。
3. **多文件操作** ：一次改动涉及多个文件 比如加一个新功能，可能需要改 API、前端、配置文件，Agent 能一次性搞定。

但 Agent 编程也有问题：复杂项目里，多个 Agent 容易"打架"，一个改了另一个又改回去。后来发现用"Planner + Worker"的分层架构更稳定 — Planner 负责规划和分配任务，Worker 专注执行，不互相协调。

## Vibe Coding：放弃代码，拥抱"氛围"

2025 年 2 月，Andrej Karpathy 造了一个新词：Vibe Coding。

他在 Twitter 上是这么说的：

> "There's a new kind of coding I call 'vibe coding'， where you fully give in to vibes， embrace exponentials， and forget that code even exists."

翻译过来就是："有一种新的编程方式叫 Vibe Coding，你完全交给'氛围'，拥抱指数级增长，忘掉代码这回事。"

这句话听起来有点玄，但 Karpathy 补充了细节：

> "It's possible because LLMs (e.g. Cursor Composer w Sonnet) are getting too good. Also I just talk to Composer with SuperWhisper so I barely even touch the keyboard."

所以，Vibe Coding 的核心是：

- 用自然语言描述功能意图和"氛围"
- AI 生成完整的可执行软件
- 开发者基本不碰代码
- 只通过运行结果来评估
![图片](https://mmbiz.qpic.cn/mmbiz_png/gR0JlSzSWbgEepZ5LClTq3ySoEmGGV1yLN4FVht2PkzVicMTpviaGHYDqGaACedZ8SeeUz1kbWw7FficmvtQm6FxA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

比如你对 AI 说："做一个动态的、有音乐反应的视觉体验，要很有艺术感。"

然后 AI 就开始生成代码、调参、生成视觉效果。你只需要看效果，说"这个感觉对了"或者"再暗一点"之类的。

这和传统编程的区别：

**传统编程** ：

def create\_visual()：  
for i in range(100)：  
x = i \* step  
y = math.sin(x) \* amplitude  
draw\_circle(x， y， radius)

**Vibe Coding** ：

"生成一个波浪形的动态视觉效果，要柔和、优雅，像海洋呼吸一样"

Vibe Coding 特别适合：

- 快速原型开发
- 周末项目和创意实验
- 非程序员快速验证想法
- 初学者学习编程

但说实话，这玩意儿在生产环境里用挺冒险的。

IBM 的调研报告里提到，Vibe Coding 生成的一些 Web 应用，170 个里有安全问题。而且，AI 可能会生成你完全看不懂的代码结构，后期维护就是灾难。

Simon Willison 说得更直白："用 Vibe Coding 写生产代码，显然有风险。"

但换个角度想，如果是做个人项目或者周末想玩点新东西，Vibe Coding 确实能让你快速把想法变成现实，不用被语法和框架细节卡住。

## Spec Coding：从"代码即事实"到"意图即事实"

如果 Vibe Coding 是"完全交给 AI"，那 Spec Coding 就是"给 AI 立规矩"。

Spec-Driven Development(SDD)的核心思想很简单：你先写清楚要做什么，然后 AI 基于这个规范来生成代码。

Martin Fowler 把 Spec Coding 分成三个级别：

**级别 1： Spec-first**

- 先写详细的规范
- 规范是唯一事实来源
- Agent 基于规范生成代码

**级别 2： Spec-anchored**

- 规范写完后保留
- 用于后续的演进和维护
- 规范会随时间更新

**级别 3： Spec-as-source**

- 规范是主要源文件
- 只有规范被人类编辑
- 人类完全不碰代码
![图片](https://mmbiz.qpic.cn/mmbiz_png/gR0JlSzSWbgEepZ5LClTq3ySoEmGGV1yenWcwhVjB8QgwhibY3drZyIWkogQyjPusX9Jnuj3W181lg3aicPohyxQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

举个具体例子，假设你要加一个用户注册功能：

作为用户，我想要注册一个账号，这样我就能使用所有功能。  
  
验收标准：  
\- 用户必须提供邮箱、密码和确认密码  
\- 密码长度至少 8 位，包含大小写字母和数字  
\- 系统发送验证邮件  
\- 邮箱验证后账号才能使用  
\- 验证链接 24 小时内有效

然后你用 AI 工具读取这个规范，让它生成实施计划：

1\. 创建用户注册 API 端点 (POST /api/register)  
2\. 实现密码验证逻辑  
3\. 集成邮件发送服务  
4\. 创建验证链接生成和验证逻辑  
5\. 添加前端注册表单  
6\. 编写单元测试

最后，AI 基于计划生成代码和任务列表：

\- \[ \] 实现 POST /api/register 端点  
\- \[ \] 添加密码强度验证函数  
\- \[ \] 配置 SMTP 邮件服务  
\- \[ \] 实现验证 token 生成  
\- \[ \] 创建前端注册页面  
\- \[ \] 编写 API 测试用例

这个流程的好处是：

- 意图明确，不容易跑偏
- 可追溯，每行代码都能对应到规范
- 便于团队协作，大家看同一份规范
- 易于维护，改需求先改规范

主要工具包括：

- **Spec Kit** ：完整的 Spec 工作流
- **Openspec** ：轻量级 Spec 工作流
- **Claude Code、Codex** ：Coding CLI 工具
- **等等**

另外像是Opencode等开源 Coding 工具也都是完整支持 plan 模式的。并且越来越多的工具、Agent 也纷纷加入了 plan 阵营。

## 两种新范式的对比

Vibe Coding 和 Spec Coding 看起来是两个极端，一个"放飞自我"，一个"严格规范"。

但从本质上说，它们解决的是不同的问题：

| 维度 | Vibe Coding | Spec Coding |
| --- | --- | --- |
| 核心理念 | 速度、创造性流程 | 结构、规范、可验证 |
| 开发者角色 | 创意指导者 | 架构师/产品负责人 |
| 代码审查 | 最小化或跳过 | 严格，基于规范验证 |
| 适用场景 | 原型、实验、创意项目 | 复杂系统、长期维护、团队协作 |
| 学习曲线 | 低，几乎不需要编程知识 | 高，需要理解软件工程原则 |
| 产出质量 | 可变，依赖 AI 能力 | 一致、可维护、可追溯 |

说实话，我个人觉得这俩都不是"银弹"。

Vibe Coding 适合快速探索，但你得敢用。比如你周末想做个小游戏，或者给女朋友写个小工具，完全可以用 Vibe Coding 快速搞定。但如果是公司的核心支付模块，还是老实写规范吧。

Spec Coding 更适合严肃的项目，尤其是团队协作的场景。一份清晰的规范能让大家对齐认知，减少扯皮。但前提是，你会写规范，而且愿意花时间写。

## 工具生态：怎么选?

现在市面上工具一大堆，怎么选适合自己的?

**如果你是代码补全派** ：

- GitHub Copilot：集成度高，稳定可靠

**如果你想试试 Agent 编程** ：

- Cursor：Agent 能力强，Plan Mode 很实用
- Windsurf：开源，定制灵活

**如果你对 Vibe Coding 感兴趣** ：

- **claude code、codex、gemini 三驾马车**
- 另外 opencode 等开源 coding 工具也非常值得尝试

**如果你走 Spec Coding 路线** ：

- Spec Kit 等各类 Spec 工作流
- Vibing Coding 的三驾马车也是必不可少的

## 实战建议：如何用好这些范式

说了这么多，到底该怎么用?

我有几个实践建议：

**1\. 混合使用，别拘泥于一种**

我现在的习惯是：

- 刚开始探索一个想法时，用 Vibe Coding 快速验证
- 想法靠谱了，将想法转换为可落地的规范
- 核心业务逻辑自己把关
- 单元测试以及 code review 作为做后的兜底必不可少

这样既能享受速度，又能保证质量。

**2\. 善用 Plan Mode**

无论用哪种工具，规划先行都很重要。

在 Claude Code 等工具中习惯先用 Plan Mode：

- 让 AI 先研究代码库
- 问清楚需求和约束
- 生成详细计划
- 我确认后再开始执行

这样能避免 AI 一上来就改错地方，或者是修改时候没有考虑到关联部分的影响。

**3\. 写好规范是 Spec Coding 的关键**

规范不是越详细越好，关键是清晰。

好的规范示例：

用户注册功能  
  
需求：  
\- 用户可以用邮箱注册账号  
\- 需要设置密码并进行强度检查  
\- 发送验证邮件  
  
验收标准：  
WHEN 用户提交注册表单 AND 密码符合强度要求  
THEN 系统发送验证邮件 AND 返回成功消息  
  
WHEN 用户点击验证链接 AND 链接在有效期内  
THEN 账户状态变为"已验证"

坏的规范示例：

注册功能，要做得好一点。

你说这俩哪个 AI 能理解?

**4\. 代码审查不能少**

尤其是 Vibe Coding 生成的代码，你必须审查。Code review 最为做后的兜底是最重要的一步。

## 总结：编程不是写代码，是解决问题

从代码补全到 Vibe Coding、Spec Coding，AI 编程范式在不断演进。

但核心没有变：编程的本质是解决问题，不是写代码。

代码补全时代，我们学的是"如何更快写代码"。  
Chat 编程时代，我们学的是"如何更好地和 AI 对话"。  
Agent 编程时代，我们学的是"如何让 AI 在一定程度上可以自己干一些活"。  
Vibe Coding 时代，我们学的是"如何描述意图和氛围"。  
Spec Coding 时代，我们学的是"如何写清楚要做什么"。

未来的开发者，可能更像"产品经理 + 架构师"的组合。你不需要精通每个编程语言的语法，但你需要：

- 清晰表达需求
- 设计合理的架构
- 判断代码质量
- 持续学习和适应新技术

说到底，AI 是工具，不是替代品。你的价值不在于能不能写代码，而在于能不能解决真正的问题。

---

如果这篇文章帮到了你，点个在看👀吧，下次再见

---

**AIGC 生活实验室**

> 📮 投稿/合作：egretss.bai.it@gmail.com  
>   
> 💬 交流群：回复"加群"  
>   
> ✍️ 作者：皮皮鲁呀鲁西西  
>   
> 🚀 关注我，一起探索技术的更多可能

继续滑动看下一个

AIGC生活实验室

向上滑动看下一个