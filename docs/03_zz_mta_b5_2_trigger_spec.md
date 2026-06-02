# ZZ-MTA B5.2 Trigger Integration

## 対象バージョン

```text
v8.1α-ZZ-MTA-B5.2 Indicator v0.2
Setup Pre H/L Fixed Guard
```

## 目的

15分ZZ-MTA ZoneをSetupとして使い、1分足B5.2 Triggerを統合する。

B5.2の基本思想は、Setup発生時点で存在している直近1分PivotをPre Break基準として固定し、その後の実体BreakをTriggerにすること。

---

## Setup条件

### Long Setup

```text
15分ZZ-MTA Trend = Bull
かつ
1分足価格がBull ZZ-MTA Zoneに接触
```

### Short Setup

```text
15分ZZ-MTA Trend = Bear
かつ
1分足価格がBear ZZ-MTA Zoneに接触
```

---

## Trigger条件

### Long Trigger

```text
Long Setup発生
↓
Setup時点で存在していた直近1分Pivot HighをPre Break Hとして固定
↓
Pre Break Hを1分足実体上限が上抜け
↓
Long Trigger
```

### Short Trigger

```text
Short Setup発生
↓
Setup時点で存在していた直近1分Pivot LowをPre Break Lとして固定
↓
Pre Break Lを1分足実体下限が下抜け
↓
Short Trigger
```

---

## v0.2の変更点

v0.1では、Setup中に再度Zone接触が発生した場合、Setupが再作成され、Pre Break H/Lが更新される可能性があった。

v0.2では、以下のガードを追加した。

```pine
canStartNewSetup = not longSetupActive and not shortSetupActive
```

Setup開始条件を以下に変更。

```pine
if bullZoneTouchOnce and canStartNewSetup
```

```pine
if bearZoneTouchOnce and canStartNewSetup
```

---

## v0.2仕様

Setup中は、同方向・逆方向を問わず、新しいSetupを開始しない。

そのため、以下のいずれかが発生するまで、Pre Break H/Lは固定される。

```text
Trigger発動
MTA失効
Trigger有効期限切れ
```

---

## 期待する挙動

```text
1. ZZ-MTA Zone接触でSetup開始
2. Setup時点の直近1分Pivot H/LをPre Breakとして固定
3. Setup中に再接触してもPre Breakは更新しない
4. 固定Pre Breakを実体BreakしたらTrigger
5. ZZ-MTA終了でSetup失効
6. 有効期限切れでSetup失効
```

---

## 次に確認すること

```text
1. v0.1と同じ場所でSetupが出るか
2. Setup中の再接触でPre Break H/Lが更新されないか
3. Trigger発動後、新しいSetupが可能になるか
4. MTA失効後、新しいSetupが可能になるか
5. 期限切れ後、新しいSetupが可能になるか
6. 通常表示とリプレイで挙動が一致するか
```

---

## 次の予定

v0.2の挙動確認がOKなら、Strategy化へ進む。

予定名：

```text
v8.1α-ZZ-MTA-B5.2 Strategy v1.0
```

初期Strategy条件：

```text
時間足：1分
Setup：15分ZZ-MTA Zone接触
Trigger：B5.2 Fixed Pre Pivot Break
SL：ZZ-MTA Zone反対側
TP：RR 1.0
初期資金：100万円
1回2%
手数料：0.04%
スリッページ：2
pyramiding：0
process_orders_on_close：false
calc_on_every_tick：false
```

##仕様はこれ

Setup：15分ZZ-MTA Zone接触

Trigger：B5.2 Fixed Pre Pivot Break

Long SL：ZZ-MTA Zone Bottom

Short SL：ZZ-MTA Zone Top

TP：Trigger確定足終値基準 RR

Entry：Trigger確定足の次足始値想定
