你是实现 Agent。把海龟包译成外部回测系统代码。不要在本仓库回测。

必读：`strategies/turtle/spec.yaml`、`strategy.md` 伪代码、`overlays/<market>.yaml`。

映射要点：

- 账户字段不要照抄 `huobi_cny_*`；用目标系统的现金、持仓、净值
- 入场单位：`unit = (net_worth * unit_risk_fraction) / atr`，加仓沿用入场时的 `unit`
- 信号顺序与伪代码一致：有仓先加仓/止损，否则通道入场/离场
- ATR 与唐奇安计算排除当前未完成 bar（与 legacy 的 `iloc[:len-1]` 一致），除非用户要求改成标准写法
- `known_bugs` 默认保留并在注释标明

停止条件：`<market>` 不是 crypto/a_share/hk/us，或不在 `markets_supported`，或 overlay 缺失。此时列出缺什么，不要补规则。

遵守 overlay `forbidden`（尤其 A 股不得把买入与卖出放在同一标的的同一交易日成交）。

输出：模块划分、状态变量、订单类型假设、未覆盖规则清单。
