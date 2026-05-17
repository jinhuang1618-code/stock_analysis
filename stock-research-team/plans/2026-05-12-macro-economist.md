# Macro Economist Agent — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Insert Layer 0 (Macro Economist) into the Investment Decision Committee skill and expand the Industry Researcher with stock qualitative profiling.

**Architecture:** Single-file modification to `~/.claude/skills/stock-research-team/SKILL.md`. The macro economist runs solo before Layer 1; its output feeds all downstream agents. The industry researcher gains a qualitative profiling section that maps stock type → macro sensitivity.

**Tech Stack:** Markdown (skill definition), Longbridge CLI (data), WebSearch (data)

---

### Task 1: Update Header Metadata

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md:1-4`

- [ ] **Step 1: Update agent count and description in frontmatter**

Replace lines 2-3:

```yaml
---
name: stock-research-team
description: 投资决策委员会 — 10 位 AI 分析师分五级（宏观→研究部→多空辩论→风控终审→首席汇总），10 分制综合评分+操作建议+关键价位。Triggers: "投资决策委员会", "股票研究团队", "研究一下", "/stock-team", "全面分析", "值得投资吗"
---
```

- [ ] **Step 2: Update header line**

Replace line 6:
```
四级架构、9 位 AI 分析师，模拟真实投研决策流程：**研究 → 多空辩论 → 风控审批 → 首席签发**。
```
with:
```
五级架构、10 位 AI 分析师，模拟真实投研决策流程：**宏观 → 研究 → 多空辩论 → 风控审批 → 首席签发**。
```

- [ ] **Step 3: Update timing estimate**

Replace line 10:
```
> ⏱ **耗时预估**：全程约 4-7 分钟（第一层 4 人并行约 60s → 第二层多空 3-5 轮辩论约 120s → 交易员约 30s → 第三层风控约 20s → 第四层汇总约 20s）。
```
with:
```
> ⏱ **耗时预估**：全程约 5-8 分钟（第零层宏观约 30s → 第一层 4 人并行约 60s → 第二层多空 3-5 轮辩论约 120s → 交易员约 30s → 第三层风控约 20s → 第四层汇总约 20s）。
```

---

### Task 2: Insert Layer 0 Architecture Diagram

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md:21-77`

- [ ] **Step 1: Replace Section 2 architecture diagram**

Replace the entire "## 四级架构" code block (lines 21-77) with the five-level diagram below.

The new architecture block:

```
## 五级架构

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                 Team Lead 接收任务
                解析标的 → 分配工作
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                          │
                          ▼
                 ┌──────────────────┐
                 │   宏观经济学家     │
                 │   FX / 利率       │
                 │   宏观日历 / 政策  │
                 │   宏观环境评分     │
                 └────────┬─────────┘
                          │
━━━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━━
              第零层：宏观研判  ↕  先行定调
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
              (接收宏观定调 + 定性画像)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                 │             │
           ┌─────▼─────────────▼─────┐
           │  多头司令 ←3-5轮→ 空头司令  │
           │  逐轮交锋，每轮含亮牌→质疑  │
           │  →防守→分歧点清单         │
           └───────────┬───────────────┘
                       │
           ┌───────────▼───────────────┐
           │        交易员             │
           │  基于分歧点清单制定交易方案  │
           └───────────┬───────────────┘
                       │
━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━
              第二层：决策部门  ↕  多空辩论
              3-5 轮结构化交锋 + 分歧点清单
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                       │
           ┌───────────▼───────────────┐
           │       风控主管            │
           │  审核交易方案              │
           │  批准 / 否决 / 修改条件     │
           └───────────┬───────────────┘
                       │
━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━
              第三层：风控部门  ↕  一票否决
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                       │
           ┌───────────▼───────────────┐
           │     首席分析师 (Team Lead)  │
           │  汇总全部结果 → 签发报告     │
           └───────────────────────────┘
                       │
━━━━━━━━━━━━━━━━━━━━━━▼━━━━━━━━━━━━━━━━━━━━━━━
              第四层：首席汇总  ↕  最终输出
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
```

---

### Task 3: Insert Step 0 — Macro Economist Execution Section

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md` — insert before "### Step 1: 解析标的"

- [ ] **Step 1: Add Step 0 section before Step 1**

Insert the following block after the architecture diagram and before `### Step 1: 解析标的` (currently line 79):

```markdown
### Step 0: 第零层 — 宏观经济学家（先行定调）

在启动第一层研究前，先由宏观经济学家独立完成宏观环境评估。宏观输出注入所有下游 Agent。

**Agent 0: 宏观经济学家**

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

结合 WebSearch 搜索：

1. "美国联邦基金利率 美联储 FOMC 最新决议"
2. "美国 CPI PCE 通胀数据 最新"
3. "美国失业率 非农就业 最新"
4. "美国 10年期国债收益率 收益率曲线"
5. "[标的所属行业] 宏观政策 产业政策 最新动态"
6. "VIX 市场恐慌指数 美股市场资金流向"

搜索 #5 需根据标的行业动态调整关键词（如 NVDA → "半导体 芯片法案 出口管制 关税 最新"）。

**输出**:
- 宏观环境评分 (10分制)
- 利率/汇率现状
- 货币政策判断（紧缩/中性/宽松）+ 下次 FOMC 日期
- 关键宏观事件（过去回顾 + 未来关注）
- 行业宏观关联（利率敏感度、政策变量）
- 宏观风险清单
- 宏观定调（一句话：当前宏观环境对该标的偏多/中性/偏空）

**输出格式**:

```
【宏观经济学家 — Layer 0】
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[宏观环境评分]: X/10

[利率与汇率]
- 联邦基金利率: X.XX%
- 美联储资产负债表: $X.XT
- 美元指数 (DXY): XXX.XX
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

**容错**: 若 Layer 0 完全失败，第一层照常运行（无宏观上下文），首席标注"宏观环节缺失"。
```

---

### Task 4: Expand Agent 3 (Industry Researcher) with Qualitative Profiling

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md` — the Agent 3 section (currently lines ~106-125)

- [ ] **Step 1: Replace Agent 3 section**

Replace the current Agent 3 block with the expanded version:

```markdown
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
```

结合 WebSearch 搜索行业动态与市场份额报告（Gartner/IDC/TrendForce 等）。
**输出**: 细分主题 ETF 持仓对标表 + 行业景气度 + 竞争地位评分(10分制)

**行业研究员新增：股票定性画像**

在原有行业分析之外，增加对股票本身的定性分类，帮助下游判断该股票对宏观环境的敏感度。

```bash
# 第四步：定性搜索
WebSearch "{symbol} 股票 定性 属性 防御型 周期型 成长型 高股息 价值型"
```

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
```

---

### Task 5: Update Execution Flow (Wire Macro into Layer 1)

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md` — the Step 2 section header and content

- [ ] **Step 1: Update Step 2 header to reference Layer 0**

Replace line 85 (approx):
```
### Step 2: 第一层 — 研究部门（4人并行）
同时启动 Agent 1-4，各自独立拉数据写报告：
```
with:
```
### Step 2: 第一层 — 研究部门（4人并行，接收宏观定调）

在启动 Agent 1-4 前，将 Layer 0 宏观经济学家的完整输出注入每个 Agent 的 prompt：

> "以下是宏观经济学家对当前宏观环境的判断，请在你的分析中考虑这些因素：
> [Layer 0 完整输出]
> 
> 此外，行业研究员的定性画像将补充提供该股票的类型属性及其对宏观的敏感度。"

同时启动 Agent 1-4，各自独立拉数据写报告：
```

- [ ] **Step 2: Update Step 3 to wire macro into debate**

In the Step 3 section (currently line 138), add after the debate rules:

```markdown
辩论双方同时接收 Layer 0 宏观输出和 Agent 3 定性画像作为共享上下文。双方可引用宏观数据和定性标签支持己方论点，但禁止引入第一层报告中未出现的新外部数据。
```

---

### Task 6: Update Output Template

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md` — the output format template (currently lines ~208-312)

- [ ] **Step 1: Add Layer 0 macro section to output template**

After the header block and before `━━━ 第一层：研究部报告 ━━━`, insert:

```
━━━ 第零层：宏观研判 ━━━

【宏观经济学家】
- 宏观环境评分: X/10
- 利率与汇率: ...
- 货币政策判断: 紧缩 / 中性 / 宽松
- 下次 FOMC: YYYY-MM-DD
- 宏观定调: 偏多 / 中性 / 偏空
- 宏观风险: ...
```

- [ ] **Step 2: Add qualitative profile to industry researcher section**

In the `【行业研究员】` section of the template, add after "可比公司估值对比":

```
【行业研究员】
- 竞争地位评分: X/10
- 行业景气度: 上行/持平/下行
- 可比公司估值对比
- 股票定性画像:
  标签: 宏观防御型 / 周期型 / ...（可叠加）
  宏观映射: 利率↑→正/负, 通胀↑→正/负, 衰退→正/负
  宏观呼应: 有利 / 不利 / 中性
```

---

### Task 7: Update Dependency and Error-Handling Tables

**Files:**
- Modify: `~/.claude/skills/stock-research-team/SKILL.md` — the dependency table (currently lines ~315-323) and error handling table (currently lines ~194-205)

- [ ] **Step 1: Update dependency table**

Replace the current dependency table:

```
| 层级 | Agent | 依赖 | 容错 |
|------|-------|------|------|
| 第零层 | Agent 0 | 无（先行运行） | 完全失败则第一层无宏观上下文运行 |
| 第一层 | Agent 1-4 | 依赖 Agent 0 宏观输出（非阻塞） | 允许部分失败，其余继续 |
| 第二层 | Agent 5-6 | 依赖 Agent 0-4，3-5 轮辩论 | 允许一方在第 2 轮后失败，另一方继续输出+分歧点清单 |
| 第二层 | Agent 7 | 依赖 Agent 5-6 辩论结果 + 分歧点清单 | 如辩论完全失败，基于原始研究数据输出 |
| 第三层 | Agent 8 | 依赖 Agent 7 | 如交易员异常，自行拟方案 |
| 第四层 | Agent 9 | 依赖 Agent 0-8 | 标注缺失，签发最终报告 |
```

- [ ] **Step 2: Add Layer 0 error handling to the fault-tolerance table**

In the existing "容错机制（降级运行）" section, add as the first row of the fault table:

```
| 宏观经济学家数据为空 | 第一层正常运作，首席标注"宏观环节缺失" |
```

---

### Task 8: Final Verification

**Files:**
- Modify: None (read-only check)

- [ ] **Step 1: Verify SKILL.md consistency**

```bash
grep -n "9 位" ~/.claude/skills/stock-research-team/SKILL.md
grep -n "四级" ~/.claude/skills/stock-research-team/SKILL.md
grep -n "Agent 0" ~/.claude/skills/stock-research-team/SKILL.md
grep -n "第零层" ~/.claude/skills/stock-research-team/SKILL.md
grep -n "定性画像" ~/.claude/skills/stock-research-team/SKILL.md
```

Expected:
- No remaining "9 位" references (should be 10)
- No remaining "四级架构" references (should be 五级)
- At least 3 references to Agent 0 (header + architecture + execution section)
- At least 3 references to 第零层 (architecture + section header + output template)
- At least 3 references to 定性画像 (Agent 3 section + output template + interaction logic)

- [ ] **Step 2: Verify markdown structure**

```bash
grep "^## \|^### " ~/.claude/skills/stock-research-team/SKILL.md
```

Expected section order:
1. 投资决策委员会 (title)
2. 五级架构 (diagram)
3. 执行步骤 → Step 0, Step 1, Step 2, Step 3, Step 4, Step 5
4. 容错机制
5. 输出格式
6. 依赖关系
7. 评分说明
8. 市场代码规则
9. 权限要求
10. 注意事项

---

### Task 9: Smoke Test

**Files:**
- None (runtime test)

- [ ] **Step 1: Invoke the updated skill on a US stock**

Run:
```
/stock-research-team JNJ.US
```

- [ ] **Step 2: Verify Layer 0 appears**

Check in the output:
- [ ] 宏观环境评分 present
- [ ] 利率/汇率 section present
- [ ] FOMC date present
- [ ] 宏观定调 present

- [ ] **Step 3: Verify qualitative profile appears**

Check in the output:
- [ ] 股票定性画像 section present in industry researcher
- [ ] 定性标签 assigned (JNJ should be 宏观防御型/高股息型)
- [ ] 宏观映射表 present
- [ ] 宏观呼应 line present

- [ ] **Step 4: Verify downstream integration**

Check:
- [ ] 基本面分析师 references macro context
- [ ] Debate participants may cite macro data
- [ ] Final report includes Layer 0 section
```
