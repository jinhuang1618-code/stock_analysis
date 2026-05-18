# {{TICKER}} 时间序列 Diff 报告（{{depth}}）

> **{{from_date}} → {{to_date}}（{{elapsed_days}} 天）**
> 旧分析: `reports/{{TICKER}}-{{from_date}}.md`
> 新分析: `reports/{{TICKER}}-{{to_date}}.md`
> Diff 深度: **{{depth}}**（light = 仅核心评分对比；deep = 含底层维度论点对比）

## 价格走势

```
{{sparkline}}  ${{price_then}} → ${{price_now}} ({{pct_change}}%)
{{from_date}}{{spaces}}{{to_date}}
```

[Longbridge K 线（{{from_date}} ~ {{to_date}}）]({{longbridge_chart_url}})

| 指标 | 值 |
|------|-----|
| 起价 → 终价 | ${{price_then}} → ${{price_now}} ({{pct_change}}%) |
| 期间高 / 低 | ${{high}} / ${{low}} |
| 最大回撤 | {{max_drawdown_pct}}% |
| ATR 变化 | {{atr_then}} → {{atr_now}} |

## 评分体系对比

| 维度 | {{from_date}} | {{to_date}} | Δ |
|------|--------------|-------------|---|
| 方向 | {{old_direction}} | {{new_direction}} | {{direction_change_flag}} |
| 信心 | {{old_confidence}}/5 | {{new_confidence}}/5 | {{conf_delta}} |
| Munger 总分 | {{old_munger_score}}/100 | {{new_munger_score}}/100 | {{munger_delta}} |
| Fat Pitch | {{old_is_fat_pitch}} | {{new_is_fat_pitch}} | {{fat_pitch_flag}} |
| 核心分歧数 | {{old_disagree_count}} | {{new_disagree_count}} | {{disagree_delta}} |

**触发指标命中：** {{triggered_indicators_list}}
**Diff 深度决策：** {{depth_decision_rationale}}

## 上次退出触发器状态（最重要）

| 触发器 | 状态 | 证据 |
|--------|------|------|
| {{trigger}} | {{status}} | {{evidence}} |

状态枚举：
- `triggered` — 已触发，应立即按计划行动
- `not_triggered_but_monitoring` — 接近触发，需密切观察
- `passed_without_trigger` — 事件已过去，未触发
- `future_event_still_pending` — 事件尚未发生
- `obsolete` — 因新分析更新，此触发器已不适用

## 交易参数变化

| 参数 | {{from_date}} | {{to_date}} |
|------|--------------|-------------|
| 入场区间 | ${{old_entry_low}}-${{old_entry_high}} | ${{new_entry_low}}-${{new_entry_high}} |
| 止损 | ${{old_stop}} | ${{new_stop}} |
| TP | ${{old_tp1}}/${{old_tp2}} | ${{new_tp1}}/${{new_tp2}} |
| 仓位 | {{old_pos}}% | {{new_pos}}% |

<!-- 以下章节仅当 depth=deep 时渲染 -->
## 各维度论点变化（仅 deep 模式）

### Fundamental
{{fundamental_changes_narrative}}

### Technical
{{technical_changes_narrative}}

### Macro (Dalio)
{{macro_changes_narrative}}

### Industry
{{industry_changes_narrative}}

### Sentiment
{{sentiment_changes_narrative}}
<!-- end deep 模式 -->

## 行动建议

**若已持仓：** {{recommendation_if_held}}

**若未持仓：** {{recommendation_if_not_held}}

---
*本 Diff 报告由 diff_agent 生成。新分析在生成时未读取旧分析，以保证独立性。*
*Light 模式仅对比核心评分；触发深度 diff 的条件见 SKILL.md 触发指标表。*
