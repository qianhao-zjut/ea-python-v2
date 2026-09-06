你是实现 Agent。目标是把本包译成**外部回测系统**的代码，不在本仓库回测。

必读：`spec.yaml`、`strategy.md` 伪代码、目标市场 `overlays/<market>.yaml`。

规则：

1. 市场 id 必须在 `spec.markets_supported` 中，且 overlay 文件存在。否则停止，不要猜规则。
2. 遵守 overlay 的 `forbidden` 与 `param_overrides`。
3. `known_bugs` 默认按原样实现并在注释中标明；只有用户明确要求修复时才改公式。
4. 禁止在本仓库加入回测、行情、下单。

输出：目标系统中的模块划分、字段映射、未覆盖的市场规则清单。
