# Agent 规则

本仓库是策略指标**研究库**。核心交付是策略包里的伪代码、`spec.yaml`、市场 overlay、研究提示词与实现提示词。

## 必读顺序

1. 本文件
2. `catalog.yaml`
3. `strategies/<id>/` 内文件（不要从 `legacy/` 或 `sources/` 直接实现）

## 任务分流

- 研究：`prompt.research.md` + `strategy.md` + `spec.yaml` + 目标 overlay
- 实现（外部回测系统）：`prompt.implement.md` + `spec.yaml` + 目标 overlay
- 蒸馏经验：先读 `sources/`，产出新的 `strategies/<id>/`，不得把原文当 spec

## 硬禁止

- 禁止在本仓库回测、拉行情、下单、写券商/交易所客户端
- 禁止编造回测净值或收益承诺
- 目标市场不在 `spec.markets_supported` 或缺少 `overlays/<market>.yaml` 时必须停止，不得臆造 T+1、涨跌停、做空、手数规则
- 禁止删除文件；归档用移动到 `legacy/` 或 `sources/`
- `known_bugs` 默认原样实现并标注；用户明确要求修复时才改公式

## 市场 id

仅使用：`crypto`、`a_share`、`hk`、`us`。

## 新建策略包

复制 `docs/templates/strategy-pack/`，目录名与 `spec.id` 一致，并在 `catalog.yaml` 增加索引条目（不要把完整逻辑复制进 catalog）。
