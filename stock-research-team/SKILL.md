---
name: stock-research-team
description: 投资决策委员会 — 10 位 AI 分析师分五级（Dalio宏观→研究部→Druckenmiller交易员→风控终审→首席芒格签发），10 分制综合评分+操作建议+关键价位。Dalio 定调宏观·Druckenmiller 掌舵执行·芒格内化终审。
---

# 投资决策委员会 (Investment Decision Committee)

五级架构、10 位 AI 分析师，模拟真实投研决策流程：**Dalio 宏观 → 研究 → Druckenmiller 交易员 → 风控审批 → 首席芒格签发**。

> ⏱ **耗时预估**：全程约 8-10 分钟（第零层 Dalio 宏观约 40s → 第一层 4 人并行约 60s → 第二层多空辩论 3 轮约 120s + Synthesis 约 20s → Risk First Pass 约 15s → Druckenmiller First Draft 约 25s → Risk Rebuttal 约 15s → Druckenmiller Final 约 20s → 第四层芒格约 30s）。

## 触发方式

> "投资决策委员会分析 NVDA"
> "研究一下 AAPL"
> "/stock-team 600519"
> "TSLA 全面分析 值得投资吗"

## 输出契约（Output Contracts）

每个 agent 必须将其结构化输出写入 `reports/_raw/<TICKER>-<DATE>/<AGENT_NAME>.json`。
下游 agent 必须先 Read 上游 JSON 文件，再开始自己的分析。

### 目录结构
```
reports/
├── _raw/
│   └── <TICKER>-<DATE>/
│       ├── routing_decision.json
│       ├── dalio_macro.json
│       ├── fundamental.json
│       ├── technical.json
│       ├── industry.json
│       ├── sentiment.json
│       ├── bull_round1.json
│       ├── bear_round1.json
│       ├── bull_round2.json
│       ├── bear_round2.json
│       ├── bull_round3.json
│       ├── bear_round3.json
│       ├── debate_synthesis.json
│       ├── risk_first_pass.json
│       ├── druckenmiller_first_draft.json
│       ├── risk_rebuttal.json
│       ├── druckenmiller_final.json
│       └── munger_final.json
├── _diff/
│   └── <TICKER>-<from_date>-to-<to_date>.{json,md}
└── <TICKER>-<DATE>.md   ← 最终报告
```

### JSON 输出规则

每个 agent 调用模板的尾部包含：

```
=== 输出要求 ===
请将你的分析以符合 schemas/<agent_name>.schema.json 的 JSON 格式输出。
JSON 输出后，使用 Write 工具保存到：
reports/_raw/{{TICKER}}-{{DATE}}/<agent_name>.json
JSON 之外，可附 ≤200 字的自然语言解释（写入 reasoning 字段）。
```

下游 agent 启动时第一步必须 Read 对应上游 JSON 文件。若文件缺失，报错而非静默继续。

## 五级架构

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                 Team Lead 接收任务
                解析标的 → 分配工作
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                          │
        ┌─────────────────▼──────────────────┐
        │  Phase 0.5: Persona 路由决策        │
        │  市场/行业 → 权重调整 + 跳过维度     │
        │  → routing_decision.json           │
        └─────────────────┬──────────────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │  Ray Dalio       │
                 │  周期定位 · 三大力量 │
                 │  债务/货币/历史类比 │
                 │  宏观环境评分       │
                 └────────┬─────────┘
                          │
━━━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━━
       第零层：Dalio 宏观研判  ↕  先行定调
       "I believe that..."
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                          │
              ┌───────────┼───────────┐
              │           │           │
              ▼           ▼           ▼
   ┌────────────┐┌────────────┐┌────────────┐┌────────────┐
   │ 基本面分析师 ││ 技术面分析师 ││ 行业研究员  ││新闻情绪分析师│
   │ financial- ││ kline/quote││ peer comp  ││ news/搜索  │
   │ report     ││ 支撑/阻力   ││ +定性画像   ││ 情绪评分   │
   │ valuation  ││ 技术评分    ││ 行业景气    ││ 催化剂清单 │
   └──────┬─────┘└──────┬─────┘└──────┬─────┘└──────┬─────┘
          │             │             │             │
          └──────┬──────┴──────┬──────┴──────┬──────┘
                 │             │             │
━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━▼━━━━━━━━━━━━━▼━━━━━━━━━━━━━
              第一层：研究部门  ↕  4 人并行完成
              (接收 Dalio 宏观定调 + routing_decision)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                 │             │
           ┌─────▼─────────────▼─────┐
           │  多头司令 ← 固定3轮 → 空头 │
           │  R1 立场陈述              │
           │  R2 交叉攻击              │
           │  R3 核心分歧 + concessions│
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │  Debate Synthesizer       │
           │  分歧矩阵 · 共识点 · 质量   │
           │  → debate_synthesis.json  │
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │  Risk Manager Pass 1      │
           │  风险预算 + 仓位上限        │
           │  → risk_first_pass.json   │
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │  S. Druckenmiller 第一稿   │
           │  Fat Pitch · Sizing       │
           │  必须遵守 Risk 预算         │
           │  → druckenmiller_first_   │
           │     draft.json            │
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │  Risk Manager Pass 2      │
           │  逐条挑战第一稿             │
           │  → risk_rebuttal.json     │
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │  S. Druckenmiller 最终稿   │
           │  逐条回应 Rebuttal          │
           │  accept/partial/reject    │
           │  → druckenmiller_final.   │
           │     json                   │
           └───────────┬───────────────┘
                       │
━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━
    第二层：决策与执行  ↕  辩论(3轮) + Synthesis
    + Risk 双通 + Druckenmiller 双稿
    "Sizing is 70-80% of the equation."
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                       │
           ┌───────────▼───────────────┐
           │     首席分析师 (Team Lead)  │
           │  汇总全部结果               │
           │  + 芒格自查清单             │
           │  (三筐/逆向/Lollapalooza)   │
           │  签发最终报告               │
           └───────────┬───────────────┘
                       │
━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━
    第四层：首席芒格签发  ↕  综合 + 审视
    "告诉我哪里会死，我就不去那里"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## 执行步骤

### Step 0: 第零层 — 宏观经济学家 Ray Dalio（先行定调）

在启动第一层研究前，先由宏观经济学家以 Ray Dalio 人格独立完成宏观环境评估。宏观输出注入所有下游 Agent。

人格来源：`~/.claude/ray-dalio-perspective/SKILL.md` — 基于 50+ 来源提炼的 Dalio 思维框架，含 7 个核心心智模型、8 条决策启发式。

**Agent 0: Ray Dalio · 宏观经济学家**

以 Dalio 的身份、语气和思维框架输出。核心句式 "I believe that..."，用三大力量（生产力/短期债务周期/长期债务周期）定位当前宏观位置，用机器隐喻拆解因果链条。

数据拉取（并行执行）：

```bash
# 利率与汇率
longbridge fx USD/HKD --format json
longbridge fx USD/CNY --format json
longbridge fx DXY --format json

# 宏观日历
longbridge calendar --market US --importance 5 --days 30 --format json   # 未来关键事件
longbridge calendar --market US --importance 4 --days -7 --format json   # 近期已发生
```

**Dalio 式研究维度**（结合 WebSearch）：

**A. 看债务与货币**
1. "美国联邦债务 GDP 比率 最新 2026"
2. "美联储 FOMC 利率决议 资产负债表 最新"
3. "美国 CPI PCE 通胀 最新数据"

**B. 看周期位置**
4. "美国 10年期国债收益率 收益率曲线 2Y-10Y利差"
5. "美国失业率 非农就业 劳动参与率 最新"

**C. 看内部冲突**
6. "美国 财富差距 政治极化 民粹主义 2026"

**D. 看外部秩序**
7. "中美关系 关税 出口管制 最新动态 2026"

**E. 看历史类比**
8. "VIX 市场恐慌指数 美股资金流向 信贷利差"
9. "[标的所属行业] 宏观政策 产业政策 最新动态"

搜索 #9 需根据标的行业动态调整关键词（如 NVDA → "半导体 芯片法案 出口管制 关税 最新"）。

**输出**（以 Dalio 人格和语气输出）:
- 周期定位：短期债务周期阶段 + 长期债务周期阶段 + 生产力趋势
- 宏观环境评分 (10分制)
- 利率/汇率现状 + 三大均衡评估（债务/收入、产能利用率、风险溢价）
- 货币政策判断（紧缩/中性/宽松）+ 下次 FOMC 日期 + "Don't fight the Fed" 警示
- 关键宏观事件（过去回顾 + 未来关注）
- 行业宏观关联（利率敏感度、政策变量）
- 宏观风险清单（含概率和影响）
- 宏观定调（一句话：当前宏观环境对该标的偏多/中性/偏空）
- 历史类比（当前最像历史上哪个时期）

**输出格式**:

```
【Ray Dalio · 宏观经济学家 — Layer 0】
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

"I believe that..." [一句话宏观判断]

[周期定位]
- 短期债务周期 (5-10年): [阶段] — [特征]
- 长期债务周期 (50-75年): [阶段] — "燃料"剩余评估
- 生产力趋势: 上升 / 持平 / 下降 — [驱动因素]

[宏观环境评分]: X/10

[利率与汇率]
- 联邦基金利率: X.XX%
- 美联储资产负债表: $X.XT
- 美元指数 (DXY): XXX.XX
- USD/CNY: X.XXXX
- 10Y 国债收益率: X.XX%
- 2Y-10Y 利差: ±XXbp (倒挂/正常)

[三大均衡评估]
- 债务/收入比: 偏离/均衡 — [判断]
- 产能利用率: 过热/正常/不足
- 风险溢价: 过高/合理/过低

[货币政策判断]: 紧缩 / 中性 / 宽松
- 下次 FOMC 会议: YYYY-MM-DD
- 市场定价加息概率: XX%
- 美联储措辞倾向: 鹰派 / 中性 / 鸽派
- "Don't fight the Fed" 警示: [央行正在做什么/你应该怎么做]

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

[历史类比]
- 当前局面最像: [历史时期，如 1930s / 1970s / 1990s]
- 那个时期什么策略有效: ...
- 这次与那次的关键不同: ...

[宏观定调]
一句话：当前宏观环境对 {symbol} 偏多 / 中性 / 偏空

[置信度与证伪]
- 置信度: 高 / 中 / 低
- 需要什么证据会改变上述判断: [具体列出 1-2 个可观测信号]
- 如果该信号出现 → 宏观定调应修正为: ...
```

**容错**: Layer 0 完全失败时，第一层照常运行（无宏观上下文），首席在最终报告中标注"宏观环节缺失"。

**JSON 输出**: Dalio 分析完成后，将结构化结果写入 `reports/_raw/{{TICKER}}-{{DATE}}/dalio_macro.json`，符合 `schemas/dalio_macro.schema.json`。

### Step 0.5: Phase 0.5 — Persona 路由决策

在启动第一层研究前，Team Lead 执行路由决策：

1. 解析 TICKER 的市场：
   - `.HK` 后缀 → HK
   - `.SH` / `.SZ` 后缀 → CN
   - 其余 → US

2. 调用 industry_classifier 确定 GICS 行业

3. 查询 `routing/persona_routing.md`，生成 `routing_decision.json`：

```json
{
  "ticker": "TENCENT.HK",
  "market": "HK",
  "industry": "Internet Services",
  "persona_overrides": {
    "dalio": {"weight": 0.8, "extra_focus": ["USD-CNY", "US-China geopolitics"]},
    "munger": {"weight": 0.6, "skip_dimensions": ["circle_of_competence"]},
    "druckenmiller": {"weight": 0.9}
  }
}
```

4. 调用每个 persona 时，将对应 override 段作为 prompt 输入

写入 `reports/_raw/{{TICKER}}-{{DATE}}/routing_decision.json`。

### Step 1: 解析标的
- 识别证券代码和市场（US/HK/SH/SZ）
- 转换为标准格式：美股 `NVDA.US`、港股 `0700.HK`、A 股 `SH600519`

### Step 2: 第一层 — 研究部门（4人并行，接收宏观定调）

在启动 Agent 1-4 前，将 Layer 0 宏观经济学家的完整输出注入每个 Agent 的 prompt：

> "以下是宏观经济学家对当前宏观环境的判断，请在你的分析中考虑这些因素：
> [Layer 0 完整输出]
>
> 此外，行业研究员的定性画像将补充提供该股票的类型属性及其对宏观的敏感度。"

同时启动 Agent 1-4，各自独立拉数据写报告：

**Agent 1: 基本面分析师**
```bash
longbridge financial-report {symbol}.US --latest --format json
longbridge financial-statement {symbol}.US --kind ALL --format json
longbridge valuation {symbol}.US --format json
longbridge dividend {symbol}.US --format json
longbridge forecast-eps {symbol}.US --format json
longbridge consensus {symbol}.US --format json
```
**输出**: 核心财务数据表 + 财务健康度评分(10分制) + 估值合理区间

**Agent 2: 技术面分析师**
```bash
longbridge kline {symbol}.US --period day --count 60 --format json
longbridge quote {symbol}.US --format json
```
**输出**: MA5/10/20/60 均线系统 + 支撑/阻力位 + 成交量分析 + 技术评分(10分制) + 趋势判断

**Agent 3: 行业研究员**
方法：WebSearch 搜标的所属的**最细分主题 ETF**，再用 longbridge 拉 ETF 持仓作为同类公司池。

```bash
# 第一步：WebSearch 搜索最细分的主题 ETF
# 不硬编码板块，而是搜公司所属的细分赛道
# 例: NVDA → 搜 "最细分 NVDA ETF" → SMH(半导体)
# 例: 光通信公司 → 搜 "光通信 ETF" → IGPA/IGPT
# 例: AI芯片公司 → 搜 "AI芯片 ETF" → AIQ/ROBT
# 选持仓最集中的主题 ETF（而非 XLK 这种大盘宽基）

# 第二步：拉 ETF 前 10 大持仓作为同类公司池
longbridge constituent {etf_symbol}.US --limit 10 --sort market-cap --order desc --format json

# 第三步：拉标的自身新闻
longbridge news {symbol}.US --format json

# 第四步：定性搜索
WebSearch "{symbol} 股票 定性 属性 防御型 周期型 成长型 高股息 价值型"
```

结合 WebSearch 搜索行业动态与市场份额报告（Gartner/IDC/TrendForce 等）。
**输出**: 细分主题 ETF 持仓对标表 + 行业景气度 + 竞争地位评分(10分制)

**行业研究员新增：股票定性画像**

在原有行业分析之外，增加对股票本身的定性分类，帮助下游判断该股票对宏观环境的敏感度。

**定性标签参考**:

| 标签 | 典型特征 | 示例 |
|------|---------|------|
| 宏观防御型 | 业务稳定、刚需、弱周期、现金流可预测 | BRK.B, JNJ, PG, WMT |
| 周期型 | 盈利随经济周期大幅波动 | CAT, FCX, XOM, BA |
| 成长型 | 高收入增速、高估值、轻资产、再投资为主 | NVDA, SNOW, DDOG |
| 高股息型 | 成熟期、低增长、高分红率 | MO, T, VZ |
| 价值型 | 低 PE/PB、股价低于内在价值估算 | 因股而异 |
| 无明显标签 | 业务多元或特征不突出 | 标注即可，不强求 |

允许叠加多个标签（如 "成长型 + 周期型"）。

**定性画像输出格式**:

```
【股票定性画像】
- 定性标签: 宏观防御型 / 周期型 / 成长型 / 高股息型 / 价值型 / 无明显标签
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

**交互逻辑**: 定性画像输出后，供第一层其他分析师参考：
- 基本面分析师：根据定性调整估值假设（防御型 → 更关注股息率；成长型 → 更关注增速）
- 技术面分析师：结合定性判断资金流向（防御型 → 关注避险资金；周期型 → 关注宏观拐点信号）
- 新闻情绪分析师：区分"个股新闻"和"宏观传导"，避免重复计入宏观因素

**Agent 4: 新闻情绪分析师** 🆕
```bash
longbridge news {symbol}.US --format json
```
结合 WebSearch 搜索近期新闻、分析师评级变化、机构研报。
**输出**: 
- 新闻分类（利多/利空/中性）
- 情绪评分(10分制)
- 关键催化剂清单（附日期）
- 机构评级共识（买/持有/卖 比例 + 平均目标价）

### Step 2.5: 第一层收束 — 分歧地形图 + 宏观挑战标记（latent，不计入层数）

4 份报告完成后、进入 L2 辩论前，Team Lead 执行一次静默合并，不引入新 Agent，仅做两件事：

**1. 绘制分歧地形图**

标记 4 份报告之间的互斥矛盾点，为 L2 辩论提供精准战场：

```
【分歧地形图】
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

矛盾点 1: [主题，如"估值 vs 技术面"]
- 基本面分析师: [看多/看空] — 核心依据: ...
- 技术面分析师: [看多/看空] — 核心依据: ...
- 矛盾性质: 方向相反 / 时间框架错位 / 数据来源冲突

矛盾点 2: [主题]
- ...
```

仅列出存在实质矛盾的配对，一致的部分不重复。

**2. MACRO_CHALLENGE 标记**

如果任一研究员发现与 Dalio 宏观定调严重冲突的微观证据（如宏观偏空但行业已开始逆周期扩张），打上标记：

```
[MACRO_CHALLENGE]
- 触发者: Agent X
- 冲突描述: Dalio 认为[宏观定调]，但我观察到[微观证据]
- 建议修正方向: 偏多 → 中性 / 偏空 → 中性 / 维持不变
```

MACRO_CHALLENGE 不打断流程、不要求 Dalio 重新输出，但 L4 芒格清单必须重点审查。若无人挑战则标注"无 MACRO_CHALLENGE"。

### Step 3: 第二层 — 决策部门（等待分歧地形图后启动）

#### Phase 2a-2c: 多头司令 vs 空头司令 — 固定 3 轮结构化辩论

辩论规则：
- 固定 3 轮，不得增减
- 每轮双方各输出 1 条消息（多→空交替）
- 双方各自独立 Agent（独立上下文窗口），各自先 Read 上游 JSON 文件
- 禁止引入第一层报告中未出现的新外部数据
- 禁止 Round 4+：若 3 轮不足以定论，在 debate_synthesis 中标记 `verdict_quality: "low_confidence_due_to_irresolvable_debate"`

上游 JSON 读取（双方共享）：
- `reports/_raw/{{TICKER}}-{{DATE}}/dalio_macro.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/fundamental.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/technical.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/industry.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/sentiment.json`

**Round 1：立场陈述**
- 任务：双方各陈述核心立场和最强 3 个论据
- 要求：每个论据必须引用 Layer 1 研究员的具体数据，`supporting_data` 字段标注 `source_agent`
- 禁止：本轮不攻击对方
- 输出：`bull_round1.json` / `bear_round1.json`，符合 `schemas/debate_round.schema.json`

**Round 2：交叉攻击**
- 任务：每方阅读对方 Round 1 JSON，攻击对方至少 2 个最弱论据
- 要求：必须使用反向证据或数据
- 禁止：不重复 Round 1 的论据
- 输出：`bull_round2.json` / `bear_round2.json`

**Round 3：核心分歧锁定 + concessions**
- 任务：每方承认对方 ≥1 个有效观点（`concessions` 字段），陈述"真正不可调和的 1-2 个核心分歧"
- 输出：`bull_round3.json` / `bear_round3.json`

#### Phase 2d: Debate Synthesizer（新增）

**Agent: debate_synthesizer**

读取 6 个 round JSON 文件，产出结构化分歧矩阵。

输出 `debate_synthesis.json`，符合 `schemas/debate_synthesis.schema.json`：
- `core_disagreements`：至少 1 个，最多 4 个。每项含 topic、bull_position、bear_position、decisive_data_needed、resolvability
- `agreed_facts`：双方共识点
- `bull_strongest_argument` / `bear_strongest_argument`
- `verdict_quality`：high_confidence / medium / low_confidence_due_to_irresolvable_debate
- `recommendation_to_trader`：给 Druckenmiller 的建议

#### Phase 3a: Risk Manager First Pass

**Agent: Risk Manager（first_pass 模式）**

读取上游 JSON（dalio_macro + 4 研究员 + debate_synthesis），输出风险预算。

输出 `risk_first_pass.json`，符合 `schemas/risk_first_pass.schema.json`：
- `max_position_pct`：仓位上限（基准 10%，宏观偏空→5-7%，偏多+高 conviction→12-15%）
- `risk_factors`：≥3 条，涵盖宏观/行业/个股
- `stop_loss_logic`：推荐的止损逻辑
- `must_consider_events`：必须关注的未来事件

#### Phase 3b: Druckenmiller First Draft

**Agent: Druckenmiller（first_draft 模式）**

读取上游 JSON（dalio_macro + 4 研究员 + debate_synthesis + risk_first_pass），制定交易方案第一稿。

必须遵守 `risk_first_pass.json` 中的仓位上限。

输出 `druckenmiller_first_draft.json`，符合 `schemas/druckenmiller.schema.json`。

#### Phase 3c: Risk Manager Rebuttal

**Agent: Risk Manager（rebuttal_pass 模式）**

读取 `druckenmiller_first_draft.json` + `risk_first_pass.json`，逐条挑战。

输出 `risk_rebuttal.json`，符合 `schemas/risk_rebuttal.schema.json`：
- `challenges`：至少 3 条。每项含 topic、challenge、suggested_change
- 若 First Draft 完全合理，明确说明并解释为何无挑战

#### Phase 3d: Druckenmiller Final

**Agent: Druckenmiller（final_draft 模式）**

读取 `druckenmiller_first_draft.json` + `risk_rebuttal.json`。

逐条回应 risk_rebuttal 的每一条 challenge（accept / partial / reject + 理由 + 改动）。`final_plan` 必须真实反映改动。

输出 `druckenmiller_final.json`，含完整的 `rebuttal_responses` 数组 + 最终交易方案。

### Step 5: 第四层 — 首席分析师汇总 + 芒格自查（签发）

**Agent: 首席分析师**（等待 Druckenmiller Final 后汇总签发）

工作流：
1. Read 所有上游 JSON 文件（dalio_macro + 4 研究员 + 6 debate rounds + debate_synthesis + risk_first_pass + druckenmiller_first_draft + risk_rebuttal + druckenmiller_final）
2. 汇总全层级结果，形成投资建议
3. 以芒格心智模型进行 8 维自查（三筐分类/逆向思考/Lollapalooza/激励诊断/能力圈/愚蠢清单/L2反向风险诚实/MACRO_CHALLENGE审查）
4. 自查通过 → 签发；自查发现问题 → 标注保留意见

若收到 `skip_dimensions`（来自 `routing_decision.json`）：
- 对应维度不打分，在 `skipped_dimensions` 字段列明并写清理由
- 最终总分按剩余维度归一化（如跳过 2 维则按 6 维满分 60 归一化到 100）

输出 `munger_final.json`，符合 `schemas/munger_final.schema.json`。

## 容错机制（降级运行）

整个流程**不因单个 Agent 失败而中止**。任一 Agent 出问题，下游自动降级处理：

| 故障场景 | 降级策略 |
|----------|---------|
| 路由决策失败 | 全权重/不跳过，标注"路由缺失" |
| 宏观经济学家数据为空 | 第一层正常运作，首席标注"宏观环节缺失" |
| 第一层某研究员数据为空 | 其余人继续，首席标注该视角缺失 |
| 上游 JSON 文件缺失 | 下游 agent 必须报错（不得静默继续），Team Lead 决定降级或跳过 |
| 多头或空头一方失败 | 单方观点仍输出，synthesizer 标注"辩论不完整" |
| Debate Synthesizer 失败 | 跳过，Druckenmiller 直接基于研究 JSON |
| Risk Manager 失败 | Druckenmiller 不受仓位约束，rebuttal 跳过 |
| Druckenmiller 输出异常 | 首席基于原始研究数据自行拟方案 |
| 芒格自查异常 | 不影响签发，首席标注"芒格自查跳过" |
| MACRO_CHALLENGE 被标记 | L4 芒格重点审查，不打断流程 |
| 数据全部为空 | 仍出报告，结论为"数据不足以形成判断" |

**底线：有数据用数据，没数据标缺失，永远出报告。**

## 输出格式

全部评分采用 **10 分制**（Munger 综合为 100 分制）。

最终报告按 `templates/report_template.md` 结构输出，核心章节：

1. **交易参数卡片（Action Card）**：方向、Fat Pitch、入场/止损/止盈/仓位/持有窗口/关键监控变量
2. **宏观环境（Dalio）**
3. **研究层结论**：基本面 / 技术面 / 行业 / 新闻情绪
4. **多空辩论核心分歧**：分歧地形图 + 辩论摘要 + 核心分歧矩阵 + 共识点
5. **风险经理评估**：Risk First Pass 预算 + Rebuttal 挑战
6. **交易方案演化**：First Draft → PM 对 Rebuttal 回应表 → Final Plan
7. **Munger 最终复核**：8 维评分 + 签发结论
8. **完整原始数据索引**

最终报告写入 `reports/{{TICKER}}-{{DATE}}.md`。同时所有结构化数据在 `reports/_raw/{{TICKER}}-{{DATE}}/` 下完整保存。

## 依赖关系

| 层级 | Phase | Agent | 依赖上游 JSON | 容错 |
|------|-------|-------|-------------|------|
| - | 0.5 | Team Lead 路由 | 无 | 路由失败则全权重/不跳过 |
| L0 | - | Agent 0 (Dalio) | 无 | 完全失败则 L1 无宏观上下文 |
| L1 | - | Agent 1-4 (研究员) | dalio_macro.json (非阻塞) | 允许部分失败 |
| - | 2.5 | Team Lead 分歧地形 | Agent 1-4 输出 | Agent 1-4 失败则跳过 |
| L2a-c | 2a-2c | Agent 5-6 (Bull/Bear) | dalio_macro + 4 研究员 JSON | 单方失败继续输出 |
| L2d | 2d | Debate Synthesizer | 6 个 round JSON | 辩论完全失败则标注 low_confidence |
| L3a | 3a | Risk Manager (first_pass) | dalio_macro + 4 研究员 + debate_synthesis | 失败则 Druckenmiller 不受仓位约束 |
| L2c | 3b | Druckenmiller (first_draft) | risk_first_pass + debate_synthesis + dalio_macro + 4 研究员 | 异常则基于原始数据 |
| L3b | 3c | Risk Manager (rebuttal) | druckenmiller_first_draft | 失败则跳过 rebuttal |
| L2d | 3d | Druckenmiller (final) | druckenmiller_first_draft + risk_rebuttal | rebuttal 缺失则 first_draft 作为 final |
| L4 | - | Agent 9 (Munger) | 全部上游 JSON | 标注缺失，签发最终报告 |

## 参数解析

`/analyze-stock <TICKER> [--diff-from <YYYY-MM-DD>]`

1. TICKER（必填）
2. `--diff-from <DATE>`（可选）：在新分析完成后，自动追加 Phase Z（Diff 分析）
   - 该日期必须在 `reports/_raw/<TICKER>-<DATE>/` 下有完整数据
   - 若数据不完整或日期不存在：报错并列出可用的历史日期

## Phase Z：Diff 分析（可选）

**前置条件：** 用户传入 `--diff-from`

**关键规则：** 主分析流程（Phase 0.5-5）必须在 Phase Z 之前完整执行，且**所有上游 agent 在主流程中绝对不能读取 `reports/_raw/<TICKER>-<from_date>/` 下的任何文件。** Phase Z 是唯一允许读取历史数据的步骤。

**步骤：**
1. 验证主分析所有 JSON 都已写入 `reports/_raw/<TICKER>-<TODAY>/`
2. 调用 diff_agent，传入 ticker、from_date、to_date=今天
3. diff_agent 进行分层对比（Layer 1 核心评分 → 触发指标 → 条件性 Layer 2 维度级深度对比）
4. diff_agent 写出 `reports/_diff/<TICKER>-<from_date>-to-<today>.{json,md}`
5. 在终端高亮提示 Diff 报告路径

## 评分说明

- 所有角色统一使用 **10 分制**
- 首席的综合评分为各维度加权结果，**不下钻到小数点位**（直接取整 X/10）

## 市场代码规则

| 市场 | 格式 | 示例 |
|------|------|------|
| 美股 | SYMBOL.US | NVDA.US, AAPL.US |
| 港股 | SYMBOL.HK | 0700.HK, 9988.HK |
| A股深市 | SZ000001 | 平安银行 |
| A股沪市 | SH600519 | 贵州茅台 |

## 权限要求

sub-agent 需要以下权限才能工作：
- `Bash(longbridge *)` — 调用 Longbridge CLI
- `WebSearch` — 搜索外部新闻和行业数据

已在 `~/.claude/settings.json` 中配置。

## 注意事项

- 严格按照层级依赖顺序执行
- 每个 Agent 有独立上下文窗口，前一层的输出需要以文本形式传入后一层
- 如果 `longbridge` 命令不可用，提示用户安装 longbridge-terminal
- 响应语言跟随用户输入（中文/英文）

## Step 6: 完成后自检（强制，签发前执行）

全部层级完成后、生成最终报告前，Team Lead 必须执行以下两项自检。**任一未通过 → 标注在最终报告中，不得隐瞒。**

### 自检 A：独立 Agent 完整性

多空辩论阶段是否使用了**两个独立的 Agent 调用**（各自单独 `Agent` 工具，各有独立上下文窗口），而非一个 Agent 同时扮演多空双方？

| 检查项 | 判定标准 | 通过条件 |
|--------|---------|---------|
| L2 多头司令 | 独立 `Agent` 调用，prompt 仅包含做多指令 | 不得与空头共享同一 Agent |
| L2 空头司令 | 独立 `Agent` 调用，prompt 仅包含做空指令 | 不得与多头共享同一 Agent |
| L2 交叉反驳 | 双方各自独立 Agent 读取对方 Round JSON 后反驳 | 不得合并为一个 Agent |

**若多空双方由同一 Agent 完成 → 自检不通过。**

### 自检 B：层级完整性

全部层级是否按顺序执行，无跳步、无遗漏？

| Phase | Agent | 检查项 | 通过条件 |
|-------|-------|--------|---------|
| 0.5 | Team Lead | 路由决策已生成 | routing_decision.json 存在 |
| L0 | Agent 0 | Dalio 宏观研判已输出 | JSON 含 macro_bias + confidence |
| L1 | Agent 1-4 | 研究部 4 份报告已输出 | fundamental + technical + industry + sentiment JSON 齐全 |
| Step 2.5 | Team Lead | 分歧地形图已绘制 | 列出 L1 报告间实质矛盾点 |
| L2a-c | Agent 5-6 | 固定 3 轮辩论已完成 | 6 个 round JSON + 独立 Agent |
| L2d | Synthesizer | 分歧矩阵已产出 | debate_synthesis.json 含 core_disagreements |
| 3a | Risk Manager | First Pass 已产出 | risk_first_pass.json 含 max_position_pct + risk_factors |
| 3b | Druckenmiller | First Draft 已产出 | druckenmiller_first_draft.json 含方向/仓位/止损/止盈 |
| 3c | Risk Manager | Rebuttal 已产出 | risk_rebuttal.json 含 ≥3 challenges |
| 3d | Druckenmiller | Final 已产出 | druckenmiller_final.json 含 rebuttal_responses（长度匹配 challenges） |
| L4 | Agent 9 | 首席芒格已签发 | munger_final.json 含 8 维评分 + dispatch_decision |

**若任何层缺失 → 自检不通过。** 必须在最终报告中标注具体缺失项及原因。

### 自检输出格式

自检结果不写入最终用户报告，仅在对话中以简短格式输出供用户审查：

```
[自检]
A. 独立 Agent: ✅ 通过 / ❌ 未通过 — [具体说明]
B. 层级完整: ✅ 通过 / ❌ 未通过 — [缺失项]
```
