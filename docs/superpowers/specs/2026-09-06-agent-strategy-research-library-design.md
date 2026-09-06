# 设计规格：Agent 策略指标研究库

日期：2026-09-06  
状态：待用户审阅书面规格后进入实现计划  
仓库：ea-python（将从 WeQuant 教学样本转为研究库）

## 1. 问题与产品边界

现有仓库是 WeQuant / 火币 CNY 现货教学脚本（`PARAMS` → `initialize` → `handle_data`），绑已停用 API，不能实盘。目标使用者改为 Agent：研究策略指标、写出可交接的伪代码和提示词。回测、下单、行情接入由其它系统负责。

主用户：研究 Agent、实现 Agent（在外部回测系统写代码）、仓库维护者（蒸馏经验、审策略包）。

主任务：把可研究的策略/指标沉淀为策略包。其它系统只消费伪代码、`spec.yaml` 和市场 overlay。

定位：Agent 用的策略指标研究库。覆盖数字货币、A 股、港股、美股的规则差异，但不接入这些市场的数据。

标的范围写在策略包的 `markets_supported` 与 overlay 中。没有 overlay 的市场视为未支持，实现 Agent 必须停止，不得臆造规则。

## 2. 明确非目标

本仓库不做：回测引擎、绩效统计、实盘与券商/交易所 API、行情拉取、把全部旧脚本迁成策略包、把 `sources/` 原文直接当作可实现 spec。

旧代码中的公式错误写入 `known_bugs`。首个示例（海龟）不借机「修正成新策略」。

## 3. 架构：目录即契约

Agent 发现与消费全部走文件系统和 git。不提供搜索 API、MCP、Skill 包装（可列为后续里程碑）。

```text
AGENTS.md
README.md
catalog.yaml
docs/
  STATUS.md
  specs/
  templates/strategy-pack/     # 空策略包
strategies/
  <id>/
    strategy.md
    prompt.research.md
    prompt.implement.md
    spec.yaml
    overlays/
      crypto.yaml
      a_share.yaml
      hk.yaml
      us.yaml
    sources.md                 # 可选，链到 sources/
legacy/                        # 冻结的 WeQuant .py
sources/                       # 个人交易经验原文
```

消费顺序：`AGENTS.md` → `catalog.yaml` → `strategies/<id>/`。研究任务读 `prompt.research.md`、`strategy.md`、`spec.yaml`。实现任务读 `prompt.implement.md`、`spec.yaml`、目标市场 overlay。`legacy/` 与 `sources/` 默认不可直接实现。

`catalog.yaml` 只做索引（id、风格、质量、已支持市场、路径、一句话 intent）。完整逻辑只存在于策略包内，避免双源。

## 4. 策略包契约

### 4.1 `spec.yaml`（市场无关核）

必填字段：

| 字段 | 含义 |
|---|---|
| `id` | 稳定标识，与目录名一致 |
| `title` | 显示名 |
| `style` | `trend` / `mean_reversion` / `allocation` / `conditional_order` |
| `quality` | `usable_skeleton` / `teaching_template` / `indicator_misuse` |
| `intent` | 一句话：这个规则在赌什么 |
| `indicators` | 名称、输入（如 OHLCV）、窗口、公式要点 |
| `signals` | 入场、出场、加仓、忽略条件（伪代码级，不绑交易所 API） |
| `position` | 全仓 / 单位仓 / 目标权重，以及数量怎么算 |
| `stops` | 价格止损、账户回撤、永久停机 |
| `params` | 默认可调参数及含义 |
| `failure_modes` | 已知失效场景 |
| `known_bugs` | 从 legacy 继承的实现坑；无则空列表 |
| `source_kind` | `legacy` 或 `distilled` |
| `legacy_files` | `legacy/` 下相对路径列表 |
| `markets_supported` | 已存在 overlay 的市场 id 列表 |

市场 id 固定为：`crypto`、`a_share`、`hk`、`us`。

信号与仓位用市场无关语言（bar、close、ATR、净值）。下单 API、账户字段名留给实现 Agent 按目标系统设计。

### 4.2 `overlays/<market>.yaml`

只写与核的差异。建议字段：`market`、`session`、`t_plus`、`price_limit`、`short_allowed`、`lot_rules`、`cost_assumptions`（费率/滑点假设，供实现约束，不是本库算出来的结果）、`param_overrides`、`forbidden`（实现时禁止项）、`suitability`（推荐 / 需改参 / 默认不推荐及原因）。

四个市场在示例包中都要有 overlay。某市场默认不推荐时，overlay 仍必须存在并写明原因，避免「文件缺失被当成可以自由发挥」。

### 4.3 人读与提示词

`strategy.md`：直觉、逐步伪代码、与教科书差异、何时不该用、legacy 对照。

`prompt.research.md`：判断是否适用、如何改参、如何对照 `sources/` 原料。禁止编造回测净值。

`prompt.implement.md`：把 spec+overlay 译成外部回测代码的契约；禁止在本仓库跑回测；禁止补全缺失的市场规则。

两份提示词不得合并成一份。

### 4.4 质量标签

沿用现有评审：

- `usable_skeleton`：逻辑自洽，可给实现 Agent
- `teaching_template`：能讲清指标，过简或费用敏感
- `indicator_misuse`：指标用法错误，先修再谈实现

首个里程碑只把 `usable_skeleton` 作为「可迁成完整包」的候选。教学模板与误用策略可仅出现在 catalog 的 legacy 索引中。

## 5. 经验原料与蒸馏

`sources/` 只存放个人交易经验原文（复盘、笔记）。未蒸馏前不得当作 spec。

蒸馏产出才是 `strategies/<id>/`，`source_kind: distilled`，并用 `sources.md` 或 spec 内引用链回原文。

Agent 默认只引用成品策略包。读 `sources/` 仅用于研究提示词里的「对照原料」步骤。

## 6. Legacy 处理

全部现有 `.py` 移入 `legacy/`。根目录不再放置可执行 EA 脚本。

README 中的逐策略对照迁到 catalog 的 legacy 条目或 `docs/` 下的对照表，避免根 README 与策略包冲突。

本里程碑不批量把可用骨架全部写成包。完整包只做海龟（`turtle`），对应 `海龟策略.py`；ETH 克隆在 catalog 中标注为同一逻辑的标的变体，不另建包。

已知 ATR 均值与首根 TR 环绕问题写入海龟包 `known_bugs`，不在本里程碑改写公式。

## 7. 决策记录

### Decision: 交付形态为策略包目录而非纯文档或工具层

- Problem and constraints: Agent 需要可筛选、可实现的契约；本库不负责回测。
- Candidates: 纯 Markdown 库；目录即契约；再包 Skill/API。
- Current recommendation: 目录即契约（`strategy.md` + 两份 prompt + `spec.yaml` + overlays）。
- Why this fits now: git 可审、无运行时、实现 Agent 能按字段翻译。
- Reconsider when: 策略数量大到 catalog 不够用，或需要统一 MCP 入口。
- Explicit non-goals: 本阶段不做搜索服务。

### Decision: 通用核 + 市场 overlay

- Problem and constraints: 四市场规则不同，指标逻辑应一份。
- Candidates: 仅标签约束；核+overlay；按市场拆包。
- Current recommendation: 核+overlay。
- Why this fits now: 减少重复，且强制写出 T+1、涨跌停等差异。
- Reconsider when: 某策略在不同市场已完全不是同一规则。
- Explicit non-goals: 本库不验证 overlay 与真实交易所规则的实时一致性。

### Decision: 只迁可用骨架，首包为海龟

- Problem and constraints: 45 个脚本质量不齐，全迁会污染 Agent 检索。
- Candidates: 全迁；只迁骨架；全部冻结只写模板。
- Current recommendation: 规范 + 海龟完整包；其余 `.py` 进 legacy。
- Why this fits now: 海龟含单位仓、加仓、止损，能撑满 spec 字段。
- Reconsider when: 首包被接受后，下一里程碑迁网格、Dual Thrust、KDJ 等。
- Explicit non-goals: 本里程碑不修 ADX/NATR/WILLR 等误用策略。

### Decision: 经验原文与成品包分离

- Problem and constraints: 个人经验会入库，但不能未整理就给实现 Agent。
- Candidates: 只作原料；与策略平级检索；挂在已有策略 notes 下。
- Current recommendation: `sources/` 原料，蒸馏后才成为策略包。
- Why this fits now: 与「核心输出是伪代码和提示词」一致。
- Reconsider when: 需要给未成形直觉单独做轻量卡片（曾讨论的方案 B）。
- Explicit non-goals: 自动从笔记生成可交易 spec。

## 8. 首个里程碑（可部署研究基线）

交付：

1. `AGENTS.md`：只研究、禁止回测、读包顺序、缺 overlay 则停止。
2. `README.md`：给人看的范围、目录、如何给外部回测系统交接。
3. `docs/STATUS.md`：当前阶段与验收证据。
4. `docs/templates/strategy-pack/`：空包，字段与第 4 节一致。
5. `catalog.yaml`：海龟完整条目；可选列出 legacy 策略索引（指向 `legacy/*.py`，无完整包路径）。
6. `strategies/turtle/`：完整示例，含四个 overlay。
7. 现有 `.py` 全部位于 `legacy/`。
8. `sources/`：约定说明 + 占位，不要求已有笔记。

验收：

- 未参与实现的 Agent 只读 `AGENTS.md` 与 `catalog.yaml` 能找到海龟并知道打开哪些文件。
- 研究提示词能回答：该不该用、A 股相对 crypto 必须改什么。
- 实现提示词给出可翻译契约，并声明本库无回测结果。
- 缺少目标市场 overlay 时行为是停止。
- 根目录无 WeQuant 风格 EA 脚本。

风险：中。跨文档契约、后续多 Agent 依赖字段名。需要书面规格、模板与示例包一致、里程碑结束时对照规格做一次独立阅读检查。不做回测所以没有运行时门禁；用「按 AGENTS.md 走一遍发现路径」代替 E2E。

## 9. 后续里程碑（非本规格实现范围）

- 将其余 `usable_skeleton` 写成策略包（网格、动态平衡、Dual Thrust、阿隆、KDJ、均值回归、EMA 回撤止损、CCI 等）。
- 从 `sources/` 蒸馏新包。
- 可选：列出/读取策略包的 Skill 或脚本入口。

## 10. 安全与合规

研究库可描述公开技术指标与仓位规则。不存放券商密钥、账户、未公开的实盘成交。`cost_assumptions` 只是实现约束，不是投资建议。`AGENTS.md` 须写明：输出供研究与外部回测实现，不构成荐股或保证收益。
