# ZZ-MTA 1H/5M 検証ログ

## 目的

15分ZZ-MTA + 1分Trigger の検証結果が芳しくなかったため、時間軸を大きくして再検証する。

次は、1時間足ZZ-MTAをSetup、5分足B5.2をTriggerとして検証する。

---

## 背景

これまでの検証では、以下の構成を試した。

```text
Setup：15分ZZ-MTA
Trigger：1分B5.2 Fixed Pre Pivot Break
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
```

SL位置調整、RR調整を行ったが、PFは大きく改善しなかった。

### 15m/1m RR比較

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L |
|---|---|---|---|---:|---:|---:|---:|---:|---:|
| 15m | 1m | ATR x 2.0 | Zone Opposite | 0.5 | 581 | 29.60% | 0.162 | 1.03% | -17.67 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 0.7 | 568 | 40.14% | 0.291 | 0.92% | -16.21 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 0.8 | 560 | 41.07% | 0.303 | 0.97% | -17.40 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 1.0 | 542 | 42.99% | 0.330 | 1.01% | -18.64 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 1.2 | 535 | 42.06% | 0.371 | 1.02% | -19.00 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 1.5 | 527 | 38.90% | 0.438 | 0.96% | -18.19 |
| 15m | 1m | ATR x 2.0 | Zone Opposite | 2.0 | 507 | 33.14% | 0.479 | 0.96% | -18.92 |

## 15m/1m の判断

15m/1mでは、RRを上げるほどPFは改善傾向だったが、依然として実用水準には届かなかった。

```text
RR 1.0：PF 0.330
RR 1.5：PF 0.438
RR 2.0：PF 0.479
```

そのため、より大きな波を対象にするため、1時間足Setup + 5分足Triggerへ移行する。

---

## 次の検証方針

### 構成

```text
Setup：1時間足ZZ-MTA
Trigger：5分足B5.2 Fixed Pre Pivot Break
Strategy：v8.1α-ZZ-MTA-B5.2 Strategy v1.1
```

### TradingView設定

```text
チャート足：5分
ZZ-MTA計算時間足：60
SL Mode：Zone Opposite
```

この設定により、

```text
request.security() 内で1時間ZZ-MTAを計算
5分足チャート上で1時間ZZ-MTA Zoneを表示
5分足Pivot H/LでB5.2 Triggerを判定
```

となる。

---

## 初期検証条件

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Memo |
|---|---|---|---|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.0 | 初期 |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.5 | 利伸ばし |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 利伸ばし |

必要に応じて、ATR倍率も調整する。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.0 | 120 | 55.00% | 0.915 | 0.08% | -2.57 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.5 | 118 | 49.15% | 1.193 | 0.05% | 6.78 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 115 | 46.09% | 1.395 | 0.05% | 14.39 |  |

---

## 確認ポイント

```text
1. 1時間ZZ-MTA Zoneが5分足上に自然に表示されるか
2. 5分足Triggerが意図通りに出るか
3. 15m/1mよりPFが改善するか
4. Tradesが減りすぎないか
5. Max DDが悪化しないか
6. 通常表示とリプレイで挙動が一致するか
```

---

## 次の判断

1H/5Mでも改善が弱い場合は、以下を検討する。

```text
- ATR倍率調整
- 時間帯フィルター
- HTF EMAフィルター
- Trigger条件の見直し
```

## 1H/5M 初回検証結果

1時間足ZZ-MTA + 5分足B5.2 Triggerで初回BTを実施。

### 条件

```text
Setup：1時間足ZZ-MTA
Trigger：5分足B5.2 Fixed Pre Pivot Break
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
```

### 結果

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.0 | 120 | 55.00% | 0.915 | 0.08% | -2.57 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.5 | 118 | 49.15% | 1.193 | 0.05% | 6.78 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 115 | 46.09% | 1.395 | 0.05% | 14.39 | 現時点ベスト |

## 判断

15m/1mではPFが伸びなかったが、1H/5Mでは明確に改善した。

```text
15m/1m RR 2.0：PF 0.479
1H/5M RR 2.0：PF 1.395
```

現時点では、1H/5Mを主戦場候補として継続検証する。

## 次に試すこと

### 1. RR追加検証

RR 2.0が現時点ベストのため、さらに上を確認する。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.2 | 113 | 42.48% | 1.299 | 0.05% | 11.87 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 111 | 40.54% | 1.439 | 0.06% | 17.89 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 3.0 | 107 | 33.64% | 1.345 | 0.08% | 15.62 |  |

### 2. Trigger有効期限検証

5分足Triggerでは、既存の120分が長すぎるまたは短すぎる可能性があるため、有効期限を調整する。

初期候補：

```text
30分
60分
90分
120分
180分
240分
300分
```

比較表：

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 30m | 76 | 44.74% | 1.598 | 0.04% | 18.50 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 60m | 87 | 43.68% | 1.542 | 0.08% | 18.14 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 90m | 107 | 42.99% | 1.471 | 0.07% | 17.93 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 120m | 111 | 40.54% | 1.439 | 0.06% | 17.89 | 基準 |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 180m | 109 | 44.04% | 1.481 | 0.07% | 20.25 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 300m | 112 | 42.86% | 1.351 | 0.09% | 17.57 |  |

## メモ

1H/5Mでは、15m/1mよりノイズが減り、ZZ-MTAの波とB5.2 Triggerの相性が改善している可能性がある。

次は、RRとTrigger有効期限を優先して確認する。

## 暫定ベスト条件

1H/5M検証における現時点の暫定ベスト。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |

## 判断

15m/1mではPFが伸びなかったが、1H/5Mでは明確に改善。

現時点では以下を主戦場候補とする。

```text
Setup：1時間足ZZ-MTA
Trigger：5分足B5.2 Fixed Pre Pivot Break
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
RR：2.5
Trigger Expire：240m
```

## トレード回数について

実運用を見据える場合、PFや勝率だけでなく、トレード回数も重要。

```text
PF・勝率が高いがTradesが少ない
↓
資金効率が落ちる可能性あり
```

今後は、以下を同時に見る。

```text
- PF
- Win Rate
- Max DD
- Avg P/L
- Trades
- 資金効率
```

## 今後の検証方針

### 1. ATR倍率調整

まずは暫定ベスト条件を基準に、ZZ-MTAの波の粗さを調整する。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 1.5 | Zone Opposite | 2.5 | 240m | 166 | 36.14% | 0.963 | 0.18% | -1.82 | 回数増加候補 |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |
| 1H | 5m | ATR x 2.5 | Zone Opposite | 2.5 | 240m | 58 | 25.86% | 0.593 | 0.19% | -27.12 |  |
| 1H | 5m | ATR x 3.0 | Zone Opposite | 2.5 | 240m | 46 | 23.91% | 0.331 | 0.25% | -51.25 |  |

### 2. トレード回数を増やす方向

Tradesが少なすぎる場合は、以下を試す。

```text
- ATR倍率を下げる
- Trigger有効期限を延ばす
- RRを少し下げる
- Trigger条件を緩める
```

ただし、回数を増やしてPFや勝率が落ちた場合は、次にフィルターを追加する。

```text
- 時間帯フィルター
- HTF EMAフィルター
- 曜日フィルター
- ボラティリティフィルター
```

## 次の優先順位

```text
1. ATR倍率調整
2. TradesとPFのバランス確認
3. 必要なら時間帯フィルター
4. 必要ならHTF EMAフィルター
```

## Step 1：RR × Trigger Expire 周辺確認

暫定ベストが単発の最適値ではなく、周辺でも成績が安定するか確認する。

目的は、いわゆる「崖」ではなく「プラトー」を探すこと。

---

## 基準条件

```text
Setup TF：1H
Trigger TF：5m
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
```

暫定ベスト：

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |

---

## 検証内容

ATR x 2.0 は固定し、RRとTrigger有効期限の周辺を確認する。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 180m | 114 | 48.25% | 1.51 | 0.06% | 19.14 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 240m | 112 | 49.11% | 1.496 | 0.06% | 19.87 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.25 | 180m | 112 | 43.75% | 1.296 | 0.07% | 12.58 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.25 | 240m | 109 | 44.95% | 1.315 | 0.08% | 14.21 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 180m | 109 | 44.04% | 1.481 | 0.07% | 20.25 | 取得済み |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |

---

## 判断基準

以下を満たす組み合わせが複数あれば、プラトー候補とする。

```text
PF：1.2以上
Trades：100前後以上
Avg P/L：プラス
Max DD：大きく悪化しない
```

---

## 見るポイント

```text
1. RR 2.0〜2.5でPFが安定するか
2. Expire 180m〜240mで大きく崩れないか
3. Tradesを100前後維持できるか
4. PFだけでなく、Avg P/LとMax DDも確認する
```

---

## 次の判断

Step 1で周辺も安定するなら、次はATR倍率の微調整へ進む。

候補：

```text
ATR x 1.8
ATR x 2.0
ATR x 2.2
```

Step 1で周辺が大きく崩れる場合は、暫定ベストがピンポイント最適化の可能性があるため、時間帯フィルターや別Trigger条件を検討する。

## Step 1 結果：RR × Trigger Expire 周辺確認

ATR x 2.0固定で、RR 2.0〜2.5 / Expire 180m〜240m を確認。

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 180m | 114 | 48.25% | 1.510 | 0.06% | 19.14 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 240m | 112 | 49.11% | 1.496 | 0.06% | 19.87 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.25 | 180m | 112 | 43.75% | 1.296 | 0.07% | 12.58 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.25 | 240m | 109 | 44.95% | 1.315 | 0.08% | 14.21 |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 180m | 109 | 44.04% | 1.481 | 0.07% | 20.25 | 取得済み |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |

### 判断

RR 2.0〜2.5 / Expire 180m〜240m の範囲でPF 1.296〜1.564を維持。

単発最適ではなく、一定のプラトーがある可能性あり。

次はATR倍率の周辺確認へ進む。

## Step 2 結果：ATR倍率周辺確認

RR 2.5 / Expire 240m と、RR 2.0 / Expire 240m の2系統でATR倍率を確認。

### RR 2.5 / Expire 240m

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 1.8 | Zone Opposite | 2.5 | 240m | 128 | 35.94% | 0.970 | 0.14% | -1.83 | 回数増えるが崩れる |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.5 | 240m | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 暫定ベスト |
| 1H | 5m | ATR x 2.2 | Zone Opposite | 2.5 | 240m | 81 | 41.98% | 1.370 | 0.13% | 17.61 | 回数減 |

### RR 2.0 / Expire 240m

| Setup TF | Trigger TF | ZZ Setting | SL Mode | RR | Trigger Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | ATR x 1.8 | Zone Opposite | 2.0 | 240m | 135 | 40.74% | 1.076 | 0.09% | 3.13 | 回数増えるが弱い |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 | 240m | 112 | 49.11% | 1.496 | 0.06% | 19.87 | バランス型 |
| 1H | 5m | ATR x 2.2 | Zone Opposite | 2.0 | 240m | 83 | 44.58% | 1.241 | 0.12% | 10.82 | 回数減 |

### 判断

ATR倍率は x2.0 が最も安定。

ATR x1.8 はTradesは増えるがPFが大きく低下。  
ATR x2.2 はPFは残るがTradesが減る。

よって、次は以下の2候補を主軸とする。

| Type | ATR | RR | Expire | Trades | PF | Memo |
|---|---:|---:|---:|---:|---:|---|
| 攻め型 | 2.0 | 2.5 | 240m | 106 | 1.564 | Avg P/L重視 |
| バランス型 | 2.0 | 2.0 | 240m | 112 | 1.496 | 勝率・回数重視 |

次は時間帯別の成績確認を検討する。

## 次回方針：時間帯フィルター検証

1H/5M検証では、以下の2候補を主軸とする。

| Type | Setup TF | Trigger TF | ATR | SL Mode | RR | Trigger Expire | Trades | PF | Memo |
|---|---|---|---:|---|---:|---:|---:|---:|---|
| 攻め型 | 1H | 5m | 2.0 | Zone Opposite | 2.5 | 240m | 106 | 1.564 | Avg P/L重視 |
| バランス型 | 1H | 5m | 2.0 | Zone Opposite | 2.0 | 240m | 112 | 1.496 | 勝率・回数重視 |

## 時間帯フィルター方針

まずはJST基準で3分割して確認する。

| Time Filter | JST | Memo |
|---|---:|---|
| Tokyo | 09:00〜15:00 | 東京時間 |
| Europe | 15:00〜21:00 | 欧州時間 |
| NY | 21:00〜03:00 | NY前半・日跨ぎ |
| All | 制限なし | 基準 |

判定はSetup時刻ではなく、Trigger / Entryが発生した5分足の時刻で行う。

## 検証順

```text
1. RR 2.5 / Expire 240m で3分割テスト
2. 結果を見て、RR 2.0 / Expire 240m でも確認
3. 悪い時間帯・良い時間帯を把握
4. 必要なら4分割で再テスト
```

## 4分割候補

3分割で傾向が見えたら、以下の4分割も検討する。

| Time Filter | JST |
|---|---:|
| Tokyo | 08:00〜15:00 |
| London Early | 15:00〜18:00 |
| London NY | 18:00〜24:00 |
| NY Late | 00:00〜05:00 |

## 判断基準

```text
PFだけでなく、Tradesも重視する。
PFが高くてもTradesが少なすぎる場合は参考扱い。
悪い時間帯を削ってもTradesが十分残るかを見る。
```

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.0 | 2.5 | 240m | Tokyo 09-15 | 24 | 45.83% | 2.006 | 0.02% | 38.63 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Europe 15-21 | 31 | 51.61% | 1.355 | 0.04% | 16.25 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | NY 21-03 | 50 | 46.00% | 1.645 | 0.04% | 28.96 |  |

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Tokyo 09-15 | 24 | 45.83% | 1.567 | 0.03% | 21.75 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Europe 15-21 | 31 | 54.84% | 1.547 | 0.04% | 21.24 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | NY 21-03 | 50 | 52.00% | 1.663 | 0.04% | 26.33 |  |

##4分割比較表

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.0 | 2.5 | 240m | Tokyo 09-15 | 24 | 45.83% | 2.006 | 0.02% | 38.63 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Europe 15-21 | 31 | 51.61% | 1.355 | 0.04% | 16.25 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | NY 21-03 | 50 | 46.00% | 1.645 | 0.04% | 28.96 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Early Asia 03-09 | 24 | 37.50% | 1.178 | 0.09% | 9.20 |  |

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Tokyo 09-15 | 24 | 45.83% | 1.567 | 0.03% | 21.75 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Europe 15-21 | 31 | 54.84% | 1.547 | 0.04% | 21.24 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | NY 21-03 | 50 | 52.00% | 1.663 | 0.04% | 26.33 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Early Asia 03-09 | 25 | 40.00% | 1.189 | 0.06% | 8.93 |  |

##時間帯絞り込み

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.0 | 2.5 | 240m | Tokyo 09-15 | 24 | 45.83% | 2.006 | 0.02% | 38.63 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Europe 15-21 | 31 | 51.61% | 1.355 | 0.04% | 16.25 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | NY 21-03 | 50 | 46.00% | 1.645 | 0.04% | 28.96 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Early Asia 03-09 | 24 | 37.50% | 1.178 | 0.09% | 9.20 | 弱め |
| 1H | 5m | 2.0 | 2.5 | 240m | Tokyo + NY | 71 | 45.07% | 1.629 | 0.06% | 27.54 | 強い時間帯合成 |
| 1H | 5m | 2.0 | 2.5 | 240m | Exclude Early Asia 09-03 | 95 | 49.47% | 1.717 | 0.07% | 29.79 | 03-09除外 |

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Tokyo 09-15 | 24 | 45.83% | 1.567 | 0.03% | 21.75 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Europe 15-21 | 31 | 54.84% | 1.547 | 0.04% | 21.24 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | NY 21-03 | 50 | 52.00% | 1.663 | 0.04% | 26.33 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Early Asia 03-09 | 25 | 40.00% | 1.189 | 0.06% | 8.93 | 弱め |
| 1H | 5m | 2.0 | 2.0 | 240m | Tokyo + NY | 71 | 49.30% | 1.534 | 0.05% | 21.34 | 強い時間帯合成 |
| 1H | 5m | 2.0 | 2.0 | 240m | Exclude Early Asia 09-03 | 98 | 52.04% | 1.604 | 0.05% | 23.07 | 03-09除外 |

## Time Filter Combination Test 結果

Early Asia 03-09 を除外する組み合わせを検証。

### RR 2.5 / Expire 240m

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.0 | 2.5 | 240m | Tokyo + NY | 71 | 45.07% | 1.629 | 0.06% | 27.54 | 強い時間帯合成 |
| 1H | 5m | 2.0 | 2.5 | 240m | Exclude Early Asia 09-03 | 95 | 49.47% | 1.717 | 0.07% | 29.79 | 03-09除外 |

### RR 2.0 / Expire 240m

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Tokyo + NY | 71 | 49.30% | 1.534 | 0.05% | 21.34 | 強い時間帯合成 |
| 1H | 5m | 2.0 | 2.0 | 240m | Exclude Early Asia 09-03 | 98 | 52.04% | 1.604 | 0.05% | 23.07 | 03-09除外 |

### 判断

`Exclude Early Asia 09-03` が最有力。

Early Asia 03-09 を除外することで、Tradesを大きく減らさずPFとAvg P/Lが改善した。

主軸候補：

| Type | ATR | RR | Expire | Time Filter | Trades | PF | Memo |
|---|---:|---:|---:|---|---:|---:|---|
| 攻め型 | 2.0 | 2.5 | 240m | Exclude Early Asia 09-03 | 95 | 1.717 | Avg P/L重視 |
| バランス型 | 2.0 | 2.0 | 240m | Exclude Early Asia 09-03 | 98 | 1.604 | 勝率・安定重視 |

次は4分割、または Europe / NY の細分化を検討する。

##細分化比較表

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.0 | 2.5 | 240m | Exclude 03-09 | 95 | 49.47% | 1.717 | 0.07% | 29.79 | 現本命 |
| 1H | 5m | 2.0 | 2.5 | 240m | Europe Early 15-18 | 17 | 47.06% | 1.527 | 0.04% | 23.19 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | Europe Late 18-21 | 14 | 57.14% | 1.164 | 0.05% | 7.82 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | NY Early 21-24 | 37 | 48.65% | 2.123 | 0.04% | 43.90 |  |
| 1H | 5m | 2.0 | 2.5 | 240m | NY Late 00-03 | 15 | 40.00% | 0.847 | 0.06% |9.13 |  |

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Exclude 03-09 | 98 | 52.04% | 1.604 | 0.05% | 23.07 | 現本命 |
| 1H | 5m | 2.0 | 2.0 | 240m | Europe Early 15-18 | 17 | 47.06% | 1.186 | 0.05% | 8.21 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | Europe Late 18-21 | 14 | 64.29% | 2.135 | 0.03% | 37.00 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | NY Early 21-24 | 37 | 54.05% | 2.189 | 0.03% | 40.21 |  |
| 1H | 5m | 2.0 | 2.0 | 240m | NY Late 00-03 | 15 | 46.67% | 0.811 | 0.05% | -10.41 |  |

## Time Filter 09-24 Test 結果

`00:00〜09:00` を除外し、`09:00〜24:00` のみEntryする `Trade 09-24` を検証。

### RR 2.5 / Expire 240m

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.5 | 240m | All | 106 | 46.23% | 1.564 | 0.06% | 24.84 | 基準 |
| 1H | 5m | 2.5 | 2.5 | 240m | Exclude 03-09 | 95 | 49.47% | 1.717 | 0.07% | 29.79 | 03-09除外 |
| 1H | 5m | 2.0 | 2.5 | 240m | Trade 09-24 | 83 | 50.60% | 1.908 | 0.05% | 35.86 | 00-09除外 |

### RR 2.0 / Expire 240m

| Setup TF | Trigger TF | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---:|---:|---|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | 2.0 | 240m | All | 112 | 49.11% | 1.496 | 0.06% | 19.87 | 基準 |
| 1H | 5m | 2.0 | 2.0 | 240m | Exclude 03-09 | 98 | 52.04% | 1.604 | 0.05% | 23.07 | 03-09除外 |
| 1H | 5m | 2.0 | 2.0 | 240m | Trade 09-24 | 86 | 52.33% | 1.734 | 0.04% | 27.17 | 00-09除外 |

### 判断

`Trade 09-24` が現時点の本命。

`00:00〜09:00` を除外することで、Tradesはやや減少するが、PF・Avg P/L・Max DDが改善した。

主軸候補：

| Type | ATR | RR | Expire | Time Filter | Trades | Win Rate | PF | Avg P/L | Memo |
|---|---:|---:|---:|---|---:|---:|---:|---:|---|
| 攻め型 | 2.0 | 2.5 | 240m | Trade 09-24 | 83 | 50.60% | 1.908 | 35.86 | 現本命 |
| バランス型 | 2.0 | 2.0 | 240m | Trade 09-24 | 86 | 52.33% | 1.734 | 27.17 | 安定候補 |

次は `Trade 09-24` 固定で、RR周辺確認を行う。

##　プラトー探索

| Setup TF | Trigger TF | ATR | SL Mode | Time Filter | RR | Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 180m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 86 | 52.33% | 1.734 | 0.04% | 27.17 | 取得済み |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.25 | 180m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.25 | 240m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.25 | 300m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 180m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 83 | 50.60% | 1.908 | 0.05% | 35.86 | 現本命 |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 300m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.75 | 180m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.75 | 240m |  |  |  |  |  |  |
| 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.75 | 300m |  |  |  |  |  |  |

##次

1. Trade 09-24固定でRR/Expireのプラトー確認

2. 良い中心値を決める

3. 回数増加テストとして Trade 08-24 を試す

4. それでも回数不足なら Trade 08-01 や Expire延長を検討
