# Persona 路由规则

## 市场维度

| 市场 | Dalio | Munger | Druckenmiller |
|------|-------|--------|---------------|
| 美股 (US) | 全权重 1.0 | 全权重 1.0 | 全权重 1.0 |
| 港股 (HK) | 权重 0.8（汇率/中美博弈加强） | 权重 0.6（避开内地业务模式判断） | 权重 0.9 |
| A 股 (CN) | 权重 0.6（其框架对 PBoC 适用性有限） | 权重 0.3（跳过"管理层信任"维度） | 权重 0.7 |

## 行业维度

| 行业 | Munger 跳过维度 | 备注 |
|------|----------------|------|
| 半导体/AI/SaaS | circle_of_competence、business_model_durability | Munger 自承不擅长科技估值 |
| 生物技术/创新药 | circle_of_competence | 同上 |
| 加密货币相关 | 全跳过 Munger 层 | 立场已知偏激 |
| 传统消费/金融/工业 | 不跳过 | Munger 舒适区 |

## 知识截止处理

| Persona | 知识截止 | 处理方式 |
|---------|---------|---------|
| Munger | 2023-11 | 涉及 2024+ 事件需标注"基于历史框架推断" |
| Dalio | 2025-Q1 | 同上 |
| Druckenmiller | 2024-Q4 | 同上 |

## 路由决策输出格式

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

## GICS 行业 → Munger 跳过维度映射

| GICS 行业 | skip_dimensions |
|-----------|----------------|
| Semiconductors, Software, IT Services, Tech Hardware | circle_of_competence, business_model_durability |
| Biotechnology, Pharmaceuticals | circle_of_competence |
| Cryptocurrency / Blockchain 相关 | 全跳过（不调用 Munger） |
| Banks, Insurance, Consumer Staples, Industrials, Materials, Energy | 无跳过 |
| 其他 | 默认无跳过，除非定性画像中判定为"高科技创新型" |
