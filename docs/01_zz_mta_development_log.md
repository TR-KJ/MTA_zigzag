# ZigZag MTA Development Log

## 概要

このドキュメントは、Pivotベースで定義していたMTAを、ZigZagベースで再定義する検証ログです。

目的は、既存のPivot MTAが `leftBars / rightBars` に依存しやすい問題を避け、裁量で見ている「波」に近いMTAを、機械的かつ再現性のある形で定義できるか確認することです。

---

## 基本方針

ZigZag MTAは、既存MTAの小改造ではなく、MTAエンジンの作り直しとして扱う。

そのため、以下の順番で進める。

```text
1. Confirmed ZigZag Swing Engine
2. Confirmed ZigZag MTA Line
3. Confirmed ZigZag MTA Zone
4. 15分ZZ-MTA Zoneを1分足へ表示
5. 1分Trigger統合
6. Strategy化
```

重要方針：

```text
- 未確定ZigZagはStrategyに使わない
- Confirmed ZigZagのみ使う
- 通常表示とリプレイで挙動が一致することを重視する
- ZigZag Engineは0.1を基準として固定する
- MTA Lineロジックは0.2.1を基準として固定する
- Zone仕様は0.3を基準として固定する
- request.security は gaps_off / lookahead_off を必ず使う
```

---

## 参考コード：MTA v2.1

他者作成の `MTA v2.1` を参考コードとして確認した。

### 参考にする部分

```text
ZigZag High / Low の並びからMTAを判定する考え方
```

具体的には、

```text
Bull MTA：
Low → High → Higher Low
かつ
終値が間のHighを上抜け

Bear MTA：
High → Low → Lower High
かつ
終値が間のLowを下抜け
```

### そのまま使わない部分

```text
- 外部ZigZagライブラリ依存
- MTAを単一価格ラインとして扱う点
- リプレイ一致確認前のStrategy化
```

そのため、自前のConfirmed ZigZag Engineを作成する方針とした。

---

## v8.1α-ZZ-MTA-0.1

### 名称

```text
v8.1α-ZZ-MTA-0.1
Confirmed ZigZag Swing Engine
```

### 目的

MTAなし、Triggerなしで、Confirmed ZigZag High / Lowのみを検出する。

### 内容

```text
- ATR / Ticks / Percent による反転幅指定
- Confirmed ZigZag High / Low 表示
- ZigZagライン表示
- Candidate High / Low 表示
- Confirm発生足ラベル表示
```

### 確認結果

```text
挙動OK
ATR倍率は2.0が裁量に近い
```

### 補足

裁量ではZigZag High / Lowとして見たいが、ATR 2.0では拾わない場所もあった。

ただし、最初から裁量に寄せすぎると沼るため、まずはZigZag Engineに従う方針とした。

---

## v8.1α-ZZ-MTA-0.2

### 名称

```text
v8.1α-ZZ-MTA-0.2
Confirmed ZigZag MTA Line
```

### 目的

Confirmed ZigZag High / Low の並びから、MTAラインを表示する。

### 問題点

0.2では、ZigZag Engine部分に変更が入り、0.1とZigZag位置がズレた。

原因：

```text
0.2では swingDirAtStart を使ったことで、0.1と同じ足内の状態遷移が変化した
```

結論：

```text
0.2は比較不能
ZigZag Engineは0.1と完全一致させる必要あり
```

---

## v8.1α-ZZ-MTA-0.2.1

### 名称

```text
v8.1α-ZZ-MTA-0.2.1
Confirmed ZigZag MTA Line / 0.1 Engine Fixed
```

### 目的

ZigZag Engineを0.1に固定し、MTA Line判定だけを追加する。

### MTA発生条件

Bull：

```text
Low → High → Higher Low
かつ
終値が間のHighを上抜け
```

Bear：

```text
High → Low → Lower High
かつ
終値が間のLowを下抜け
```

### MTA終了条件

MTA v2.1準拠。

Bull終了：

```text
close < mtaPrice
```

Bear終了：

```text
close > mtaPrice
```

終了後は反対方向へ即転換せず、中立に戻る。

```text
MTA Break
↓
mtaTrend = 0
↓
中立
↓
直近ZigZag構造がBull / Bear条件を満たしたら次のMTA発生
```

### 確認結果

```text
0.1と0.2.1でZigZag位置が一致
MTA v2.1準拠で進める方針に決定
```

---

## v8.1α-ZZ-MTA-0.3

### 名称

```text
v8.1α-ZZ-MTA-0.3
Confirmed ZigZag MTA Zone
```

### 目的

0.2.1のMTA Lineを維持したまま、MTA Zone表示を追加する。

### 固定方針

```text
ZigZag Engine：0.1固定
MTA Lineロジック：0.2.1固定
追加：Zone表示のみ
```

### Zone仕様

B1.1のゾーン思想に準拠。

Bull ZZ-MTA Zone：

```text
Zone Bottom = ZZ Low価格
Zone Top = ZZ Low以降の実体下限の最安値
```

Bear ZZ-MTA Zone：

```text
Zone Top = ZZ High価格
Zone Bottom = ZZ High以降の実体上限の最高値
```

### 終了判定

0.3では、0.2.1と同じくMTAライン基準。

ただし、現在のゾーン仕様では、

```text
Bull：mtaPrice = Zone Bottom
Bear：mtaPrice = Zone Top
```

となるため、実質的にゾーン反対側基準と同義。

### 確認結果

```text
0.3挙動OK
ZigZag位置、MTA Line位置、Zone表示とも問題なし
```

---

## v8.1α-ZZ-MTA-0.4

### 名称

```text
v8.1α-ZZ-MTA-0.4
15m ZigZag MTA Zone on 1m
```

### 目的

15分足で計算したZZ-MTA Zoneを、1分足チャート上に表示する。

### 内容

```text
15分足の中でZZ-MTAを計算
↓
request.security() で1分足へ表示
```

### 固定方針

```text
ZigZag Engine：0.1固定
MTA Lineロジック：0.2.1固定
Zone仕様：0.3固定
```

### 実装方針

```pine
request.security(syminfo.tickerid, htfTf, calcZzMta(), gaps = barmerge.gaps_off, lookahead = barmerge.lookahead_off)
```

### 保存状況

```text
GitHub保存済み
TradingView保存済み
```

### 次回確認すること

```text
1. 15分足に0.3を入れた表示と、1分足に0.4を入れた表示が一致するか
2. Bull / Bearのゾーン上限・下限がズレていないか
3. MTA終了タイミングが0.3と一致するか
4. 1分足リプレイで表示が安定するか
5. 0.4の表示をSetupとして使えそうか
```

---

## 現在の判断

ZZ-MTAは、MTA v2.1準拠で進める。

つまり、MTAブレイク時に即反対方向へ転換するのではなく、

```text
MTAブレイク
↓
一旦中立
↓
直近Confirmed ZigZag構造が条件を満たしたら新しいMTA発生
```

という認識を採用する。

これはPivot MTA B1.1の内部trendState反転とは異なる。

---

## 次にやること

休憩後、まず0.4の挙動確認を行う。

### Step 1：0.4挙動確認

確認内容：

```text
- 15分足0.3と1分足0.4の一致
- Zone表示のズレ有無
- MTA発生 / 終了タイミング
- 通常表示とリプレイの一致
```

---

## Step 2：B5.2 Trigger統合版インジケーター

0.4がOKなら、次はB5.2 Trigger統合版のインジケーターを作る。

### 予定名称

```text
v8.1α-ZZ-MTA-B5.2 Indicator v0.1
15m ZZ-MTA Setup + 1m Pre Pivot Break Trigger
```

### 目的

15分ZZ-MTA ZoneをSetupとして使い、1分足B5.2 Triggerを載せる。

### B5.2 Trigger仕様

既存B5.2と同じ。

Long：

```text
Bull ZZ-MTA Zone接触
↓
Setup前に存在していた直近1分Pivot Highを記録
↓
そのPivot Highを実体上抜け
↓
Long Trigger
```

Short：

```text
Bear ZZ-MTA Zone接触
↓
Setup前に存在していた直近1分Pivot Lowを記録
↓
そのPivot Lowを実体下抜け
↓
Short Trigger
```

### 重要方針

```text
- まずはインジケーターで挙動確認
- いきなりStrategy化しない
- 既存B5.2 Triggerロジックはなるべく変えない
- Setup部分だけPivot MTAからZZ-MTAへ置き換える
```

---

## Step 3：B5.2 Trigger統合版 Strategy

B5.2 Trigger統合インジケーターの挙動がOKなら、Strategy化する。

### 予定名称

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.0
```

### 初期Strategy条件

```text
時間足：1分
Setup：15分 ZZ-MTA Zone接触
Trigger：B5.2 Pre Pivot Break
RR：1.0
SL：ZZ-MTA Zone反対側
TP：Trigger確定足終値基準のRR
初期資金：100万円
1回2%
手数料：0.04%
スリッページ：2
pyramiding：0
process_orders_on_close：false
calc_on_every_tick：false
```

---

## Step 4：比較検証

Pivot MTA版とZigZag MTA版を比較する。

### 比較対象

```text
Pivot MTA + B5.2
ZZ-MTA + B5.2
```

必要に応じて、その後、

```text
ZZ-MTA + B1
ZZ-MTA + B2.1
```

へ横展開する。

### 比較表テンプレート

| MTA Type | Trigger | ZZ Setting | Trades | Win Rate | PF | Max DD | Avg P/L | Memo |
|---|---|---|---:|---:|---:|---:|---:|---|
| Pivot MTA | B5.2 | none |  |  |  |  |  | 既存基準 |
| ZZ-MTA | B5.2 | ATR x 2.0 | 542 | 42.99% | 0.33 | 1.01% | -18.64 |  |
| ZZ-MTA | B1 | ATR x 2.0 |  |  |  |  |  | 必要なら横展開 |
| ZZ-MTA | B2.1 | ATR x 2.0 |  |  |  |  |  | 必要なら横展開 |

---

## 注意点

今後も以下は守る。

```text
- ZigZag Engineは0.1固定
- MTA Lineロジックは0.2.1固定
- Zone仕様は0.3固定
- 0.4のHTF表示挙動確認後にTrigger統合へ進む
- request.security は gaps_off / lookahead_off
- 通常表示とリプレイの一致確認を必ず行う
- Strategy化はインジケーター挙動確認後
```

---

## docs/保存案

このドキュメントは以下に保存する。

```text
docs/01_zz_mta_development_log.md
```

今後、追加で作るなら以下。

```text
docs/02_zz_mta_0_4_behavior_check.md
docs/03_zz_mta_b5_2_trigger_spec.md
docs/04_zz_mta_strategy_results.md
docs/05_pivot_mta_vs_zz_mta_comparison.md
```
