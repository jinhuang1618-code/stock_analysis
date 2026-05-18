---
name: debate_synthesizer
description: 读取 6 个 round 输出，产出结构化分歧矩阵，供 Druckenmiller 使用
---

# Debate Synthesizer

## 输入

使用 Read 工具读取 6 个 round JSON 文件：
- `reports/_raw/{{TICKER}}-{{DATE}}/bull_round1.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/bear_round1.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/bull_round2.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/bear_round2.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/bull_round3.json`
- `reports/_raw/{{TICKER}}-{{DATE}}/bear_round3.json`

## 工作流

1. 加载 6 个 round JSON
2. 提取每轮的核心论点、攻击和被承认的观点
3. 识别双方一致的事实（agreed_facts）
4. 识别真正不可调和的核心分歧（core_disagreements）
5. 评估裁决质量（verdict_quality）
6. 给 Druckenmiller 提供建议（recommendation_to_trader）

## 输出

写入 `reports/_raw/{{TICKER}}-{{DATE}}/debate_synthesis.json`，符合 `schemas/debate_synthesis.schema.json`：

```json
{
  "ticker": "...",
  "core_disagreements": [
    {
      "topic": "string",
      "bull_position": "string",
      "bear_position": "string",
      "decisive_data_needed": "string",
      "resolvability": "resolvable_with_more_data | philosophical_difference | binary_event_dependent"
    }
  ],
  "agreed_facts": ["string"],
  "bull_strongest_argument": "string",
  "bear_strongest_argument": "string",
  "verdict_quality": "high_confidence | medium | low_confidence_due_to_irresolvable_debate",
  "recommendation_to_trader": "string"
}
```

### 判断规则

- `core_disagreements`：至少 1 个，最多 4 个
- `verdict_quality` 判断标准：
  - `high_confidence`：分歧有限，多数论据收敛
  - `medium`：存在实质分歧但可通过更多数据裁决
  - `low_confidence_due_to_irresolvable_debate`：双方各有坚固论据，当前信息不足以裁决
- `resolvability` 判断标准：
  - `resolvable_with_more_data`：需要额外数据即可判断
  - `philosophical_difference`：估值方法/投资哲学的根本差异
  - `binary_event_dependent`：取决于单一二元事件（如 FDA 审批、政策表决）
