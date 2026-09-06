# 海龟

趋势跟随。赌的是价格能离开近端震荡区间并延续。仓位按波动（ATR）计单位，不是全仓开关。

本包描述 `legacy/海龟策略.py` 的实际逻辑，不是教材里的完整海龟系统（没有双重通道 N/2N 入场分离、没有 S1/S2、没有空头）。

## 状态

- `hold_flag`，`last_buy_price`，`unit`，`add_time`，`limit_unit=4`
- 离场或止损后调用重置：清空持仓标志与单位仓

## 伪代码

```
T = 20
need = T + 1 bars
if bars < need: return

hist_for_channels_and_atr = all bars except the newest row
price = current price
atr = mean(TR_i for i in hist_for_channels_and_atr)
  TR_i = max(H_i-L_i, H_i-C_{i-1}, C_{i-1}-L_i)  # i=0 uses C[-1] wrap

if in_position and qty > 0:
  if price >= last_buy_price + 0.5 * atr and add_time < 4:
      buy min(cash, unit * price)
      last_buy_price = price
      add_time += 1
  elif price <= last_buy_price - 2 * atr:
      flatten
      reset state
else:
  up = max(high[-T:] of hist_for_channels_and_atr)
  down = min(low[-T/2:] of hist_for_channels_and_atr)
  if price > up:
      unit = (net_worth * 0.01) / atr
      add_time = 1
      hold_flag = true
      last_buy_price = price
      buy min(cash, unit * price)
  elif price < down:
      if hold_flag: flatten; reset state
```

处理顺序与 legacy 一致：先有仓则只做加仓/止损，无仓（或数量为 0）才做通道入场/离场。因此「有仓但数量已是 0」会走通道分支。

## 与教科书的差异

- 只做多
- 出场是 T/2 下轨，不是完整 N 日离场通道
- ATR 不是 Wilder 平滑的 20 日 ATR
- 加仓与止损相对**上次买入价**，不是系统的整段均价

## 何时不该用

无趋势、成本高、无法按 ATR 拆成多笔单的标的；需要做空对冲的市场（本核没有空头规则）。A 股还要叠加 T+1，见 overlay。

## Legacy 对照

- `legacy/海龟策略.py`（T=20，日线，BTC 教学账户字段）
- `legacy/ETH-海龟策略.py`（T=5，同一套信号）
