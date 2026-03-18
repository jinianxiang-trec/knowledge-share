---
marp: true
theme: uncover
class: invert
paginate: true
footer: "SDD 规格驱动开发 · 团队内部分享 · 2026.03"
style: |
  :root {
    --color-background: #0f1117;
    --color-foreground: #e8eaf0;
    --color-highlight: #7c6af7;
    --color-accent: #f06292;
    --color-success: #56c4a8;
    --color-warning: #ffca28;
  }
  section {
    background: #0f1117;
    color: #e8eaf0;
    font-family: 'PingFang SC', 'Noto Sans SC', 'Segoe UI', sans-serif;
    font-size: 1.1rem;
    padding: 50px 64px;
  }
  h1 { color: #7c6af7; font-size: 2.2rem; border-bottom: 2px solid #7c6af7; padding-bottom: 8px; }
  h2 { color: #a29bfe; font-size: 1.7rem; }
  h3 { color: #56c4a8; font-size: 1.3rem; }
  strong { color: #ffca28; }
  em { color: #f06292; }
  code { background: #1e2130; color: #a8dadc; border-radius: 4px; padding: 2px 6px; font-size: 0.95rem; }
  pre { background: #1e2130; border-left: 4px solid #7c6af7; border-radius: 8px; padding: 16px; }
  blockquote { border-left: 4px solid #7c6af7; background: #1a1d2e; padding: 12px 20px; border-radius: 0 8px 8px 0; color: #b0b8d0; font-style: normal; }
  ul li::marker { color: #7c6af7; }
  footer { color: #555c7a; font-size: 0.75rem; }
  section.cover { text-align: center; }
  section.cover h1 { border-bottom: none; font-size: 2.8rem; margin-bottom: 0.3em; }
  .tag { display: inline-block; background: #7c6af7; color: white; border-radius: 4px; padding: 2px 10px; font-size: 0.8rem; margin: 2px; }
  table { border-collapse: collapse; width: 100%; }
  th { background: #1e2130; color: #a29bfe; padding: 8px 14px; }
  td { padding: 6px 14px; border-bottom: 1px solid #2a2e40; }
---

<!-- _class: cover invert -->

# SDD 规格驱动开发
## 从"凭感觉"走向"AI 辅助工程化"

<br>

> 📅 2026.03 · 团队内部知识分享

---

## 😤 痛点：当 AI 说"完成了！"

你让 AI 做一个登录功能……

- ✅ 有表单、有验证逻辑、设计专业
- ❌ 登录成功后**不跳转页面**
- 🔧 修了跳转 → 坏了密码验证
- 🔧 修了密码 → 又改坏了别的地方
- 😮 架构陌生、包没见过、看不懂维护

<br>

> 这不是你的问题，也不是 AI 的问题——**这是方法论的问题。**

---

## 🎢 Vibe Coding 的甜蜜与危险

**凭感觉写代码**：聊着聊着，代码就写出来了。

✅ **甜蜜点**：快速 PoC / 脚本工具 / 探索新技术

❌ **危险区（项目变大时）**：

| 问题 | 根因 |
|------|------|
| 代码风格不一致 | 三次对话 = 三种架构 |
| 遗漏边界条件 | 你没提，AI 就不做 |
| 盲目自信 | "测试没过？先跳过了" |

---

## 💡 什么是 SDD？

**Spec-Driven Development · 规格驱动开发**

> 在写代码之前，先把规格写清楚——让规格成为人类与 AI 之间的**共同语言**。

不是冗长的瀑布式文档 📚  
而是可以持续演进的**共识基础** 🔄

<br>

`SDD ≠ 更多文档` &nbsp;&nbsp; `SDD = 更清晰的对话`

---

## 🔄 SDD 的三个黄金阶段

```
需求 → 设计 → 任务
```

**📝 1. Requirements（需求）**
用户故事 + 验收标准 → `requirements.md`

**📐 2. Design（设计）**  
架构图 / 数据模型 / 错误处理 → `design.md`

**✅ 3. Tasks（任务）**  
足够小的可追踪任务，每个任务关联需求编号 → `tasks.md`

---

## 📏 EARS：让需求变"可执行"

**Easy Approach to Requirements Syntax**  
源自 Rolls-Royce / NASA / 空客的工程实践（2009）

```
当 [触发条件] 时
则 系统应 [预期行为]
```

**示例：**
```
当 用户输入正确的邮箱和密码时
则 系统应将用户重定向到首页
```

> **好处**：强迫想清楚边界 · 容易转为测试用例 · 减少 AI 猜测空间

---

## 📊 SDD 的三个实践层级

| 层级 | 规格的命运 | 状态 |
|------|-----------|------|
| **Spec-first** 规格优先 | 写完即抛弃 | 🟢 主流入门 |
| **Spec-anchored** 规格锚定 | 进入版本控制，随代码演进 | 🌟 **推荐实践** |
| **Spec-as-source** 规格即源码 | 代码完全由规格自动生成 | 🔮 未来愿景 |

---

## 🛠️ 工具生态一览

- **Kiro** *(Amazon)* — Requirements → Design → Tasks，EARS + 自动测试钩子
- **Spec Kit** *(GitHub)* — Constitution → Specify → Plan → Tasks，高可定制
- **Tessl** — 追求 Spec-as-source，内置开源库的 usage specs 规格库
- **OpenSpec** — 轻量级，区分 `specs/`（真相来源）和 `changes/`（变更提案），对既有项目最友好

---

## ⚠️ SDD 的局限：诚实面对

1. 🐢 **速度变慢** — 小工具直接 Vibe Coding 更快
2. 🎭 **角色转变** — 工程师变成"产品经理兼监工"
3. 📚 **学习成本** — 写好规格和写好代码一样需要练习
4. 🔀 **工具混战** — 生态尚未稳定，核心是掌握**思维**而非绑定工具

---

## 🎯 三大适用场景

**🌱 0-to-1 全新项目**  
从源头打好地基，避免后期重构债务

**🔭 探索性开发**  
同一份规格让 AI 尝试多种技术栈，选最优方案

**🔧 渐进式改进（最有价值）**  
对既有系统加功能，规格锚定边界，防止 AI 误伤现有代码

---

<!-- _class: cover invert -->

## 核心原则

# 在开始之前，先定义什么叫"完成"

<br>

下次 AI 跟你说**"做完了！"**的时候  
请微笑着反问它一句：

## 🤖 &nbsp; "完成了**什么标准**？"

---

<!-- _class: cover invert -->

# Q & A

**感谢聆听，欢迎交流 🙌**

<br>

`#SDD` &nbsp; `#AI驱动开发` &nbsp; `#开发方法论`
