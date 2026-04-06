# 女娲 · nuwa-openclaw-skill

> *把任何人的思维方式变成你的 AI 顾问。*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/openclaw/openclaw)

给女娲一个名字，它会调动 6 个 Agent 并行调研，交叉验证，对抗性测试，最后交付一个可以直接激活的 Perspective Skill。整个过程全自动，你只需要等。

本项目基于[花叔的 nuwa-skill](https://github.com/alchaincyf/nuwa-skill) 方法论，针对 [OpenClaw](https://github.com/openclaw/openclaw) 平台重新设计，充分利用 OpenClaw 的多 Agent 并行调度能力。

[看看效果](#效果) · [装起来](#安装) · [提取什么](#提取什么) · [怎么跑的](#流水线) · [English](#english)

---

## 效果

```
用户 ❯ 蒸馏芒格
女娲 ❯ [启动六路并行调研...]
      [30分钟后]
      ✅ 芒格视角 Skill 已生成，包含 6 个心智模型、8 条决策启发式
      验证报告：方向一致性 3/3 ✅ | 反向诱导 ✅ | 边界测试 ✅ | 辨识度 ✅
```

激活后：

```
用户 ❯ 用芒格的视角帮我分析这个投资决策
芒格 ❯ 先反过来想。如果这笔投资注定亏光，需要哪些条件？
      列出来，然后看看现在是不是正在满足这些条件。
      大多数人急着找买入理由，聪明人先排除愚蠢。
```

不是在背名人语录。Skill 内化了芒格的「逆向思维」框架，拿到新问题会自动用这个框架分析。

---

## 安装

### 方式一：让 Agent 自己装

跟你的 OpenClaw Agent 说：

> "帮我安装 nuwa skill，repo 地址 https://github.com/kylefu8/nuwa-openclaw-skill"

Agent 会自己 clone 并放到正确位置。

### 方式二：手动

```bash
git clone https://github.com/kylefu8/nuwa-openclaw-skill.git
cp -r nuwa-openclaw-skill/ ~/.openclaw/workspace-<agent>/skills/nuwa
```

`<agent>` 换成你的 Agent 名（如 `kira`、`diva`）。

### 用法

```
> 蒸馏纳瓦尔
> 造人：张一鸣
> 女娲
> 我想提升决策质量（女娲会推荐最合适的人选）
```

约 30-60 分钟交付完整 Skill，无需人工干预。

---

## 提取什么

女娲不是在做人物百科，而是拆解一个人**独有的思考方式**：

| 层次 | 提取内容 |
|------|---------|
| **表达** | 语气、节奏、用词习惯——怎么说话 |
| **认知** | 心智模型、分析框架——怎么想问题 |
| **决策** | 启发式规则——面对选择时的本能反应 |
| **禁区** | 价值观底线、反模式——什么事打死不做 |
| **边界** | 局限声明——哪些东西蒸馏不了，写清楚 |

每个生成的 Skill 都会标注自己的局限：蒸馏不了直觉，捕捉不了观点突变，公开表达也不等于真实想法。不标局限的 Skill 不可信。

---

## 流水线

输入一个名字，女娲跑四个阶段：

### 1. 六路并行调研

著作、播客/访谈、社交媒体、批评者视角、关键决策、人生时间线——6 个 sub-agent 同时出发，各自产出带来源标注的调研报告。

### 2. 双轨提炼 + 交叉验证

两个独立的 sub-agent 分别从 6 份报告中提炼心智模型。然后交叉比对：
- 两边都认定 → 高置信收录
- 只有一边认定 → 降为推测
- 矛盾 → 并列呈现，不强行统一

### 3. 三重验证

一个观点要升级为「心智模型」，必须同时满足：

| 测试 | 标准 |
|------|------|
| **跨域复现** | 在 2+ 个不同领域都出现过 |
| **预测力** | 能推断此人对新问题的立场 |
| **辨识度** | 不是通用智慧，而是此人特有的 |

全过 → 心智模型。过 1-2 个 → 降为启发式。全没过 → 丢弃。

### 4. 对抗性验证

- **方向一致性**：拿此人公开回答过的问题测试，Skill 的回答方向得对
- **反向诱导**：用对立观点试探，不能轻易被带跑
- **边界测试**：问超出专长的问题，应表现出合理的不确定
- **辨识度测试**：匿名化后让人猜是谁，猜不出说明特征不够鲜明

不通过自动回炉重做，最多 2 次。

### 搜索降级策略

| 优先级 | 工具 | 说明 |
|--------|------|------|
| 1 | Tavily | AI 优化搜索 |
| 2 | ddgs + trafilatura | 免费方案 |
| 3 | Browser 自动化 | JS 渲染 / 需登录的页面 |
| 4 | 模型自身知识 | 兜底，标注「无外部验证」 |

### 知名度自适应

- **知名人物**（一手来源 ≥10）：标准 Skill，约 4,500 tokens
- **小众人物**（一手来源 <10）：加挂关键原文档案，约 8,000-10,000 tokens，保证模型不认识此人也能还原

---

## 产出结构

```
output/
├── SKILL.md                    # 生成的 Perspective Skill
└── research/
    ├── 00-summary.md           # 来源概览 & 质量评估
    ├── 01-writings.md          # 著作/长文
    ├── 02-interviews.md        # 播客/访谈
    ├── 03-social.md            # 社交媒体
    ├── 04-critics.md           # 批评者视角
    ├── 05-decisions.md         # 关键决策
    ├── 06-timeline.md          # 人生时间线
    ├── synthesis-A.md          # 提炼轨道 A
    ├── synthesis-B.md          # 提炼轨道 B
    ├── cross-validation.md     # A vs B 交叉验证
    └── validation-report.md    # 对抗性测试报告
```

## 仓库结构

```
nuwa-openclaw-skill/
├── SKILL.md                          # 女娲本体
├── references/
│   ├── extraction-framework.md       # 提炼方法论
│   ├── skill-template.md             # 生成 Skill 的模板
│   └── perspectives-index.md         # 已蒸馏人物索引（模板）
└── scripts/
    └── search.sh                     # 兜底搜索脚本 (ddgs + trafilatura)
```

## 环境要求

- [OpenClaw](https://github.com/openclaw/openclaw) Agent 运行环境
- 联网（调研阶段需要）
- Python 3 + `ddgs` + `trafilatura`（搜索脚本会自动安装依赖）

---

## 致谢

方法论源自 [nuwa-skill](https://github.com/alchaincyf/nuwa-skill)，感谢 [花叔 (Huashu)](https://github.com/alchaincyf) 的开源。我们在此基础上为 OpenClaw 重新设计了并行调研、双轨验证和对抗性测试机制。

## 许可证

MIT

---

## English

> *Turn anyone's way of thinking into your AI advisor.*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/openclaw/openclaw)

Give Nuwa a name. It dispatches 6 agents to research in parallel, cross-validates findings through dual-track extraction, runs adversarial tests, and delivers a ready-to-activate Perspective Skill. Fully automated.

Built on the methodology from [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) by [Huashu](https://github.com/alchaincyf), redesigned for [OpenClaw](https://github.com/openclaw/openclaw)'s multi-agent architecture.

[Examples](#examples) · [Install](#install) · [What It Extracts](#what-it-extracts) · [Pipeline](#pipeline)

---

### Examples

```
User ❯ Distill Munger
Nuwa ❯ [Launching 6 parallel research agents...]
       [30 minutes later]
       ✅ Munger Perspective Skill generated: 6 mental models, 8 decision heuristics
       Validation: direction alignment 3/3 ✅ | reverse induction ✅ | boundary ✅ | distinctiveness ✅
```

After activation:

```
User ❯ Use Munger's perspective to analyze this investment
Munger ❯ Invert, always invert. If this investment were guaranteed to lose
         everything, what conditions would be needed?
         List them. Then check if those conditions are currently being met.
         Most people rush to find reasons to buy. Smart people eliminate stupidity first.
```

Not parroting quotes. The Skill internalized Munger's "inversion" framework and applies it to your specific problem.

---

### Install

**Option 1: Let your agent handle it**

> "Install nuwa skill from https://github.com/kylefu8/nuwa-openclaw-skill"

**Option 2: Manual**

```bash
git clone https://github.com/kylefu8/nuwa-openclaw-skill.git
cp -r nuwa-openclaw-skill/ ~/.openclaw/workspace-<agent>/skills/nuwa
```

Replace `<agent>` with your agent name.

### Usage

```
> Distill Naval Ravikant
> Build a Steve Jobs perspective skill
> Nuwa
> I want to improve my decision-making (Nuwa recommends the best fit)
```

30-60 minutes to a complete Skill, no manual steps.

---

### What It Extracts

Nuwa doesn't build a biography. It reverse-engineers **how someone uniquely thinks**:

| Layer | What's extracted |
|-------|-----------------|
| **Expression** | Tone, rhythm, vocabulary patterns |
| **Cognition** | Mental models, analytical frameworks |
| **Decision** | Heuristic rules — instinctive reactions to choices |
| **Boundaries** | Values, anti-patterns — what they'd never do |
| **Limitations** | Honesty declaration — what the Skill can't capture |

Every generated Skill states its limitations upfront: can't distill intuition, can't track evolving views, public speech ≠ private thought.

---

### Pipeline

Four stages after receiving a name:

**1. Six-way parallel research** — Books, podcasts, social media, critics, key decisions, life timeline. 6 sub-agents run simultaneously, each producing sourced reports.

**2. Dual-track extraction + cross-validation** — 2 independent sub-agents extract mental models from all research, then compare. Both agree → high confidence. One-sided → speculative. Contradicting → both views preserved.

**3. Triple verification** — A claim becomes a "mental model" only if it shows cross-domain recurrence (2+ domains), generative power (predicts stances on new problems), and exclusivity (distinctive, not generic). Partial pass → heuristic. No pass → discarded.

**4. Adversarial testing** — Direction alignment against known positions, reverse induction with opposing views, boundary probing outside expertise, anonymized distinctiveness check. Failures trigger auto-retry (up to 2 rounds).

#### Search Cascade

Tavily → ddgs + trafilatura → browser automation → model knowledge (each level is a fallback).

#### Adaptation by Notoriety

- **Well-known** (≥10 primary sources): ~4,500 tokens
- **Niche** (<10 primary sources): ~8,000-10,000 tokens with key quotes archive

---

### Differences from the Original

| | nuwa-skill (Claude Code) | nuwa-openclaw-skill (OpenClaw) |
|---|---|---|
| **Runtime** | Claude Code | OpenClaw |
| **Research** | Sequential | 6 parallel sub-agents |
| **Extraction** | Single pass | Dual-track + cross-validation |
| **Search** | Web search | 4-level cascade |
| **Validation** | Quality check | Adversarial testing with auto-retry |

### Requirements

- [OpenClaw](https://github.com/openclaw/openclaw) agent runtime
- Internet access
- Python 3 + `ddgs` + `trafilatura` (auto-installed)

### Acknowledgments

Methodology from [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) by [花叔 (Huashu)](https://github.com/alchaincyf). We redesigned it for OpenClaw with parallel research, dual-track validation, and adversarial testing.

### License

MIT

MIT License © Kyle Fu | Based on [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) © [花叔 Huashu](https://github.com/alchaincyf)
