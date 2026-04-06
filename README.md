# 女娲 · nuwa-openclaw-skill

> *蒸馏任何人的思维方式，生成可运行的 AI 人格。*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/openclaw/openclaw)

**女娲帮你蒸馏任何人的思维方式——心智模型、决策启发式、表达DNA——让芒格、费曼、乔布斯都给你打工。**

[花叔的 nuwa-skill](https://github.com/alchaincyf/nuwa-skill) 证明了蒸馏一个人的思维方式是完全可行的。这个项目把方法论适配到了 [OpenClaw](https://github.com/openclaw/openclaw) 平台，利用 OpenClaw 的多 Agent 并行能力，实现六路调研 → 双轨提炼 → 交叉验证 → 对抗性测试的全自动流水线。

[效果示例](#效果示例) · [安装](#安装) · [女娲蒸馏了什么](#女娲蒸馏了什么) · [工作原理](#工作原理) · [English](#english)

---

## 效果示例

```
用户 ❯ 蒸馏芒格
女娲 ❯ [启动六路并行调研...]
      [30分钟后]
      ✅ 芒格视角 Skill 已生成，包含 6 个心智模型、8 条决策启发式
      验证报告：方向一致性 3/3 ✅ | 反向诱导 ✅ | 边界测试 ✅ | 辨识度 ✅
```

生成的 Skill 激活后：

```
用户 ❯ 用芒格的视角帮我分析这个投资决策
芒格 ❯ 先反过来想。如果这笔投资注定亏光，需要哪些条件？
      列出来，然后看看现在是不是正在满足这些条件。
      大多数人急着找买入理由，聪明人先排除愚蠢。
```

这不是角色扮演复读名人语录——是用他们的**认知框架**帮你分析问题。

---

## 安装

### 方式一：让你的 OpenClaw Agent 自己装

直接跟你的 Agent 说：

> "帮我安装 nuwa skill，repo 地址 https://github.com/kylefu8/nuwa-openclaw-skill"

Agent 会自动 clone 并安装到正确的 skills 目录。

### 方式二：手动安装

```bash
git clone https://github.com/kylefu8/nuwa-openclaw-skill.git
cp -r nuwa-openclaw-skill/ ~/.openclaw/workspace-<agent>/skills/nuwa
```

把 `<agent>` 替换成你的 Agent 名称（如 `kira`、`diva`）。

### 使用

安装后直接跟 Agent 说：

```
> 蒸馏纳瓦尔
> 造人：张一鸣
> 女娲
> 我想提升决策质量（女娲会自动推荐最合适的人选）
```

女娲全自动完成调研、提炼、验证，约 30-60 分钟交付完整 Skill。

---

## 女娲蒸馏了什么

蒸馏各领域最强的人，需要提取比日常工作习惯更深的东西。女娲提取五层：

| 层次 | 说明 |
|------|------|
| **怎么说话** | 表达DNA——语气、节奏、用词偏好 |
| **怎么想** | 心智模型、认知框架 |
| **怎么判断** | 决策启发式——"如果X则Y" |
| **什么不做** | 反模式、价值观底线 |
| **知道局限** | 诚实边界——做不到什么写清楚 |

工作习惯可以靠流程文档传递，但让芒格和马斯克面对同一个问题做出不同判断的，是认知框架。女娲提取的是认知操作系统。

### 诚实边界

每个 Skill 都明确标注做不到什么：

- 蒸馏不了直觉——框架能提取，灵感不能
- 捕捉不了突变——截止到调研时间的快照
- 公开表达 ≠ 真实想法——只能基于公开信息

**一个不告诉你局限在哪的 Skill，不值得信任。**

---

## 工作原理

输入一个名字后，女娲做四件事：

**1. 六路并行调研** — 著作、播客/访谈、社交媒体、批评者视角、决策记录、人生时间线，6 个 sub-agent 同时跑，各自存档。

**2. 双轨提炼 + 交叉验证** — 两个独立 sub-agent 各自提炼心智模型，然后交叉比对。都认定的 → 高置信收录；只有一方认定 → 降级为推测；矛盾 → 并列呈现。

**3. 三重验证筛选** — 一个观点要被收录为心智模型，必须：

| 验证 | 要求 |
|------|------|
| **跨域复现** | 在 2+ 个不同领域出现过 |
| **生成力** | 能推断对新问题的立场 |
| **排他性** | 不是所有聪明人都这么想 |

三个都过 → 心智模型。1-2 个过 → 降级为启发式。0 个 → 丢弃。

**4. 对抗性验证** — 方向一致性测试（对比已知立场）、反向诱导（用反对观点试探）、边界测试（超出专长领域）、辨识度测试（匿名化后能否认出）。不通过自动回炉，最多重试 2 次。

### 搜索工具四级降级

| 优先级 | 工具 | 说明 |
|--------|------|------|
| 1 | Tavily | AI 优化搜索，速度最快 |
| 2 | ddgs + trafilatura | 免费，无 API key |
| 3 | Browser 自动化 | JS 渲染页面、需要登录态 |
| 4 | 模型知识 | 兜底，标注"无外部验证" |

### 知名度自适应

- **知名人物**（一手来源 ≥10）：标准 Skill（~4,500 tokens）
- **小众人物**（一手来源 <10）：扩展版 Skill + 关键原文档案（~8,000-10,000 tokens），确保模型即使不认识此人也能准确还原

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
│   └── perspectives-index.md         # 已蒸馏人物索引
└── scripts/
    └── search.sh                     # 兜底搜索脚本 (ddgs + trafilatura)
```

## 环境要求

- [OpenClaw](https://github.com/openclaw/openclaw) Agent 运行环境
- 联网（调研阶段需要）
- Python 3 + `ddgs` + `trafilatura`（搜索兜底脚本会自动安装）

---

## 致谢

本项目受 [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) 启发，感谢 [花叔 (Huashu)](https://github.com/alchaincyf) 开源的原版女娲方法论。本项目将其适配到 [OpenClaw](https://github.com/openclaw/openclaw) 平台，增加了多 Agent 并行调研、双轨交叉验证和对抗性测试。

## 许可证

MIT — 随便用，随便改，随便造。

---

## English

> *"Distill anyone's thinking into a runnable AI persona."*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/openclaw/openclaw)

**Nuwa distills anyone's thinking — mental models, decision heuristics, expression DNA — so Munger, Feynman, and Jobs can work for you.**

[nuwa-skill](https://github.com/alchaincyf/nuwa-skill) by Huashu proved that distilling how someone thinks is entirely viable. This project adapts the methodology for [OpenClaw](https://github.com/openclaw/openclaw), leveraging its multi-agent parallelism: 6-way research → dual-track extraction → cross-validation → adversarial testing, fully automated.

[Examples](#examples) · [Install](#install) · [What It Extracts](#what-nuwa-extracts) · [How It Works](#how-it-works-1)

---

### Examples

```
User ❯ Distill Munger
Nuwa ❯ [Launching 6 parallel research agents...]
       [30 minutes later]
       ✅ Munger Perspective Skill generated: 6 mental models, 8 decision heuristics
       Validation: direction alignment 3/3 ✅ | reverse induction ✅ | boundary ✅ | distinctiveness ✅
```

After activating the generated Skill:

```
User ❯ Use Munger's perspective to analyze this investment
Munger ❯ Invert, always invert. If this investment were guaranteed to lose
         everything, what conditions would be needed?
         List them. Then check if those conditions are currently being met.
         Most people rush to find reasons to buy. Smart people eliminate stupidity first.
```

This isn't role-playing or parroting quotes — it's analyzing your problem through their **cognitive frameworks**.

---

### Install

**Option 1: Let your OpenClaw agent install it**

Just tell your agent:

> "Install nuwa skill from https://github.com/kylefu8/nuwa-openclaw-skill"

The agent will clone and install it to the correct skills directory.

**Option 2: Manual install**

```bash
git clone https://github.com/kylefu8/nuwa-openclaw-skill.git
cp -r nuwa-openclaw-skill/ ~/.openclaw/workspace-<agent>/skills/nuwa
```

Replace `<agent>` with your agent name (e.g. `kira`, `diva`).

### Usage

After installation, just tell your agent:

```
> Distill Naval Ravikant
> Build a Steve Jobs perspective skill
> Nuwa
> I want to improve my decision-making (Nuwa recommends the best thinker for you)
```

Nuwa handles everything autonomously — research, extraction, validation — and delivers a complete skill in 30-60 minutes.

---

### What Nuwa Extracts

Distilling the best minds requires extracting something deeper than daily work habits. Nuwa extracts five layers:

| Layer | Description |
|-------|-------------|
| **How they speak** | Expression DNA — tone, rhythm, vocabulary |
| **How they think** | Mental models, cognitive frameworks |
| **How they decide** | Decision heuristics — "if X then Y" |
| **What they refuse** | Anti-patterns, value boundaries |
| **What they can't do** | Honesty boundaries — limitations stated clearly |

Work habits transfer through process docs. But what makes Munger and Musk reach different conclusions on the same problem is cognitive architecture. Nuwa extracts the cognitive operating system.

#### Honesty Boundaries

Every Skill explicitly states what it can't do:

- Can't distill intuition — frameworks yes, inspiration no
- Can't capture change — it's a snapshot as of research date
- Public expression ≠ private thought — based on public information only

**A Skill that doesn't tell you its limitations isn't worth trusting.**

---

### How It Works

After receiving a name, Nuwa does four things:

**1. Six-way parallel research** — Books, podcasts/interviews, social media, critics, key decisions, life timeline. 6 sub-agents run simultaneously, each producing a sourced report.

**2. Dual-track extraction + cross-validation** — 2 independent sub-agents each extract mental models from all 6 research reports, then cross-compare. Both agree → high confidence. Only one → downgraded to speculative. Contradiction → both views presented.

**3. Triple verification** — A claim becomes a "mental model" only if it passes all three:

| Test | Requirement |
|------|-------------|
| **Cross-domain recurrence** | Appears in 2+ different domains |
| **Generative power** | Can predict their stance on new problems |
| **Exclusivity** | Not generic wisdom — distinctive to this person |

Pass all three → mental model. Pass 1-2 → downgraded to heuristic. Pass 0 → discarded.

**4. Adversarial validation** — Direction alignment (compare against known positions), reverse induction (test with opposing views), boundary testing (questions outside their expertise), distinctiveness testing (can you identify who it is anonymously?). Failures trigger auto-retry, up to 2 rounds.

#### Search Tool Cascade

| Priority | Tool | Description |
|----------|------|-------------|
| 1 | Tavily | AI-optimized search, fastest |
| 2 | ddgs + trafilatura | Free, no API key needed |
| 3 | Browser automation | JS-rendered / login-required pages |
| 4 | Model knowledge | Fallback, labeled as unverified |

#### Celebrity vs. Niche Adaptation

- **Well-known figures** (10+ primary sources): Standard Skill (~4,500 tokens)
- **Niche figures** (<10 primary sources): Extended Skill + key quotes archive (~8,000-10,000 tokens), ensuring accurate simulation even when the model doesn't recognize the person

---

### Output Structure

```
output/
├── SKILL.md                    # The generated Perspective Skill
└── research/
    ├── 00-summary.md           # Source overview & quality assessment
    ├── 01-writings.md          # Books, essays, long-form
    ├── 02-interviews.md        # Podcasts, talks, interviews
    ├── 03-social.md            # Twitter/X, social platforms
    ├── 04-critics.md           # Criticism & counter-perspectives
    ├── 05-decisions.md         # Key decisions & reasoning
    ├── 06-timeline.md          # Life timeline & turning points
    ├── synthesis-A.md          # Extraction track A
    ├── synthesis-B.md          # Extraction track B
    ├── cross-validation.md     # A vs B comparison
    └── validation-report.md    # Adversarial test results
```

### Repository Structure

```
nuwa-openclaw-skill/
├── SKILL.md                          # Nuwa core skill definition
├── references/
│   ├── extraction-framework.md       # Extraction methodology
│   ├── skill-template.md             # Output template for generated skills
│   └── perspectives-index.md         # Registry of distilled personas
└── scripts/
    └── search.sh                     # Fallback search script (ddgs + trafilatura)
```

### Requirements

- [OpenClaw](https://github.com/openclaw/openclaw) agent runtime
- Internet access (for research phase)
- Python 3 + `ddgs` + `trafilatura` (auto-installed by fallback search script)

---

### Acknowledgments

Inspired by [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) by [花叔 (Huashu)](https://github.com/alchaincyf) — the original Nuwa for Claude Code. This project adapts the methodology for [OpenClaw](https://github.com/openclaw/openclaw) with parallel sub-agent research, dual-track cross-validation, and adversarial testing.

### Key Differences from the Original

| | nuwa-skill (Claude Code) | nuwa-openclaw-skill (OpenClaw) |
|---|---|---|
| **Runtime** | Claude Code | OpenClaw |
| **Research** | Sequential | 6 parallel sub-agents |
| **Extraction** | Single track | Dual-track + cross-validation |
| **Search** | Web search | 4-level cascade (Tavily → ddgs → browser → model) |
| **Validation** | Quality check | Adversarial testing with auto-retry |

### License

MIT — use it, modify it, build with it.

MIT License © Kyle Fu | Inspired by [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) © [花叔 Huashu](https://github.com/alchaincyf)
