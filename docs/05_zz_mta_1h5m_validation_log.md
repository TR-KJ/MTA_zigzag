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
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.0 |  |  |  |  |  |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 1.5 |  |  |  |  |  |  |
| 1H | 5m | ATR x 2.0 | Zone Opposite | 2.0 |  |  |  |  |  |  |

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
