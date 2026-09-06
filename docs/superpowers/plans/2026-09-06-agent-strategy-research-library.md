# Agent 策略指标研究库 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把仓库从 WeQuant 样本转为 Agent 策略研究库：规范、catalog、海龟完整策略包、legacy 归档。

**Architecture:** 目录即契约。`catalog.yaml` 发现；`strategies/turtle/` 为唯一完整包；`.py` 全部进入 `legacy/`；`sources/` 只放原料。

**Tech Stack:** Markdown、YAML、git。无运行时、无回测。

## Global Constraints

- 本库禁止回测、下单、行情接入。
- 策略包字段名与规格 `2026-09-06-agent-strategy-research-library-design.md` 第 4 节一致。
- 市场 id 仅限：`crypto`、`a_share`、`hk`、`us`。
- 不删除文件；用 `git mv` 将 `.py` 移入 `legacy/`。
- 不 commit，除非用户另行要求。

---

### Task 1: 归档 legacy 并建立目录骨架

**Files:**
- Move: 根目录全部 `*.py` → `legacy/`
- Create: `sources/README.md`
- Create: `docs/templates/strategy-pack/` 空包文件

- [ ] **Step 1:** `mkdir -p legacy sources docs/templates/strategy-pack/overlays && git mv *.py legacy/`
- [ ] **Step 2:** 写入模板与 `sources/README.md`（字段与规格一致）
- [ ] **Step 3:** `ls legacy | wc -l` 期望 45；根目录无 `*.py`

### Task 2: Agent 契约与 catalog

**Files:**
- Create: `AGENTS.md`
- Create: `catalog.yaml`
- Modify: `README.md`
- Create: `docs/STATUS.md`

- [ ] **Step 1:** 写 `AGENTS.md`（读包顺序、缺 overlay 停止、禁止回测）
- [ ] **Step 2:** 写 `catalog.yaml`（turtle 完整条目 + usable_skeleton 的 legacy 索引）
- [ ] **Step 3:** 重写 `README.md` 为人用入口
- [ ] **Step 4:** 写 `docs/STATUS.md`

### Task 3: 海龟完整策略包

**Files:**
- Create: `strategies/turtle/strategy.md`
- Create: `strategies/turtle/prompt.research.md`
- Create: `strategies/turtle/prompt.implement.md`
- Create: `strategies/turtle/spec.yaml`
- Create: `strategies/turtle/overlays/{crypto,a_share,hk,us}.yaml`
- Create: `strategies/turtle/sources.md`

- [ ] **Step 1:** 按 `legacy/海龟策略.py` 蒸馏，不修正 ATR 公式；坑写入 `known_bugs`
- [ ] **Step 2:** 四市场 overlay 均存在；A 股写 T+1 / 涨跌停 / 禁止做空
- [ ] **Step 3:** 对照规格第 8 节走一遍发现路径（只读检查）

### Task 4: 验收

- [ ] **Step 1:** 确认根目录无 `.py`；`strategies/turtle` 文件齐全
- [ ] **Step 2:** `docs/STATUS.md` 记录验收结果
