# 女娲 · nuwa-openclaw-skill

> *蒸馏任何人的思维方式，生成可运行的 AI 人格。*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-blue)](https://github.com/openclaw/openclaw)

**女娲帮你蒸馏任何人的思维方式——心智模型、决策启发式、表达DNA——让芒格、费曼、乔布斯都给你打工。**

[花叔的 nuwa-skill](https://github.com/alchaincyf/nuwa-skill) 证明了蒸馏一个人的思维方式是完全可行的。这个项目把方法论适配到了 [OpenClaw](https://github.com/openclaw/openclaw) 平台，利用 OpenClaw 的多 Agent 并行能力，实现六路调研 → 双轨提炼 → 交叉验证 → 对抗性测试的全自动流水线。

[效果示例](#效果示例) · [安装](#安装) · [工作原理](#工作原理) · [English](#english)

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

| 层次 | 说明 |
|------|------|
| **怎么说话** | 表达DNA——语气、节奏、用词偏好 |
| **怎么想** | 心智模型、认知框架 |
| **怎么判断** | 决策启发式——"如果X则Y" |
| **什么不做** | 反模式、价值观底线 |
| **知道局限** | 诚实边界——做不到什么写清楚 |

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

- **知名人物**（一手来源 ≥10）：标准 Skill（~3K tokens）
- **小众人物**（一手来源 <10）：扩展版 Skill + 原文档案（~6-8K tokens），确保模型即使不认识此人也能准确还原

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

**[nuwa-skill](https://github.com/alchaincyf/nuwa-skill)** proved that distilling how someone thinks is viable. **nuwa-openclaw-skill** adapts the methodology for [OpenClaw](https://github.com/openclaw/openclaw) agents, leveraging OpenClaw's multi-agent parallelism for automated research → extraction → cross-validation → adversarial testing.

**Install**: Tell your OpenClaw agent `"Install nuwa skill from https://github.com/kylefu8/nuwa-openclaw-skill"`, or manually:

```bash
git clone https://github.com/kylefu8/nuwa-openclaw-skill.git
cp -r nuwa-openclaw-skill/ ~/.openclaw/workspace-<agent>/skills/nuwa
```

**How it works**: Input a name → 6 parallel research agents → dual-track extraction with cross-validation → triple-verified mental models → adversarial testing → ready-to-use Perspective Skill.

**What it extracts**: Mental models (3-7), decision heuristics (5-10), expression DNA, values & anti-patterns, honesty boundaries — all sourced and verifiable.

**Key differences from the original**:
- Runs on OpenClaw (not Claude Code)
- 6 parallel sub-agents for research (vs sequential)
- Dual-track extraction with cross-validation (2 independent synthesizers)
- 4-level search fallback (Tavily → ddgs → browser → model knowledge)
- Adversarial validation with auto-retry

See the Chinese README above for examples, methodology details, and output structure.

MIT License © Kyle Fu | Inspired by [nuwa-skill](https://github.com/alchaincyf/nuwa-skill) © [花叔 Huashu](https://github.com/alchaincyf)
