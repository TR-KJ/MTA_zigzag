# Roadmap：ZigZag MTA 再定義プロジェクト

## 目的

現在のMTAはPivotベースで定義しているが、Pivotの `leftBars / rightBars` に依存しやすい。

本プロジェクトでは、MTAをPivotではなく **確定済みZigZag Swing** で再定義し、裁量で見ている「波」により近いMTAを作れるか検証する。

---

## 基本方針

- 未確定ZigZagはStrategyに使わない
- 確定済みZigZagのみ使用する
- ルックアヘッドバイアスを避ける
- 通常表示とリプレイで挙動が一致することを重視する
- いきなりB1/B2/B5.2へ統合しない
- まずZigZag単体 → ZigZag MTA → Trigger統合の順番で進める

---

## Step 1：Confirmed ZigZag Swing Engine

### 目的

15分足上で、Strategyに使える確定済みZigZag High / Lowを検出する。

### 内容

- ZigZag High表示
- ZigZag Low表示
- ZigZagライン表示
- 確定タイミングの確認
- 通常表示とリプレイの一致確認

### 検討パラメータ

- ATR倍率反転
- 固定pips反転
- パーセント反転

初期案：

```text
15分ATR × 1.0
15分ATR × 1.5
15分ATR × 2.0
```

### 成果物

```text
v8.1α-ZZ-MTA-0.1
Confirmed ZigZag Swing Engine
```

---

## Step 2：ZigZag MTA Zones

### 目的

確定済みZigZag High / Lowを使って、MTAゾーンを再定義する。

### Bull MTA案

```text
ZigZag Highが前回Highを上抜け
↓
直近ZigZag Lowを押し安値MTAとして採用
↓
そのZigZag Lowのヒゲ〜実体範囲をゾーン化
```

### Bear MTA案

```text
ZigZag Lowが前回Lowを下抜け
↓
直近ZigZag Highを戻り高値MTAとして採用
↓
そのZigZag Highのヒゲ〜実体範囲をゾーン化
```

### 確認項目

- MTA発生位置
- MTAゾーンの自然さ
- 対応高値/安値の表示
- 失効条件
- 通常表示とリプレイの一致

### 成果物

```text
v8.1α-ZZ-MTA-0.2
Confirmed ZigZag MTA Zones
```

---

## Step 3：1分足への15分ZigZag MTA表示

### 目的

15分足で定義したZigZag MTAを、1分足チャート上に表示する。

### 方針

```pine
request.security(syminfo.tickerid, "15", calcZigZagMta(), gaps = barmerge.gaps_off, lookahead = barmerge.lookahead_off)
```

### 確認項目

- 15分足表示と1分足表示が一致するか
- ゾーンがズレないか
- リプレイで挙動が安定するか

### 成果物

```text
v8.1α-ZZ-MTA-0.3
15m ZigZag MTA on 1m
```

---

## Step 4：Pivot MTAとの比較

### 目的

既存のPivot MTAと、新しいZigZag MTAの違いを確認する。

### 比較項目

- MTA発生が早いか遅いか
- 余計なMTAが減るか
- 裁量で見ているMTAに近いか
- ゾーンの位置が自然か
- Triggerを載せる価値があるか

### 記録項目

```text
日時
通貨ペア
時間帯
Pivot MTAの表示
ZigZag MTAの表示
裁量感との一致度
メモ
```

---

## Step 5：Trigger統合

### 目的

ZigZag MTAに既存の1分足Triggerを載せる。

### 優先順

1. B5.2系
2. B1系
3. B2.1系

### 理由

B5.2はSetup前直近Pivotを使うため、既存検証との比較がしやすい。

### 成果物

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.0
v8.1α-ZZ-MTA-B1 Strategy v1.0
v8.1α-ZZ-MTA-B2.1 Strategy v1.0
```

---

## Step 6：Strategy検証

### 初期検証条件

```text
時間足：1分
上位足MTA：15分ZigZag MTA
RR：1.0
SL：MTA Zone反対側
期間：2021/01/01〜2025/12/31
Filter：All
```

### 比較表

| MTA Type | Trigger | ZigZag Setting | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---:|---:|---:|---:|---:|---|
| Pivot MTA | B5.2 | none |  |  |  |  |  | 既存基準 |
| ZigZag MTA | B5.2 | ATR x 1.0 |  |  |  |  |  |  |
| ZigZag MTA | B5.2 | ATR x 1.5 |  |  |  |  |  |  |
| ZigZag MTA | B5.2 | ATR x 2.0 |  |  |  |  |  |  |
| Pivot MTA | B1 | none |  |  |  |  |  | 既存基準 |
| ZigZag MTA | B1 | ATR x 1.0 |  |  |  |  |  |  |
| ZigZag MTA | B2.1 | ATR x 1.0 |  |  |  |  |  |  |

---

## 注意点

ZigZag版MTAは既存MTAの小改造ではなく、MTAエンジンの作り直し。

そのため、以下を守る。

- 既存B1/B2/B5.2にいきなり統合しない
- ZigZagの確定ロジックを先に検証する
- リプレイと通常表示の一致を必ず確認する
- 未確定スイングをStrategyに使わない
- request.securityは必ず `gaps_off` / `lookahead_off`
- Pine Script v6の既存コーディングルールを維持する

---

## 現時点の仮説

Pivot MTAは、Pivot本数設定に依存しやすい。

ZigZag MTAでは、価格の反転幅を基準にスイングを定義するため、裁量で見ている「波」に近いMTAを作れる可能性がある。

ただし、ZigZagはリペイントの危険があるため、確定済みスイングだけを使う設計が必須。

---

## 次の作業

まずは以下を作る。

```text
v8.1α-ZZ-MTA-0.1
Confirmed ZigZag Swing Engine
```

目的は、MTAではなくZigZag単体の挙動確認。

確認ポイント：

```text
1. ZigZag High / Lowが自然か
2. 確定タイミングが明確か
3. 通常表示とリプレイで一致するか
4. 過去の確定点が後から変わらないか
```
