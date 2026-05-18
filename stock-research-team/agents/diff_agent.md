---
name: diff_agent
description: 比较同一 ticker 在两个不同日期的完整分析输出，产出结构化 Diff 报告
---

# Diff Agent

## 输入参数
- ticker：股票代码
- from_date：旧分析日期（YYYY-MM-DD）
- to_date：新分析日期（YYYY-MM-DD，通常即今天）

## 流程

### Step 1：Layer 1 — 加载核心文件
仅读取 6 个文件（每边 3 个）：
- `reports/_raw/{ticker}-{from_date}/druckenmiller_final.json`
- `reports/_raw/{ticker}-{from_date}/munger_final.json`
- `reports/_raw/{ticker}-{from_date}/debate_synthesis.json`
- `reports/_raw/{ticker}-{to_date}/` 同上 3 个文件

若任何文件缺失：报错并提示用户。

### Step 2：拉取期间价格数据
通过 Longbridge CLI 拉取 from_date 到 to_date 的日线数据：
```bash
longbridge kline {ticker}.US --period day --from {from_date} --to {to_date} --format json
```

计算：
- 起止价格、区间最高/最低、累计涨跌幅、区间 ATR
- 生成 ASCII sparkline（用 ▁▂▃▄▅▆▇█ 8 阶字符压缩到 20-30 字符宽度）

### Step 3：计算触发指标，决定是否进入 Layer 2

按以下触发指标表逐项检查。任一命中即 `should_deep_dive: true`。

| 触发指标 | 阈值 | 含义 |
|---------|------|------|
| direction 变化 | 任何变化 | 委员会方向反转（long ↔ hold ↔ short） |
| Munger 总分差距 | abs ≥ 15 分（满分 100） | 质量评估实质变化 |
| confidence 差距 | abs ≥ 2（满分 5） | 信心水平大幅波动 |
| is_fat_pitch 翻转 | 任何变化 | 极佳机会判断变化 |
| 期间价格涨跌 | abs ≥ 15% | 价格大幅变动 |
| 核心分歧数变化 | abs ≥ 2 | 辩论核心冲突点结构性变化 |

### Step 4（条件性）：Layer 2 — 加载底层 agent 文件
若 `should_deep_dive=true`，额外读取 10 个文件：
- `reports/_raw/{ticker}-{from_date}/fundamental.json`
- `reports/_raw/{ticker}-{from_date}/technical.json`
- `reports/_raw/{ticker}-{from_date}/dalio_macro.json`
- `reports/_raw/{ticker}-{from_date}/industry.json`
- `reports/_raw/{ticker}-{from_date}/sentiment.json`
- `reports/_raw/{ticker}-{to_date}/` 同上 5 个文件

### Step 5：产出 Diff JSON
写入 `reports/_diff/{ticker}-{from_date}-to-{to_date}.json`，符合 `schemas/diff_report.schema.json`

### Step 6：写 Diff 报告
渲染 `templates/diff_report_template.md`（Light 或 Deep 版本，按 should_deep_dive 决定）。
写入 `reports/_diff/{ticker}-{from_date}-to-{to_date}.md`

## Sparkline 生成算法

将期间日收盘价数组归一化到 [0, 7]，映射到字符 `▁▂▃▄▅▆▇█`。
若数据点 > 30，按比例下采样到 20-30 个字符。

```
def to_sparkline(prices):
    chars = "▁▂▃▄▅▆▇█"
    lo, hi = min(prices), max(prices)
    if hi == lo: return chars[3] * len(prices)
    return "".join(chars[int((p-lo)/(hi-lo) * 7)] for p in prices)
```

## 关键约束

- **新分析完全独立**：diff_agent 不得在生成新分析时被调用。新分析的所有 agent 绝对不能读取 `reports/_raw/<TICKER>-<from_date>/` 下的文件
- **只读**：diff_agent 只读取已有 JSON，不修改任何上游文件
- **必含退出触发器状态**：`previous_exit_triggers_status` 对每个上次的触发器都必须有明确状态
