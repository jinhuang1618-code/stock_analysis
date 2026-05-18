# Stock Analysis — AI 投资决策委员会

基于 Claude Code 多 Agent 架构的机构级投资分析系统，10 位独立 AI 分析师、五级委员会、结构化 JSON 合约、固定 3 轮辩论、Risk 双通闭环、时间序列 Diff 分析。

## 架构

```
L0: Ray Dalio 宏观经济学家 — 先行定调
  │
L1: 研究部 4 人并行（基本面 + 技术面 + 行业 + 新闻情绪）
  │
L2a: 多头司令 ← 固定 3 轮 → 空头司令（立场陈述 → 交叉攻击 → 核心分歧+concessions）
  │
L2b: Debate Synthesizer — 结构化分歧矩阵
  │
L3a: Risk Manager Pass 1 — 风险预算 + 仓位上限
  │
L2c: Druckenmiller First Draft — 必须遵守 Risk 预算
  │
L3b: Risk Manager Pass 2 — Rebuttal 逐条挑战
  │
L2d: Druckenmiller Final — 逐条回应（accept / partial / reject）
  │
L4: Munger 8 维自查 — 签发
```

| 层级 | 角色 | 核心职能 |
|------|------|---------|
| L0 | Ray Dalio | 周期定位 · 利率汇率 · 宏观定调 |
| L1 | 研究部 (4人并行) | 基本面 + 技术面 + 行业定性画像 + 新闻情绪 |
| L2 | 多空辩论 (固定3轮) + Synthesizer + Druckenmiller 双稿 | 对抗辩论 · 分歧矩阵 · Fat Pitch · 交易方案 |
| L3 | Risk Manager (双通) | First Pass 预算 → Rebuttal 挑战 → PM 逐条回应 |
| L4 | Munger | 8 维自查 · 综合评分 · 签发 |

## 核心特性

### 中间层 JSON 合约
每个 Agent 输出结构化 JSON 到 `reports/_raw/<TICKER>-<DATE>/`。下游 Agent 通过 Read 工具读取上游 JSON，缺失即报错。消除自然语言传递导致的信息失真。

### Risk Rebuttal 闭环
Druckenmiller 出第一稿 → Risk 逐条挑战 → Druckenmiller 逐条回应（accept / partial / reject）。最终报告含完整 PM 回应表，体现委员会决策闭环。

### 可执行交易参数
强制输出：direction、entry_zone、stop_loss + logic、take_profit_levels、position_size_pct、holding_window_weeks。所有数字可追溯至上游 JSON 锚点。

### Persona 路由
按市场（US/HK/CN）和行业动态调整 persona 权重。Munger 在半导体/AI/生物技术领域跳过能力圈维度，A 股/港股降权。

### 固定 3 轮辩论 + Debate Synthesizer
R1 立场陈述 → R2 交叉攻击 → R3 核心分歧 + concessions。禁止 R4+。Synthesizer 产出结构化分歧矩阵（topic / resolvability / verdict_quality）。

### 同股票时间序列 Diff 分析
`/analyze-stock AAPL --diff-from 2026-04-15`。新分析完全独立运行（不读旧数据），事后 diff_agent 分层对比（Layer 1 核心评分 → 触发指标 → 条件性 Layer 2 维度级深度对比）。

## 目录结构

```
stock-research-team/
├── SKILL.md                         # 主流程入口
├── SKILL_list_history.md            # 历史分析列表
├── schemas/                         # JSON 输出合约 (9 个)
├── agents/                          # 功能性 agent (debate_synthesizer, diff_agent)
├── routing/                         # Persona 路由规则
├── templates/                       # HTML 报告模板 + Diff 报告模板
├── personalities/
│   ├── dalio/SKILL.md
│   ├── druckenmiller/SKILL.md
│   ├── munger/SKILL.md
│   └── risk_manager/SKILL.md        # 双模式风控
└── reports/
    ├── _raw/<TICKER>-<DATE>/        # 结构化 JSON 归档 (17 个/次)
    ├── _diff/                        # Diff 分析输出
    └── <TICKER>-<DATE>.md           # 最终报告
```

## 人格构建

四个人格均通过 [女娲 · Skill造人术](https://github.com/alchaincyf/nuwa-skill) 蒸馏构建：从原始素材（视频字幕、著作、访谈记录、13F 持仓文件）中提取思维框架、表达风格与决策启发式，经多轮质量校验迭代而成。

各人格目录内 `references/research/` 保存了蒸馏过程中积累的系统性调研笔记。

## 输出

分析完成后，生成：
- **结构化 JSON**：`reports/_raw/<TICKER>-<DATE>/` 下 17 个文件，完整记录全链路决策
- **HTML 报告**：`~/stock_analysis_rpt/{symbol}_analysis/{symbol}.html` — 12 板块统一模板
- **小红书 PNG**：`{symbol}_xiaohongshu/` — 1080×2x 切片，逐卡片渲染

## 依赖

- [Longbridge CLI](https://longbridge.com) — 行情、基本面、估值、新闻等数据
- Claude Code Agent 工具链 + WebSearch
- Puppeteer — HTML → PNG 渲染

## 使用

```
/stock-research-team 分析 NVDA
/stock-research-team 研究一下 700.HK
/stock-research-team 分析 AAPL --diff-from 2026-04-15
/stock-research-team /list-history AAPL
```

## 免责声明

⚠️ 本系统由 AI 自动生成分析报告，仅供参考，不构成投资建议。投资有风险，决策须谨慎。
