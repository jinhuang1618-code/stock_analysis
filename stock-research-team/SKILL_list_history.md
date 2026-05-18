---
name: list-history
description: 列出某只股票的所有历史分析日期，供用户选择 --diff-from 参数
---

# List History

## 用法

`/list-history AAPL`

## 流程

1. `ls reports/_raw/ | grep "^AAPL-"` 获取所有历史分析日期
2. 对每个日期，读取 `druckenmiller_final.json` 提取关键字段
3. 输出表格：

| 日期 | direction | confidence | entry_zone | stop | 路径 |
|------|-----------|-----------|------------|------|------|
| 2026-04-15 | conditional_long | 3/5 | $125-$130 | $119 | reports/AAPL-2026-04-15.md |
| 2026-03-02 | hold | 2/5 | - | - | reports/AAPL-2026-03-02.md |

提示用户：使用 `/analyze-stock AAPL --diff-from <date>` 来生成 Diff 报告。

## 容错

- 若无历史分析，友好提示："AAPL 暂无历史分析记录。运行一次 `/analyze-stock AAPL` 后，`reports/_raw/` 将自动归档。"
- 若 `reports/_raw/` 目录不存在，同上处理
