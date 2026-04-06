---
name: nuwa
description: |
  蒸馏任何人的思维方式——心智模型、决策启发式、表达DNA——生成可运行的 OpenClaw Perspective Skill。
  全自动流水线：六路并行调研 → 双轨提炼 → 溯源验证 → 对抗性测试 → 产出。
  触发词：「蒸馏XX」「造人」「女娲」「XX的思维方式」「做个XX视角的skill」「perspective skill」。
  模糊需求也触发：「我想提升决策质量」「有没有一种思维方式能帮我…」「我需要一个思维顾问」。
---

# 女娲 · 思维蒸馏术

> 捕捉 HOW they think，不是 WHAT they said。

## 导航

- 方法论 → `references/extraction-framework.md`
- 产出模板 → `references/skill-template.md`
- 搜索脚本 → `scripts/search.sh`（Tavily 不可用时的 fallback）

## Phase 0: 入口分流

| 输入类型 | 路径 |
|---------|------|
| 明确人名（"蒸馏芒格"） | → Phase 1 |
| 模糊需求（"提升决策质量"） | → 搜索推荐 1-3 人选，自主选最佳 → Phase 1 |
| 主题（"X运营方法论"） | → Phase 1（主题模式，见方法论） |

## Phase 1: 六路并行调研

创建输出目录 `{outputDir}/research/`，用 `sessions_spawn` 起 6 个 subagent 并行：

| # | 维度 | 产出 | 搜索词示例 |
|---|------|------|-----------|
| 1 | 著作/长文 | `01-writings.md` | "[人名] book/著作/essay" |
| 2 | 播客/访谈 | `02-interviews.md` | "[人名] interview/podcast/演讲" |
| 3 | 社交媒体 | `03-social.md` | "[人名] twitter/X/知乎/公众号" |
| 4 | 批评者视角 | `04-critics.md` | "[人名] criticism/争议/wrong" |
| 5 | 关键决策 | `05-decisions.md` | "[人名] decision/为什么[事件]" |
| 6 | 人生时间线 | `06-timeline.md` | "[人名] biography/经历/timeline" |

**搜索工具四级降级**（写进每个 subagent 的 task）：
1. **tavily_search + tavily_extract** — 首选，速度最快，AI 优化摘要
2. **scripts/search.sh**（ddgs + trafilatura）— tavily 报错时自动切换
   - 搜索：`bash {skillDir}/scripts/search.sh "查询词"`
   - 搜索+提取：`bash {skillDir}/scripts/search.sh "查询词" --extract 3`
   - 单 URL 提取：`bash {skillDir}/scripts/search.sh --extract "https://..."`
   - 免费无限用，无 API key 依赖
3. **browser tool**（navigate + snapshot）— 需要动态渲染/登录态/JS 页面时使用
   - 小红书、微信公众号、B站等需要真实浏览器的平台
   - 也可用于 Google/Bing 搜索作为兜底
4. **兜底**：以上都失败时，用模型自身知识 + 明确标注"无外部来源验证"

**降级触发规则**：每个工具尝试 1 次，报错/超时/空结果 → 自动降级到下一级。不要在同一级重试超过 1 次。

**每个 subagent 产出标准**：
- 至少 5 个高质量来源，标注 URL
- 原文引用用 `>` 格式
- 标注一手（本人产出）vs 二手（他人分析）
- 文件末尾：来源清单 + 信息丰富度自评（充足/一般/稀缺）

**Phase 1 结束后**自动生成 `research/00-summary.md`：
- 总来源数（一手 vs 二手）
- 每个维度信息丰富度
- 信息空白点
- 去重 URL 清单
- **知名度评估**：根据来源数量和质量判断
  - **知名人物**（一手来源 ≥10，维基百科/大量书籍）→ 标准模式
  - **小众人物**（一手来源 <10，无维基百科）→ 深度模式，Phase 3 自动嵌入原文

## Phase 2: 双轨提炼（交叉验证）

读取 `references/extraction-framework.md`。

**起 2 个独立 subagent（A 和 B），各自读取全部 6 份调研素材，独立提炼**：

每个 subagent 提炼：
1. **心智模型**（3-7个）— 三重验证：跨域复现 + 生成力 + 排他性
2. **决策启发式**（5-10条）— "如果X则Y"，有案例
3. **表达DNA** — 句式/词汇/节奏/幽默/确定性/引用习惯
4. **价值观与反模式** — 追求/拒绝/内在矛盾
5. **智识谱系** — 影响者 → 此人 → 被影响者
6. **诚实边界** — 做不到什么、信息不足的维度

产出：`research/synthesis-A.md` 和 `research/synthesis-B.md`

**交叉比对**（主 agent 执行）：
| 情况 | 处理 |
|------|------|
| A、B 都认定的心智模型 | ✅ 高置信度收录 |
| 只有一方认定 | ⚠️ 标注为"推测性"，降级为启发式或附注 |
| A、B 矛盾 | 并列呈现两种解读，标注分歧 |

比对结果写入 `research/cross-validation.md`。

## Phase 3: 构建 Skill

读取 `references/skill-template.md`，填入 Phase 2 交叉验证后的结果。

**溯源锁定规则**：Skill 中每个心智模型和启发式，必须附带 `research/` 中的来源引用编号。无来源支撑的观点 → 删除或标注为推测。

**知名度自适应**（根据 `00-summary.md` 的知名度评估）：
- **知名人物**：标准 Skill（~4,500 tokens）
- **小众人物**：扩展版 Skill + 关键原文档案（~8,000-10,000 tokens），确保模型即使不认识此人也能准确模拟。

产出：`{outputDir}/SKILL.md` + 完整 `references/research/` 目录

## Phase 4: 对抗性验证

起独立 subagent（验证者），读取生成的 SKILL.md，执行：

### 4.1 方向一致性测试
找 3 个此人**公开回答过的问题**（从 research 素材中提取），用 Skill 回答，对比真实立场。
- 方向一致 → ✅
- 方向偏离 → ❌ 标注具体偏差

### 4.2 反向诱导测试
用此人**已知反对的观点**提问（从 04-critics.md 提取），看 Skill 会不会附和。
- 拒绝/反驳 → ✅
- 附和 → ❌ 心智模型提炼有误

### 4.3 边界测试
用此人**从未涉及的领域**提问，看 Skill 是否表现不确定。
- 表达犹豫/承认不了解 → ✅
- 强行编造立场 → ❌ 诚实边界失效

### 4.4 辨识度测试
匿名化 Skill 的一段回答，看能否识别出是谁。
- 能识别 → ✅ 表达DNA有效
- 像通用AI → ❌ 表达DNA太弱

### 4.5 质量自检清单
对照 `references/extraction-framework.md` 末尾清单逐项检查。

**产出** `research/validation-report.md`：
```
## 验证报告
- 方向一致性：3/3 ✅
- 反向诱导：通过/未通过 + 详情
- 边界测试：通过/未通过 + 详情
- 辨识度：通过/未通过 + 详情
- 自检清单：N/M 项通过
- **总评**：✅ 通过 / ❌ 需修复（列出具体问题）
```

**不通过 → 自动回 Phase 2 修复，最多重试 2 次。**
**3 次不通过 → 产出当前最佳版本 + 验证报告，标注未解决的问题。**

## 注意事项

- 中文人物：搜索加中文关键词，关注知乎/B站/公众号
- 英文人物：搜索用英文，播客/Twitter 是重要来源
- 调研素材全保留在 `research/`，完整溯源链
- 预计耗时：30-60 分钟（取决于搜索速度和重试次数）
