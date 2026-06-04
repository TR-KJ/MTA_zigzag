# ZZ-MTA 開発まとめ

## 目的

PivotベースMTAではなく、Confirmed ZigZagを使ってMTAを機械的に定義する。

目的は以下。

```text
裁量に近い波をZigZagで定義
↓
MTA Zoneを作成
↓
HTF Setup + LTF TriggerでStrategy化
↓
BT / OOS / Forwardへ進める
```

---

## 開発の流れ

### 1. Confirmed ZigZag Engine 作成

外部ライブラリに依存せず、自前のConfirmed ZigZag Engineを作成。

```text
v8.1α-ZZ-MTA-0.1
Confirmed ZigZag Swing Engine
```

目的：

```text
リペイントを抑えたZigZag High/Lowを確定させる
```

---

### 2. ZigZag MTA Line 作成

ZigZagのHigh/Low構造からMTAラインを判定。

```text
v8.1α-ZZ-MTA-0.2.1
Confirmed ZigZag MTA Line
```

方針：

```text
MTA v2.1の転換認識に寄せる
ブレイク後は中立
直近ZigZag構造がBull/Bear条件を満たしたらMTA発生
```

---

### 3. MTA Zone化

MTAを単一ラインではなく、ゾーンとして表示。

```text
v8.1α-ZZ-MTA-0.3
Confirmed ZigZag MTA Zone
```

仕様：

```text
Bull Zone：MTA Low 〜 MTA確定までの実体安値
Bear Zone：MTA High 〜 MTA確定までの実体高値
```

---

### 4. HTF MTAをLTFへ投影

1時間足や15分足のZZ-MTA Zoneを、下位足チャートへ表示。

```text
v8.1α-ZZ-MTA-0.4
HTF ZZ-MTA Zone on LTF
```

検証例：

```text
Setup：1H ZZ-MTA Zone
Trigger：5m
```

---

### 5. B5.2 Trigger統合

ZZ-MTA Zone接触後、下位足B5.2 TriggerでEntry判定。

```text
v8.1α-ZZ-MTA-B5.2 Indicator / Strategy
```

B5.2 Trigger：

```text
Setup前直近Pivot H/Lを固定
Long：Pre Break Hを実体上抜け
Short：Pre Break Lを実体下抜け
```

追加仕様：

```text
Setup中は同方向再接触でPre H/Lを更新しない
Trigger / MTA失効 / 期限切れまで固定
```

---

## Strategy化

主な条件：

```text
Setup TF：1H
Trigger TF：5m
ZZ Setting：ATR x 2.0
SL Mode：Zone Opposite
Time Filter：Trade 09-24
```

SL：

```text
Long：ZZ-MTA Zone Bottom
Short：ZZ-MTA Zone Top
```

---

## 検証の流れ

### 1. 15m/1m 検証

15分ZZ-MTA + 1分TriggerではPFが伸びず。

```text
15m/1m：不採用
```

---

### 2. 1H/5m 検証

1時間ZZ-MTA + 5分Triggerで大きく改善。

```text
1H/5m：主戦場候補
```

---

### 3. SL / RR / Expire 検証

結果：

```text
SL Mode：Zone Opposite が最良
ATR：2.0 が最良
RR：2.0〜2.5付近が良好
Expire：180m〜240m付近が良好
```

---

### 4. Time Filter 検証

弱い時間帯を除外。

結果：

```text
弱い：00:00〜09:00
良い：09:00〜24:00
```

採用候補：

```text
Trade 09-24
JST 09:00〜24:00のみEntry
```

---

## 暫定3候補

| Type | Setup TF | Trigger TF | ATR | SL Mode | Time Filter | RR | Expire | Memo |
|---|---|---|---:|---|---|---:|---:|---|
| 攻め型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.5 | 240m | 第一候補 |
| 安定型 | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 240m | 勝率重視 |
| 回数寄り | 1H | 5m | 2.0 | Zone Opposite | Trade 09-24 | 2.0 | 300m | Trades重視 |

---

## OOS結果

期間：

```text
IS：2021/01/01〜2024/12/31
OOS：2025/01/01〜2025/12/31
```

結果：

```text
3候補すべてOOSでPF 1.0超え
Avg P/Lもプラス
```

判断：

```text
OOS-A 合格寄り
USDJPY専用候補としてForwardへ移行
```

---

## 現時点の扱い

```text
USDJPY専用候補
汎用FX手法ではない
他通貨ペアでは再現性低め
```

---

## 次の戦略開発への教訓

```text
1. まずMTA/Setupの定義を固定する
2. Indicatorで挙動確認してからStrategy化する
3. 最初から最適化しすぎない
4. 15m/1mが悪ければ時間軸を変える
5. SL → RR → Expire → Time Filterの順で確認する
6. 単発ベストではなくプラトーを見る
7. Tradesも資金効率として重視する
8. OOSで耐えたらForwardへ進む
9. 他通貨で崩れても、専用手法として成立する可能性はある
```

---

## Forward移行

次は実運用前のForward検証。

目的：

```text
利益額より、BTとの挙動一致確認
```

確認項目：

```text
Signal
Entry
SL / TP
Time Filter
Spread / Slippage
Trade Frequency
```
