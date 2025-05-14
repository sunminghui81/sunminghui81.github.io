---
layout: post
keywords: AI 开发
description: AI 辅助开发编程
title: Vibe Coding
categories: [AI]
tags: [AI]
group: archive
icon: google
---

## 引言
随着软件开发和技术架构的不断进步，人工智能（AI）逐渐渗透到多个领域，尤其是软件工程和开发流程中。
AI辅助开发工具，如Cursor、MetaGPT等，已经成为提高开发效率、自动化任务和优化代码质量的关键力量。
这些工具能够智能化地帮助开发者进行代码生成、故障排查、测试优化等工作，从而减少人为错误和提高工作效率。
在现代开发环境中，如何有效利用AI工具来优化开发流程，尤其是在涉及复杂技术栈、分布式系统和高可用架构的项目中，已成为一个值得深思的话题。

## 概述
### 什么是Vibe Coding：定义和基本原则
Vibe Coding 是一种利用自然语言与人工智能（AI）结合来生成代码的全新编程方式。
开发者通过简单的自然语言输入描述需求，AI 会根据这些输入自动生成相应的代码，帮助开发者快速实现功能。
其核心理念是 “意图驱动开发”，开发者不再需要关心底层代码的实现，而是关注功能需求和代码的正确性。

### 自然语言编程：通过自然语言输入生成代码
Vibe Coding 的关键在于自然语言编程，即开发者用平常的语言表达需求，AI 自动将其转化为代码。
这一过程大大降低了开发门槛，使得即使是非技术背景的人也能够参与到开发过程中，减少了开发的复杂度。
例如，开发者可以告诉 AI：“创建一个登录界面，包含用户名和密码输入框，提交按钮，并进行基本的输入验证”，AI 会根据这些描述生成相应的 HTML 和 JavaScript 代码。

### 人工智能驱动：开发者与AI的协作过程
Vibe Coding 依赖于强大的人工智能技术，尤其是深度学习和自然语言处理（NLP）技术。
开发者的角色从编写代码转变为与 AI 的协作。AI 不仅生成初步代码，还能根据开发者的反馈进行优化、修改，甚至参与代码调试和测试。
AI 和开发者之间的合作是动态的，开发者可以实时提供反馈，AI 会根据这些反馈不断调整生成的代码，保证代码的质量和功能的实现。

### AI编程的模式与工具
https://sourcegraph.com/blog/levels-of-code-ai
下面level0-level4是人主导，level5是AI主导完成开发
1. Level 0 – 无 AI 协助：
开发者完全手动编写代码，AI 不参与任何生成或修改。此阶段包括基础的非 AI 辅助功能，如符号名称补全、悬停提示、定义和引用查找、自动格式化等。
- Visual Studio Code
- JetBrains 系列（如 IntelliJ IDEA、PyCharm）
- Git
- GitHub
- GitLab

2. Level 1 – 代码补全：
AI 根据上下文生成单行或整块代码，例如根据函数签名推测实现逻辑。此时，AI 仅在开发者的指引下执行单次操作，生成的代码仍需开发者验证。
- GitHub Copilot：由 OpenAI Codex 驱动，提供上下文相关的代码建议。
- Tabnine：支持多种编程语言的 AI 代码补全工具。
- JetBrains AI：集成于 JetBrains IDE 中，提供智能代码补全功能。
- Amazon CodeWhisperer：亚马逊推出的 AI 代码补全工具。
- Google Gemini Code Assist：谷歌推出的 AI 代码补全工具。

3. Level 2 – 代码生成：
AI 能够根据开发者指令，设计和实现 API、修复现有代码等，生成更长的代码块。开发者仍需审查 AI 生成的代码，但 AI 的理解能力和生成质量已显著提升。
- OpenAI Codex：支持多种编程语言的代码生成。
- Anthropic Sonnet：支持多种编程语言的代码生成。
- Replit Ghostwriter：Replit 提供的 AI 代码生成工具。
- FauxPilot：提供代码生成和补全功能的工具。

4. Level 3 – 监督自动化：
开发者提供高层次目标，AI 执行多步操作以实现目标，并具备一定的自我验证能力。例如，修复多种类型的 bug、添加新特性、与其他系统集成等。
- Sourcegraph Cody：提供代码生成、调试和文档生成等功能的 AI 助手。
- Cursor：AI 驱动的集成开发环境，支持代码生成和重构。
- Functionize：AI 驱动的自动化测试平台。
- Digital.ai Continuous Testing：提供持续集成和自动化测试的解决方案

5. Level 4 – 完全自动化：
AI 能够独立完成复杂任务，开发者无需审查最终代码输出。此阶段的 AI 类似于高级工程师，能够自主解决复杂问题。
- Builder.io Visual Copilot：将设计转化为代码的 AI 工具。
- StackAI：无代码 AI 平台，帮助构建 AI 代理自动化业务任务。

6. Level 5 – AI 主导的完全自主：
AI 不仅执行开发者定义的目标，还能自主设定目标，最大化某种基本奖励函数。这类似于人类大脑追求多巴胺或公司追求利润的方式。
- OpenAI Codex CLI：命令行界面的 AI 编程助手。
- Windsurf（原 Codeium）：AI 编程助手，支持多种编程语言。

** AI工具可能都是具备跨多个level能力的，整理一个多个典型工具支持的level，单独做一个页面 **

## 
