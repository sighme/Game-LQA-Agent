# Game-LQA-Agent 🎮

> 基于大语言模型（LLM）与多 Agent 协作的独立游戏本地化（Localization）自动 QA 流水线。
> A multi-agent workflow for automated game localization and LQA based on LLMs.

## 📖 项目背景与核心痛点

在独立游戏汉化/本地化过程中，文本量通常高达数十万词，且深度耦合在 JSON/XML 等包含大量变量、换行符（如 \n、[INPUTSTEPS]）的代码文件中。

传统的 CAT（计算机辅助翻译）工具（如 memoQ、Trados）的内置 QA 功能，仅能进行基于硬规则的检查（如漏译、多余空格、标点错误），无法解决以下深层语义问题：
1. “机翻腔”与死译：翻译文理不通或生硬，缺乏游戏沉浸感。
2. 语境错误：脱离游戏 Lore（世界观）设定的词汇误用。
3. 人工成本极高：全量双语逐句对齐审阅耗时极其漫长。

本项目旨在构建一个“代码解析 + 大模型语义推理”的闭环工作流，将本地化审校效率提升 80% 以上。

---

## ⚙️ 核心架构与 Agent 工作流

本项目采用模块化的 Multi-Agent 设计，主要包含以下核心工作流：

### 1. 📂 预处理与解析 Agent (Parser Agent)
- 自动读取底层 cutscene.json 等本地化文件。
- 精准提取源语言（ENG）与目标语言（ZHO）的键值对（Key-Value）。
- 过滤掉非文本类的纯代码字段，降低 LLM 处理的 Token 噪音。

### 2. 🧠 语义审查与润色 Agent (Semantic Review Agent)
- 长上下文注入：载入游戏角色设定档案与专有名词表（Glossary）作为 System Prompt。
- 深度推理：对比中英双语，进行“信达雅”评估，精准定位死译、生硬表达或情感色彩不符的句段。
- 动态建议：不仅指出错误，更直接提供符合语境的修改建议。

### 3. 🛡️ 代码标签校验 Agent (Tag & Variable Checker)
- 作为最后的安全屏障，交叉验证 LLM 提供的修改建议。
- 确保游戏引擎所需的变量（如 {playerName}）和转义字符（如 \n）未被大模型意外破坏或篡改，防止游戏运行时崩溃。

### 4. 📊 闭环输出与反馈 (Report Generator)
- 将审查结果自动结构化输出为 Excel (.xlsx) 双语审查报告。
- 译者可直接根据报告在 memoQ 等工具中进行针对性修改。

---

## 🚀 预期产出示例 (Output Example)

Agent 生成的自动 LQA 报告结构如下：

| 句段 ID | 原文 (Source) | 当前译文 (Target) | 错误类型 / 警告 | Agent 优化建议 |
| :--- | :--- | :--- | :--- | :--- |
| tip12 | Press 'R' to whistle and call Drayton to you. | 按 R 键吹口哨并呼叫 Drayton 给你。 | 🤖 机翻腔/死译 | 按下“R”键吹口哨，就能把 Drayton 叫到你身边。 |
| 1781 | Just hold down [INPUTSTEPS] while moving... | 只要在移动时按住[INPUTSTEPS]... | ⚡ 标签格式警告 | (保留代码变量) 移动时只需按住 [INPUTSTEPS] 即可... |

---

## 🛠️ 技术栈 (Tech Stack)

- 数据处理: Python, json, pandas (Excel 交互)
- CAT 工具对接: memoQ (支持双语 RTF/Excel 导出解析)
- 底层驱动: 大语言模型 API (计划深度接入 Xiaomi MiMo 系列模型，利用其卓越的中文理解力与长上下文推理能力降低大规模处理成本)

---

## 📅 未来规划 (Roadmap)

- [x] 完成基础 JSON 文本提取与结构化对齐。
- [x] 确立多 Agent 提示词（Prompt）架构与本地化审查规范。
- [ ] 接入 MiMo / 主流大语言模型 API 实现自动化批量处理。
- [ ] 开发轻量级可视化 UI，方便非技术译者一键拖拽审查。
