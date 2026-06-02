# ZZ-MTA SL位置調整ログ

## 対象

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.0
```

## 現状結果

| MTA Type | Trigger | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---|
| Pivot MTA | B5.2 | none | Zone Opposite | 1.0 | 922 | 30.37% | 0.259 | 1.58% | -17.09 | 既存基準 |
| ZZ-MTA | B5.2 | ATR x 2.0 | Zone Opposite | 1.0 | 542 | 42.99% | 0.330 | 1.01% | -18.64 | v1.0 |

## 現状評価

ZZ-MTA化により、取引数は減少し、勝率・PF・Max DDは改善した。

```text
Trades：922 → 542
Win Rate：30.37% → 42.99%
PF：0.259 → 0.330
Max DD：1.58% → 1.01%
```

ただし、Avg P/Lは悪化しているため、次はSL位置調整を行う。

---

## SL調整の目的

同じSetup / Triggerのまま、SL位置だけを変更し、損益構造が改善するか確認する。

主な確認ポイント：

```text
- PFが改善するか
- Avg P/Lが改善するか
- 勝率が大きく崩れないか
- Tradesが減りすぎないか
- Max DDが悪化しないか
```

---

## 検証するSL Mode

### 1. Zone Opposite

現行方式。

```text
Long SL：ZZ-MTA Zone Bottom
Short SL：ZZ-MTA Zone Top
```

### 2. Zone Body

ゾーン実体側を使う浅めSL。

```text
Long SL：ZZ-MTA Zone Top
Short SL：ZZ-MTA Zone Bottom
```

### 3. Setup Candle

Setup発生足の高値/安値を使う。

```text
Long SL：Setup接触足のLow
Short SL：Setup接触足のHigh
```

参考：

```text
MTA v8.1α-A-B5.2 Strategy v1.3 Setup Candle SL
```

### 4. PreBreak Pivot

Setup後、Trigger前に形成された反対側Pivotを使う。

```text
Long SL：Pre Break H以降に形成されたPivot Low
Short SL：Pre Break L以降に形成されたPivot High
```

参考：

```text
MTA v8.1α-A-B5.2 Strategy v1.4 PreBreak-Trigger Pivot SL
```

---

## 次に作るもの

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.1
SL Mode Test
```

## 初期検証条件

```text
MTA：15分ZZ-MTA
Trigger：B5.2 Fixed Pre Pivot Break
ZZ Setting：ATR x 2.0
RR：1.0
```

##確認ポイント

1. Zone Opposite が v1.0 と同じ結果になるか

2. Setup Candle でSetup足のLow/HighがSLになるか

3. PreBreak Pivot でSL用Pivotがない時はEntryしないか

4. PreBreak Pivot でSL用Pivotがある時だけEntryするか

## 比較表テンプレート

| MTA Type | Trigger | ZZ Setting | SL Mode | RR | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---|---:|---:|---:|---:|---:|---:|---|
| ZZ-MTA | B5.2 | ATR x 2.0 | Zone Opposite | 1.0 | 542 | 42.99% | 0.330 | 1.01% | -18.64 | 現行 |
| ZZ-MTA | B5.2 | ATR x 2.0 | Zone Body | 1.0 |  |  |  |  |  | 浅めSL |
| ZZ-MTA | B5.2 | ATR x 2.0 | Setup Candle | 1.0 |  |  |  |  |  | Setup足SL |
| ZZ-MTA | B5.2 | ATR x 2.0 | PreBreak Pivot | 1.0 |  |  |  |  |  | Trigger前Pivot SL |

## 次の判断

SL Mode Testで改善が見えたら、良かったSL Modeに絞ってRR調整を行う。

その後、時間帯フィルターやHTF EMAフィルターを検討する。
