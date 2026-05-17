# Macro Economist Agent — Design Spec

**Date**: 2026-05-12
**Status**: Approved
**Scope**: Add Layer 0 (Macro Economist) + expand Layer 1 Industry Researcher with stock qualitative profiling

## Overview

Insert a Macro Economist agent as **Layer 0** before the existing four-layer pipeline. The agent produces a macro environment assessment that sets the tone for all downstream analysis (fundamental, technical, industry, sentiment → debate → trading → risk → chief).

## Architecture Change

```
Before:                          After:
  Layer 1: Research (4 agents)     Layer 0: Macro Economist (NEW, solo)
  Layer 2: Debate + Trading          ↓
  Layer 3: Risk Control              Layer 1: Research (4 agents, receives macro)
  Layer 4: Chief Summary             ↓
                                     Layer 2-4: unchanged
```

Layer 0 output is fed as required reading into every Layer 1 agent and both debate participants.

## Data Sources

### Structured (Longbridge CLI — parallel calls)

```bash
longbridge fx USD/HKD --format json
longbridge fx USD/CNY --format json
longbridge fx DXY --format json
longbridge calendar --market US --importance 5 --days 30 --format json   # upcoming
longbridge calendar --market US --importance 4 --days -7 --format json   # recent
```

### Unstructured (WebSearch — parallel calls)

Six targeted searches:

1. "美国联邦基金利率 美联储 FOMC 最新决议"
2. "美国 CPI PCE 通胀数据 最新"
3. "美国失业率 非农就业 最新"
4. "美国 10年期国债收益率 收益率曲线"
5. "[标的所属行业] 宏观政策 产业政策 最新动态"
6. "VIX 市场恐慌指数 美股市场资金流向"

Search #5 is symbol-specific (e.g., NVDA → "半导体 芯片法案 出口管制 关税 最新").

## Output Schema

```
【宏观经济学家 — Layer 0】
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[宏观环境评分]: X/10

[利率与汇率]
- 联邦基金利率: X.XX%
- 美联储资产负债表: $X.XT
- 美元指数 (DXY): XXX
- USD/CNY: X.XXXX
- 10Y 国债收益率: X.XX%
- 2Y-10Y 利差: ±XXbp (倒挂/正常)

[货币政策判断]: 紧缩 / 中性 / 宽松
- 下次 FOMC 会议: YYYY-MM-DD
- 市场定价加息概率: XX%
- 美联储措辞倾向: 鹰派 / 中性 / 鸽派

[关键宏观事件]
过去:
- YYYY-MM-DD: [事件] → 实际 vs 预期 → 市场反应
未来:
- YYYY-MM-DD: [事件] → 关注要点

[行业宏观关联]
- [标的行业]对利率敏感度: 高/中/低
- 当前宏观环境对[标的行业]的影响: 正面/中性/负面
- 关键政策变量: ...

[宏观风险清单]
1. [风险] — 概率: X% — 影响: 高/中/低
2. ...

[宏观定调]
一句话：当前宏观环境对 {symbol} 偏多 / 中性 / 偏空
```

## Execution Flow

1. Team Lead parses symbol → identifies industry → passes to Layer 0
2. Layer 0 agent runs: 5 Longbridge calls + 6 WebSearch calls (all parallel)
3. Agent synthesizes results into the structured output above
4. Output appended to prompt of every Layer 1 agent: "以下是宏观经济学家对当前宏观环境的判断，请在你的分析中考虑这些因素：[macro output]"
5. Debate (Layer 2) participants also receive macro output, may cite it to support arguments

## Error Handling

| Failure | Fallback |
|---------|----------|
| Longbridge FX calls fail | WebSearch for exchange rates, mark as "estimated" |
| Longbridge calendar fails | WebSearch for macro calendar, less structured |
| All searches return empty | Output "宏观数据不可用", score N/A, downstream proceeds without macro context |
| Partial WebSearch failures | Non-critical; synthesize from available results |

Layer 0 failure does NOT block the pipeline. If completely broken, Layer 1 runs as before (no macro context).

## Skill File Changes

Modify `~/.claude/skills/stock-research-team/SKILL.md`:

1. Architecture diagram: insert Layer 0 block
2. Add "Step 0: Macro Economist" execution section before current Step 1
3. Add Agent 0 to the dependency table
4. Add macro section to the output format template
5. Update agent count: 9 → 10

## Layer 1 Industry Researcher — Expanded Scope

### New Responsibility: Stock Qualitative Profiling

The Industry Researcher now produces two outputs instead of one:

1. **Original**: Sector outlook + competitive positioning score
2. **New**: Stock qualitative profile — what *type* of stock this is, and how macro forces affect it

### Additional Data Source

One extra WebSearch alongside existing searches:

> "{symbol} 股票 定性 属性 防御型 周期型 成长型 高股息 价值型"

### Qualitative Profile Output Schema

```
【股票定性画像】
- 定性标签: 宏观防御型 / 周期型 / 成长型 / 高股息型 / 价值型 / 无明显标签
  允许叠加多个标签（如 "成长型 + 周期型"）
- 标签依据: (1-2句，基于业务结构、现金流特征、历史表现)
- 宏观映射表:
  ┌────────────┬──────────┐
  │ 宏观变量    │ 影响方向  │
  ├────────────┼──────────┤
  │ 利率上行    │ 正/负/中  │
  │ 通胀高位    │ 正/负/中  │
  │ 经济衰退    │ 正/负/中  │
  │ 美元走强    │ 正/负/中  │
  │ 信贷紧缩    │ 正/负/中  │
  └────────────┴──────────┘
- 与 Layer 0 宏观定调的呼应:
  当前宏观环境对该股票: 有利 / 不利 / 中性
  依据: (结合宏观定调 × 定性标签的映射关系)
```

### Tag Criteria Reference

| 标签 | 典型特征 | 示例 |
|------|---------|------|
| 宏观防御型 | 业务稳定、刚需、弱周期、现金流可预测 | BRK.B, JNJ, PG, WMT |
| 周期型 | 盈利随经济周期大幅波动 | CAT, FCX, XOM, BA |
| 成长型 | 高收入增速、高估值、轻资产、再投资为主 | NVDA, SNOW, DDOG |
| 高股息型 | 成熟期、低增长、高分红率 | MO, T, VZ |
| 价值型 | 低 PE/PB、股价低于内在价值估算 | 因股而异 |
| 无明显标签 | 业务多元或特征不突出 | 标注即可，不强求 |

### Interaction with Layer 0

```
Layer 0 宏观经济学家
  ├→ 宏观定调："当前偏空，利率高位+衰退预期"
  │
  ▼
Layer 1 行业研究员
  ├→ 定性: BRK.B = 宏观防御型
  ├→ 宏观映射: 衰退 → 防御型正面
  ├→ 呼应: "宏观偏空对防御型资产有利，BRK.B 可能受益于资金避险轮动"
  │
  ├─ 行业研究员
  ├─ 基本面分析师  ← 收到定性画像，调整估值假设
  ├─ 技术面分析师  ← 收到定性画像，判断是否有避险资金流入
  └─ 新闻情绪分析师 ← 收到定性画像，区分"个股新闻"和"宏观传导"
```

## Implementation Tasks

1. Write the Layer 0 agent prompt with data-fetching instructions
2. Insert Layer 0 step into SKILL.md execution flow
3. Update architecture diagram (ASCII art)
4. Wire macro output into Layer 1 agent prompts
5. Expand Industry Researcher prompt with qualitative profiling + macro mapping
6. Update output template: add macro section + qualitative profile section
7. Update dependency/error-handling tables
8. Smoke test: run full pipeline on a US stock, verify macro + qualitative sections appear

## Deferred

- Research report feed pipeline (separate spec)
- Trading-master personality distillation for Risk/Chief (separate spec)
