# ZZ-MTA 1H/5M Time Filter 検証ログ

## 対象Strategy

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.2.x
```

## 基本条件

```text
Setup TF：1H
Trigger TF：5m
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
Time Filter：Trade 09-24
```

## Time Filter 方針

`Trade 09-24` は、JST 09:00〜24:00のみEntryする。

```text
取引する時間：09:00〜24:00
除外する時間：00:00〜09:00
```

## 暫定3候補

| Type | Setup TF | Trigger TF | ATR | SL Mode | Time Filter | RR | Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| 攻め型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 83 | 50.60% | 1.908 | 0.05% | 35.86 | 現時点本命 |
| 安定型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 86 | 52.33% | 1.734 | 0.04% | 27.17 | 勝率重視 |
| 回数寄り | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m | 93 | 51.61% | 1.595 | 0.05% | 25.01 | Trades重視 |

## 判断

`Trade 09-24` は、`00:00〜09:00` を除外することでPF・Avg P/Lが改善した。

現時点では、以下の3タイプを残す。

```text
攻め型：RR 2.5 / Expire 240m
安定型：RR 2.0 / Expire 240m
回数寄り：RR 2.0 / Expire 300m
```

## 08時台追加テスト

`Trade 08-24` も試したが、Tradesは少し増える一方でPF・Avg P/Lが低下した。

| Type | Time Filter | RR | Expire | Trades | PF | Avg P/L | 判断 |
|---|---|---:|---:|---:|---:|---:|---|
| 攻め型 | Trade 09-24 | 2.5 | 240m | 83 | 1.908 | 35.86 | 採用候補 |
| 攻め型 | Trade 08-24 | 2.5 | 240m | 85 | 1.827 | 33.24 | 回数増・PF低下 |
| 安定型 | Trade 09-24 | 2.0 | 240m | 86 | 1.734 | 27.17 | 採用候補 |
| 安定型 | Trade 08-24 | 2.0 | 240m | 88 | 1.660 | 24.87 | 回数増・PF低下 |
| 回数寄り | Trade 09-24 | 2.0 | 300m | 93 | 1.595 | 25.01 | 採用候補 |
| 回数寄り | Trade 08-24 | 2.0 | 300m | 96 | 1.511 | 21.90 | 回数増・PF低下 |

## 現時点の結論

08時台追加は一旦見送り。

主軸は `Trade 09-24` とする。

## 次に検証する候補

```text
1. 曜日別成績
2. 月別・年別の安定性
3. 期間分割テスト
4. スプレッド/コスト耐性
5. 回数増加策の追加検討
```

## メモ

今後はPFだけでなく、Tradesも重視する。  
実運用・EA化を見据え、資金効率の観点から「PFを大きく崩さずにTradesを増やせるか」を確認する。

## 備考

現時点ではUSDJPY専用候補。

他通貨ペアでは同条件の再現性なし。

通貨ペア汎用性は未確認、または低い可能性あり。

##次

1. USDJPYでOOS確認

2. OOSで耐えたらフォワードへ移行

3. 他通貨ペアは一旦追わない

4. 他通貨でやるなら、同じパラメーターを流用せず、別手法扱いで検証

## OOS-A  
IS 2021/01/01から2024/12/31  
OOS 2025/01/01から2025/12/31

| Pair | Period Type | Period | Type | Setup TF | Trigger TF | ATR | SL Mode | Time Filter | RR | Expire | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---|---|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| USDJPY | IS |  | 攻め型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 63 | 49.21% | 1.911 | 0.05% | 37.38 | 現本命 |
| USDJPY | OOS |  | 攻め型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 20 | 55.00% | 1.891 | 0.06% | 30.62 |  |
| USDJPY | IS |  | 安定型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 65 | 52.31% | 1.840 | 0.04% | 31.35 | 勝率重視 |
| USDJPY | OOS |  | 安定型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 21 | 52.38% | 1.399 | 0.04% | 13.50 |  |
| USDJPY | IS |  | 回数寄り | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m | 71 | 50.70% | 1.667 | 0.04% | 28.32 | Trades重視 |
| USDJPY | OOS |  | 回数寄り | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m | 22 | 54.55% | 1.359 | 0.03% | 13.80 |  |

## フォワード検証へ移行

OOS-Aで3候補すべてがPF 1.0を上回り、Avg P/Lもプラスだったため、フォワード検証へ移行する。

### OOS-A 期間

```text
IS：2021/01/01〜2024/12/31
OOS：2025/01/01〜2025/12/31
```

### OOS-A 判断

| Type | IS PF | OOS PF | OOS Trades | OOS Avg P/L | Judge |
|---|---:|---:|---:|---:|---|
| 攻め型 | 1.911 | 1.891 | 20 | 30.62 | 合格 |
| 安定型 | 1.840 | 1.399 | 21 | 13.50 | 合格 |
| 回数寄り | 1.667 | 1.359 | 22 | 13.80 | 合格 |

## フォワード対象

まずは以下の2候補を優先する。

| Priority | Type | Pair | Setup TF | Trigger TF | ATR | SL Mode | Time Filter | RR | Expire | Memo |
|---|---|---|---|---|---:|---|---|---:|---:|---|
| 1 | 攻め型 | USDJPY | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 第一候補 |
| 2 | 安定型 | USDJPY | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 比較候補 |
| 3 | 回数寄り | USDJPY | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m | 補欠 |

## フォワードの目的

フォワードでは利益額より、BTとの挙動一致を重視する。

確認項目：

```text
1. TradingView上のシグナルが想定通り出るか
2. 1H ZZ-MTA Zoneと5m Triggerの挙動がBT・リプレイと一致するか
3. Entry時刻がTrade 09-24内に限定されているか
4. SL/TPが想定通り配置されるか
5. スプレッド・スリッページで期待値が大きく崩れないか
6. エントリー頻度がBT想定から大きくズレないか
```

## フォワード記録表

| Date | Pair | Type | Signal Time JST | Direction | Entry | SL | TP | Result | P/L | Memo |
|---|---|---|---|---|---:|---:|---:|---|---:|---|
|  | USDJPY | 攻め型 |  |  |  |  |  |  |  |  |
|  | USDJPY | 安定型 |  |  |  |  |  |  |  |  |

## 週次集計表

| Week | Type | Signals | Trades | Wins | Losses | Win Rate | PF | Net P/L | Memo |
|---|---|---:|---:|---:|---:|---:|---:|---:|---|
|  | 攻め型 |  |  |  |  |  |  |  |  |
|  | 安定型 |  |  |  |  |  |  |  |  |

## 判断基準

```text
短期の勝ち負けで判断しない。
まずは20〜30トレード程度まで挙動確認を優先する。
```

暫定基準：

| Judge | Criteria |
|---|---|
| 継続 | BTと挙動が一致し、PF 1.0以上またはAvg P/Lプラス |
| 条件付き継続 | PF 1.0未満でも、挙動一致・DD小・サンプル不足 |
| 停止検討 | BTと挙動が違う、または想定外のEntry/SL/TPが発生 |
| 停止 | 重大なロジック不一致、連続した想定外損失 |

## 注意

現時点ではUSDJPY専用候補として扱う。  
他通貨ペアでは同条件の再現性が低かったため、汎用FX手法ではなく、USDJPYの時間帯・波形特性に依存した手法として検証する。
