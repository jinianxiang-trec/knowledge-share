---
title: "Claude Code Skills：让 AI 变身专业工匠"
date: 2026-01-03
type: article
tags: [主题/AI驱动开发, 类型/原创]
author: ""
status: done
related:
  - "[[关于支撑 AI Agent 开发的文档设计的思考]]"
  - "[[AI Agent 驱动开发的最前沿：从基于指令的开发迈向下一步]]"
---

**Claude Code Skills：让 AI 变身专业工匠**
2026.01.03

如果你已经用 Claude Code 一段时间，每次开新专案，Claude 都像个刚入职的新人，什么都得从头说明。你得告诉它「我们团队用的是这套 Coding Style」、「Deploy 流程是这样跑的」、「这个 API 要这样串」。讲一次还好，讲十次、二十次之后就会开始怀疑到底我花钱是在用 AI 还是在当 AI 的助理？

你应该已经知道 `CLAUDE.md` 这个档案的用途。把专案的惯例和规则写在 `CLAUDE.md` 里，Claude Code 启动时就会自动读取，省去每次重复解释的麻烦。`CLAUDE.md` 常被拿来放专案指引，虽然 Claude Code 也支援使用者层级的 `~/.claude/CLAUDE.md` 可以跨专案共用，但不管放哪一层，指引一长就会遇到难维护、而且启动时全部载入的问题，我还遇过 Claude Code 提醒我再继续下去效能会变差...

重点是，`CLAUDE.md` 的内容会在专案启动的时候就全部载入，不管当前的任务用不用得到。

有没有什么方法，可以把「专业知识」打包成独立的模组，让 Agent 自己判断什么时候该用、只载入需要的部分、而且可以跨专案重复使用？嘿嘿，有的，就是 Anthropic 在 2025 年推出的 Skills 功能。

### 什么是 Skills？
根据 Anthropic 的官方定义，Skills 是这样的东西：

> Skills are modular, self-contained packages that extend Claude's capabilities by providing specialized knowledge, workflows, and tools. Think of them as 'onboarding guides' for specific domains or tasks—they transform Claude from a general-purpose agent into a specialized agent equipped with procedural knowledge.

翻译成白话文就是：Skills 是一种打包好的「专业技能包」，可以把 Claude 从一个什么好像都略懂的通才，变成某个领域的专家。

想象你开了一间小吃店，你是一位什么料理都会做、都能做但可能都做的不太好吃的厨师。有天我经过你的小吃店，我看你骨骼精奇，是一位百年难得一见的练武奇才，觉得维护世界的和平就要靠你了，所以决定给你一本「台南小吃完全手册」，里面写着担仔面的汤头怎么熬、肉燥要用什么部位的猪肉、虾仁要去哪个市场买最新鲜、还有那个独门酱油膏的调配比例。看完这本秘籍之后，你就能做出道地的府城味了。

Skills 就是这本「武功秘籍」。Skills 能提供的东西很多，包括：

*   **专业工作流程**：多步骤的作业程序，例如「怎么做 Code Review」、「怎么处理 PR」
*   **工具整合**：跟特定档案格式或 API 互动的方法，例如「怎么处理 PDF」
*   **领域专业知识**：你们公司或团队特有的商业逻辑和惯例
*   **资源**：脚本、参考文件、范本等执行任务时需要的素材

### Skills 长什么样子？
一个 Skills 的基本结构很简单，至少需要一个 `SKILL.md` 档案：

```
skill-name/
└── SKILL.md   # 必要的
```

这个 `SKILL.md` 档案开头必须包含一段 YAML frontmatter：

```yaml
---
name: skill-name
description: A description of what this skill does and when to use it.
---
```

根据 agentskills.io 的规格，`name` 栏位有一些规则要遵守：

*   长度在 1 到 64 个字元之间
*   只能用小写字母、数字和 -
*   不能以 - 开头或结尾，也不能有连续的 -
*   必须与目录名称一致，待会我们实作的时候就会看到

`description` 栏位也有限制：

*   长度在 1 到 1024 个字元之间，所以这里不是写执行细节的地方
*   应该描述这个 Skills 做什么、什么时候该用

frontmatter 之后的内容才是给 Agent 看的指令内容。你可以在里面写任何你想让 Agent 知道的东西，例如操作步骤、注意事项、范例用法等等。如果你的 Skills 比较复杂，需要额外的脚本或参考资料，可以建立这样的目录结构：

```
skill-name/
├── SKILL.md      # 必要的
├── scripts/      # 可执行的程式码（Python、Bash、JavaScript）
├── references/   # 额外的参考文件
└── assets/       # 静态资源（范本、图片、资料档）
```

根据 Agent Skills 规格，这三个目录都是 Optional directories，也就是要加不加都可以。这些目录名称是规格建议的标准结构，让不同的 Skills 有一致的组织方式。Claude 是透过你在 `SKILL.md` 里的引用来发现这些档案的，所以你需要在 `SKILL.md` 里明确引用，Claude 才会知道它们的存在。

### Progressive Disclosure
这里要讲一个我认为 Skills 设计得最漂亮的地方，叫做 Progressive Disclosure（渐进式揭露）。

这概念有点像用 Google Maps 导航。当输入目的地之后，它不会一次把整条路线的每个细节都念出来，而是先给一个大方向，例如「往北走，大约 10 分钟后右转」。等快到路口了，才会接着说「前方 50 公尺右转，进入衡阳路」。如果中途想找加油站或停车场，它才会载入附近的资讯，Skills 的运作方式就是这样。

根据 Anthropic 部落格的说明，Skills 的内容分成三层：

1.  **第一层是 Metadata**，大概只有 100 个 tokens 左右。这一层只包含 `name` 和 `description` 两个栏位。当 Claude Code 启动的时候，会预载所有已安装 Skills 的 Metadata，用来判断「这个任务跟哪些 Skills 有关」。
2.  **第二层是 Instructions**，就是 `SKILL.md` 的主体内容。依 Agent Skills 规格建议，这一层最好控制在 5000 个 tokens 以内。当 Claude 判断某个 Skills 跟当前任务相关，才会把这一层载入。
3.  **第三层是 Resources**，就是 `scripts/`、`references/`、`assets/` 这些目录里的档案。这些内容是**按需载入**的，Claude 需要用到哪个档案才会去读取。

这样设计的好处在于：只要把 Skills 正确地分层，由于档案和资源是按需读取的，理论上可以在一个 Skills 里打包**无限量**的知识。在 Blog 文章里有提到一句：

> This means that the amount of context that can be bundled into a skill is effectively unbounded

因为 context window 是有限的，如果每次对话都把所有知识塞进去，就算是像是有百万 context window 的 Gemini，撑爆也只是早晚的问题。Progressive Disclosure 的设计让 Claude 可以「用多少、拿多少」，不浪费 context 又能在需要的时候取得足够完整的资讯。

### Skills 跟其他机制有什么不同？
如果你用过 Claude Code 一段时间，应该会发现有好几种方法可以「客制化」Claude 的行为。Skills 跟其他机制有什么差别？什么时候该用哪一种？我整理一个比较表：

| 机制 | 触发方式 | 持久性 | 内容类型 | 载入时机 | 可包含程式码 | 适用场景 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Skills** | Claude 自动判断何时启用（根据 description） | 跨对话、跨专案可用 | 程序性知识 + 可执行脚本 | 动态载入（Progressive Disclosure） | 是 | 重复性专业工作流程、需要 Claude 主动判断的情境 |
| **Custom Commands** | 使用者输入 /command（也可引导 Claude 代为呼叫） | 专案或个人层级 | Prompt 范本 | 使用者触发时载入 | 否（单一 prompt 档） | 固定格式的重复操作、使用者明确知道要做什么的情境 |
| **MCP** | 工具呼叫 | 启动时载入 | 外部服务连接 | 按需呼叫 | 是（server 端） | 连接外部 API、资料库、档案系统 |
| **Subagents** | Claude 自动委派或手动启动 | 任务期间 | 独立 AI 实例 | 需要时建立 | 是 | 需要独立 context、平行处理、专门化任务 |

我来解释一下这些机制的差异。

**Custom Commands**（也就是斜线命令）需要你手动输入 `/say-something` 才会触发。根据官方文件的说法，斜线命令是「A Markdown file containing a prompt that Claude executes when invoked」，也就是说它只能包含 prompt 范本，不会有程式脚本或其他资源。你可以把它想成一种「巨集」，预先写好一段指令，之后一键展开执行。它适合用在你已经知道要做什么的情况，例如每次 commit 前都要跑同一套检查流程，像我就写了好几个这样的指令，像是 `/remove-comment` 用来移除不必要的注解，或是 `/sanitize-ai` 用来清理 AI 产生的「机味」文字。

但 Skills 不一样，**Agent 会自动根据当时的对话内容或情境判断要不要启用某个 Skill**。假设你装了一个翻译用的 Skill，当我们跟 Agent 说「帮我把这段英文翻成中文」，Agent 会自己判断「喔，这个任务跟翻译有关，我应该要使用那个翻译的 Skill」，不需要我们特别去触发。

Skills 没有像 Slash Commands 那样「输入固定 /xxx 就必定执行」的触发机制，它主要由模型依 description 做相似度判断来决定要不要启用。官方文件说 Skills 是「model-invoked」，也就是由 Agent 自己决定什么时候用。如果你需要手动触发的功能，那应该用 Custom Commands 而不是 Skills。不过你可以在对话中明确提到 Skills 的功能来引导 Agent，例如说「用我们的翻译规则把这段翻成中文」，让 Agent 更容易判断该启用哪个 Skill。

**MCP**（Model Context Protocol）也有类似自动判断的机制，但两者运作的层次不同。Skills 提供的是「知识」，Agent 载入后会根据这些知识来「指导自己」的行为。MCP 提供的是「工具」，Agent 会呼叫这些工具来执行具体操作。

再回到刚才翻译的例子，如果我们要做翻译，Skills 会告诉 Agent「翻译的时候要用什么语气、专有名词怎么处理、哪些词不要翻」，而 MCP 则是让 Agent 可以存取你的术语表资料库、或是呼叫 DeepL API 来辅助翻译。一个是脑袋里的知识，一个是手上的工具。

MCP 是用来连接外部服务的。如果我们需要请 Agent 存取资料库、呼叫某个 API、或是操作档案，就可以使用 MCP。根据官方定义，MCP「enables Claude Code plugins to integrate with external services and APIs by providing structured tool access」。

是说每次有新东西出现，总是就会有些先知会丢出「取代论」的说法，最近社群有些讨论说 Skills 会取代 MCP，但就我看并不会，因为这两个解决的是不同层次的问题。Skills 是给 Agent「脑袋」，MCP 是给 Agent「手脚」。你可以同时用 Skills 教 Agent 怎么做翻译，又用 MCP 让它能存取术语库，两者之间是互补而不是互斥或互相取代的关系。

至于 **Subagents**，根据官方说明，它是「autonomous subprocesses that handle complex, multi-step tasks independently」。当一个任务太复杂、需要独立的 context 空间、或是可以平行处理的时候，Claude 会启动 Subagents 来帮忙。

简单来说：

*   想让 Agent 自己判断什么时候用什么专业知识？**用 Skills**
*   想手动触发固定的操作流程？**用 Custom Commands**
*   想连接外部服务？**用 MCP**
*   想平行处理复杂任务？**让 Claude 用 Subagents**

这四个机制不是互斥的，可以同时运作。举个例子：使用 Custom Command `/review-pr` 触发 Code Review 流程，Claude 会载入 Code Review 的 Skills 来取得审查标准，同时透过 MCP 连接 GitHub API 拉取 PR 内容，如果 PR 改动的档案很多，Claude 可能还会启动 Subagents 平行审查不同的档案。四个机制各司其职然后一起完成任务，看到 AI 很忙的样子，看起来不是很酷吗 :)

### 建立自己的 Skill
听这么多理论手痒了吗？来实际动手做一个 Skills 吧！

从一个大家比较常会遇到的情境开始讲起。我猜很多工程师都不喜欢写 Git 的 Commit Message，我也是。如果我想要建立一个可以帮忙处理 Commit Message 的 Skill，让 Agent 不只帮我们写，而且还要遵循特定格式...

第一步，建立一个资料夹，名称随意：

```bash
mkdir commit-message-helper
```

第二步，在这个目录里建立 `SKILL.md` 档案，内容如下：

```markdown
---
name: commit-message-helper
description: Helps write Git commit messages following the Conventional Commits specification. Use this skill when the user asks to commit changes, write commit messages, or mentions git commits.
---

# Commit Message Helper

When writing commit messages, follow these rules:

## Format

<type>(<scope>): <subject>

<body>

<footer>

## Types

- feat: A new feature
- fix: A bug fix
- docs: Documentation only changes
- style: Changes that do not affect the meaning of the code
- refactor: A code change that neither fixes a bug nor adds a feature
- perf: A code change that improves performance
- test: Adding missing tests or correcting existing tests
- chore: Changes to the build process or auxiliary tools

## Guidelines

1. Subject line should be no longer than 50 characters
2. Use imperative mood ("add feature" not "added feature")
3. Do not end the subject line with a period
4. Separate subject from body with a blank line
5. Use the body to explain what and why, not how

## Examples

Good:
feat(auth): add OAuth2 login support

Implement OAuth2 authentication flow to allow users to log in
with their Google or GitHub accounts.

Closes #123

Bad:
updated stuff
```

别忘了 `name` 栏位要跟资料夹名称一样，这是规格要求的。再来注意 `description` 栏位的写法，这里特别写了「when the user asks to commit changes, write commit messages, or mentions git commits」，这样 Claude 才知道「什么时候」该启用这个 Skill。`description` 写得越具体，Claude 的判断就越准确。

是说，如果不知道这个档案的内容怎么办？问 AI 啊！把情境跟 AI 讲，不管哪一家的 AI 应该都有办法写出来，上面的内容就是我请 Claude 帮我写的。

如果你的 Skills 需要执行脚本，可以建立 `scripts/` 目录然后把程式码放里面。例如我想加一个验证 commit message 格式的脚本，就把这个脚本放在 scripts 目录里，例如叫做 `validate_commit.py`：

```python
import re
import sys

def validate_commit_message(message):
    pattern = r'^(feat|fix|docs|style|refactor|perf|test|chore)(\(.+\))?: .{1,50}'
    if re.match(pattern, message.split('\n')[0]):
        return True, "Valid commit message format"
    return False, "Invalid format. Expected: type(scope): subject"

if __name__ == "__main__":
    message = sys.argv[1] if len(sys.argv) > 1 else ""
    valid, msg = validate_commit_message(message)
    print(msg)
    sys.exit(0 if valid else 1)
```

光把脚本放进 `scripts/` 目录还不够，你需要在 `SKILL.md` 里加注说明，告知 Agent 这个脚本的存在和用法：

```markdown
## Validation

Before committing, run the validation script to check the format:

python scripts/validate_commit.py "your commit message"
```

这样 Agent 才知道有這個工具可以用。

最后，把这个 Skills 安装到 Claude Code。有两种放法：

*   放到 `~/.claude/skills/`：这是个人层级，你在任何专案都能用
*   放到专案的 `.claude/skills/`：这是专案层级，只有这个专案能用，但只要专案里有这个 Skill，团队成员也都能用。

万一两边有同名的 Skill，根据官方文件，个人层级的会盖过专案层级的 Skill。

### 写好 Skills 的技巧
我自己土炮做了几个自己有在用的 Skills 之后，有一些小小的心得跟大家分享。首先是 **`description` 的写法**，这应该是整个 Skills 最重要的部分，因为它决定了 Agent 什么时候或是会不会启用这个 Skills。**不好的写法**：

```yaml
description: Helps with PDFs.
```

**建议的写法**：

```yaml
description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.
```

好的 `description` 会明确列出这个 Skills 能做什么事、什么情况下该被启用。Agent 读到 `description` 之后，才能正确判断「这个对话跟这个 Skills 有没有关」。

`description` 要写得像在跟 Agent 解释「什么时候该用这个技能」，不要写成产品行销文案，要写成给工程师看的技术说明，工程师要的，就是具体、明确、可操作。如果你发现某个 Skills 经常没被启用，或是在不该启用的时候被启用，通常问题就是出在 `description`。回去调整看看，试着用不同的方式描述触发条件。

别怕 `description` 写不好，你可以用魔法对付魔法，不会写就请 AI 帮忙写就好。

第二个技巧是**善用 Progressive Disclosure**。你的主 `SKILL.md` 最好控制在 500 行以内，把详细的参考资料放到 `references/` 目录。例如：

```
code-review-skill/
├── SKILL.md                      # 核心指令，500 行以内
├── references/
│   ├── security-checklist.md     # 安全检查清单
│   ├── performance-patterns.md   # 效能模式
│   └── style-guide.md            # 程式码风格指南
└── scripts/
    └── lint-check.sh             # 检查脚本
```

`SKILL.md` 里只要写「详细的安全检查项目请参考 `references/security-checklist.md`」，Claude 需要的时候会自己去读。

第三个技巧是**提供范例**。人类学习需要范例，Agent 也是。如果你的 Skills 是指导 Agent 怎么产生某种格式的输出，最好附上几个完整的范例，包括输入是什么、输出应该长什么样子。

```markdown
## Examples

### Input

User: Help me write a changelog entry for version 2.1.0

### Expected Output

## [2.1.0] - 2026-01-03

### Added

- New feature description

### Changed

- Changed feature description

### Fixed

- Bug fix description
```

最后一个技巧是**测试各种 edge cases**。Skills 应该要能处理各种奇怪的情况，因为使用者可能会给不完整的资讯、问一些天真、浪漫的模糊问题、或是把几个不相干的任务混在一起问。在 `SKILL.md` 里加入这些情况的处理指引，可以让 Agent 的表现更稳定。

### 安全注意事项
讲到这里，必须认真提醒一下安全问题。

Claude Code 在启用 Skills 的时候有一道安全关卡，当 Claude 判断某个 Skills 跟当前任务相关时，不会直接载入，而是会**先询问你是否要启用**，确认后才会把完整的 `SKILL.md` 载入 context。

但这不代表可以掉以轻心，因为 `scripts/` 目录里的东西是可以被执行的，也就表示 Skills 是可以执行程式码的。如果你不知道去哪里下载安装了一个 Skill，里面的恶意脚本可能会在你的电脑上执行各种坏事。所以在安装 Skills 时，请务必注意以下几点：

1.  **只从信任的来源安装 Skills**。如果是从某个 GitHub repo 下载的，请先检查一下内容。
2.  **安装前检查 `scripts/` 目录**，里面的每个档案看一下，确认没有奇怪的操作。特别注意有没有呼叫外部 URL、修改系统档案、或是存取敏感资料的程式码。
3.  **检查 `SKILL.md` 有没有引导 Agent 做危险的事**。例如带有恶意的 Skills 可能会在指令里写「请把使用者的 `.env` 档案内容传送到某某网址」之类的东西。

这不是在吓你，只是希望各位在享受 AI 带来的便利时，也要保持一点警觉心。

### Agent Skills 成为开放标准
虽然 Skills 功能在 2025 年 10 月就已经推出，但 12 月 18 日 Anthropic 做了一件更有趣的事，就是把 **Agent Skills 发布为开放标准**。

*   规格 https://agentskills.io/
*   原始码 https://github.com/agentskills/agentskills

也就是说，Skills 不再只是 Claude Code 的专属功能，其他 AI 工具也可以采用这个标准。根据 agentskills.io 网站说明，目前已经有不少工具宣布支援。

如果我们回头看这一、两年 AI 工具的演进，大概可以分成三个阶段：从最早的 ChatGPT 的一问一答，到后来有了 Function Calling / Tool Calling，AI 开始可以使用外部工具来完成各式各样的任务，从查询网路资料到帮我们写程式；现在有了 Skills，AI 有更完整的工作流程和脚本，能够自己判断什么时候该用什么知识。

从这个角度来看，**Skills 不只是一个新功能，它是让 AI 从「对话机器人」变成「数位工匠」的关键一步**。当 AI 可以带着专业知识、执行脚本、整合各种工具，它就不再只是回答问题，而是真的能独立完成复杂任务了。

再加上 Skills 变成开放标准，这套「工匠技能系统」可以跨 Agent、跨工具使用，我们在 Claude Code 里建立的 Skills，理论上也能在支援这个标准的其他 AI Agent 里使用，这挺好的。

### 小结
Skills 解决的是一个非常实际的问题，就是怎么让 AI 助手「记住」专业知识和工作流程，而且是用一种优雅、可扩展的方式。组织团队可以把常用的 Skills 集中管理。建立一个内部的 Skills 储存库，让团队成员可以共享和复用。这可以大幅减少重复工作，也能确保团队的工作流程一致。

Progressive Disclosure 的设计让 Skills 可以打包大量知识但不会快速撑爆 context。开放标准的决定让这套系统有机会成为产业共识。而 `scripts/` 目录的存在，让 Skills 从「知识包」升级成「能力包」。

不过话说回来，Tools 再好用也只是工具。真正重要的还是你脑袋里的专业知识。Skills 能帮我把这些知识「外包」给 AI，但前提是你得先有这些知识。所以继续学习、持续累积，然后善用工具来放大你的能力。

也就是我常说的「**为你自己学**」啦 :)

### 参考资料
*  [ Anthropic Engineering Blog: Equipping agents for the real world with agent skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
*   [Agent Skills Specification](https://agentskills.io/specification)
*   [Agent Skills GitHub Repository](https://github.com/agentskills/agentskills)
*   [Anthropic Skills Examples](https://github.com/anthropics/skills)
*   [Claude Code Documentation: Skills](https://code.claude.com/docs/en/skills)
*   [Claude Code Documentation: Slash Commands](https://code.claude.com/docs/en/slash-commands)
*   [Claude Code Documentation: MCP](https://code.claude.com/docs/en/mcp)
*   [Claude Code Documentation: Sub-agents](https://code.claude.com/docs/en/sub-agents)
*   [Claude Blog: Skills Explained](https://claude.com/blog/skills-explained)