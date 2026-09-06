# STATUS

## Active milestone

首个研究基线：目录即契约 + 海龟完整包。

- Branch: master（工作区未提交）
- Plan: `docs/superpowers/plans/2026-09-06-agent-strategy-research-library.md`
- Spec: `docs/superpowers/specs/2026-09-06-agent-strategy-research-library-design.md`

## Done

- 45 个 WeQuant `.py` 移到 `legacy/`（未删除）
- `AGENTS.md`、`README.md`、`catalog.yaml`、模板、`sources/README.md`
- `strategies/turtle/` 含两份 prompt、`spec.yaml`、四市场 overlay

## Verification

- 根目录无 `.py`
- `legacy/*.py` = 45
- 发现路径文件均存在：`AGENTS.md` → `catalog.yaml` → `strategies/turtle/`
- 四个 overlay：crypto / a_share / hk / us

未做：回测、独立 Agent 真人走查、git commit

## Next

- 用户审阅后按需 commit
- 下一里程碑：其余 `usable_skeleton` 写成策略包（网格、Dual Thrust、KDJ 等）
- 向 `sources/` 放入交易笔记后再蒸馏

## Blocked

无
