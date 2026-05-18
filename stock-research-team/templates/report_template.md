# {{TICKER}} 投资委员会报告 — {{DATE}}

> **本次分析路由决策：**
> - 市场：{{market}} | 行业：{{industry}}
> - Munger 跳过维度：{{skipped_dimensions}}（{{skip_reason}}）
> - Dalio 加强关注：{{extra_focus}}

---

## 交易参数卡片（Action Card）

| 字段 | 值 |
|------|-----|
| 方向 | {{direction}} |
| Fat Pitch | {{is_fat_pitch}} |
| 入场区间 | ${{entry_low}} – ${{entry_high}} |
| 止损 | ${{stop_loss}}（{{stop_loss_logic}}） |
| TP1 | ${{tp1_price}}（平 {{tp1_size_pct}}%） |
| TP2 | ${{tp2_price}}（平 {{tp2_size_pct}}%） |
| 仓位 | {{position_size_pct}}% of portfolio |
| 持有窗口 | {{holding_window_low}} – {{holding_window_high}} 周 |
| 关键监控变量 | {{key_variable_to_monitor}} |
| 反向风险 | {{contrarian_risks_list}} |

---

## 1. 宏观环境（Dalio）
{{dalio_macro_summary}}

## 2. 研究层结论

### 2.1 基本面
{{fundamental_summary}}

### 2.2 技术面
{{technical_summary}}

### 2.3 行业
{{industry_summary}}

### 2.4 新闻情绪
{{sentiment_summary}}

## 3. 多空辩论核心分歧

### 3.1 分歧地形图
{{disagreement_map}}

### 3.2 辩论摘要（3 轮）
{{debate_summary}}

### 3.3 核心分歧
| 主题 | 多头立场 | 空头立场 | 可裁决性 |
|------|---------|---------|---------|
{{#core_disagreements}}
| {{topic}} | {{bull_position}} | {{bear_position}} | {{resolvability}} |
{{/core_disagreements}}

**共识点：** {{agreed_facts}}
**裁决质量：** {{verdict_quality}}

## 4. 风险经理评估

### 4.1 风险预算（First Pass）
- 最大仓位上限：{{max_position_pct}}%
- 风险因素：{{risk_factors}}
- 止损逻辑：{{stop_loss_logic}} — {{stop_loss_rationale}}
- 必须关注事件：{{must_consider_events}}

### 4.2 挑战（Rebuttal）
{{#challenges}}
- **{{topic}}**：{{challenge}} → 建议：{{suggested_change}}
{{/challenges}}

## 5. 交易方案演化

### 5.1 First Draft
{{druckenmiller_first_draft_summary}}

### 5.2 PM 对 Rebuttal 的回应

| 挑战 | 决定 | 理由 | 改动 |
|------|------|------|------|
{{#rebuttal_responses}}
| {{challenge_topic}} | {{decision}} | {{reason}} | {{change_made}} |
{{/rebuttal_responses}}

### 5.3 Final Plan
{{druckenmiller_final_summary}}

## 6. Munger 最终复核

| 维度 | 评分 |
|------|------|
| 三筐分类 | {{three_baskets}}/10 |
| 逆向 — 最大亏钱路径 | {{inversion_risk}}/10 |
| Lollapalooza 检测 | {{lollapalooza_check}}/10 |
| 激励对齐 | {{incentive_alignment}}/10 |
| 能力圈 | {{circle_of_competence}}/10 |
| 愚蠢清单 | {{stupidity_checklist}}/10 |
| L2 反向风险诚实 | {{l2_honesty_check}}/10 |
| MACRO_CHALLENGE 审查 | {{macro_challenge_review}}/10 |
| **综合评分** | **{{overall_score}}/100** |

{{#skipped_dimensions}}
- 跳过：{{dimension}} — {{reason}}
{{/skipped_dimensions}}

**签发：** {{dispatch_decision}}
**保留意见：** {{reservations}}

## 7. 完整原始数据索引

```
reports/_raw/{{TICKER}}-{{DATE}}/
├── dalio_macro.json
├── fundamental.json
├── technical.json
├── industry.json
├── sentiment.json
├── bull_round1.json
├── bear_round1.json
├── bull_round2.json
├── bear_round2.json
├── bull_round3.json
├── bear_round3.json
├── debate_synthesis.json
├── risk_first_pass.json
├── druckenmiller_first_draft.json
├── risk_rebuttal.json
├── druckenmiller_final.json
└── munger_final.json
```

---
*本报告由 AI 投资决策委员会自动生成，仅供参考，不构成投资建议。*
*报告模板版本: v1.0 — 2026-05-18*
