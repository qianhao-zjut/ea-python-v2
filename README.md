# ea-python

Agent 用的**策略指标研究库**。核心输出是策略包：伪代码、`spec.yaml`、分市场 overlay、研究提示词、实现提示词。

不负责回测、行情、下单。回测在其它系统做。

标的规则覆盖数字货币、A 股、港股、美股（写在 overlay 里，不接数据）。

## 给 Agent

1. 读 `AGENTS.md`
2. 读 `catalog.yaml` 选包
3. 打开 `strategies/<id>/`

当前完整包只有 `strategies/turtle/`（海龟）。其余 WeQuant 脚本在 `legacy/`，只当参考。

## 给维护者

- 新策略：复制 `docs/templates/strategy-pack/`，填字段，更新 `catalog.yaml`
- 个人经验原文进 `sources/`，蒸馏后再建策略包
- 旧教学脚本：`legacy/`，来源 [zhy0313/ea-python](https://github.com/zhy0313/ea-python)（WeQuant 火币 CNY 现货样本，约 2014–2017）

规格：`docs/superpowers/specs/2026-09-06-agent-strategy-research-library-design.md`  
进度：`docs/STATUS.md`
