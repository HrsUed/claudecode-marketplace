---
name: coupling-analysis
description: コンポーネント/モジュールの結合バランスを「均衡結合モデル」で評価し、設計改善を提案します。統合強度・距離・変動性の3次元で分析し、モジュール性と均衡度を算出します。結合の評価、設計レビュー、アーキテクチャ分析を求められた場合に使用してください。
---

# 結合バランス評価

均衡結合モデル（Balancing Coupling）でコンポーネントの結合を評価し、設計改善を提案。

## フロー

```
[Phase 1] → [Phase 2] → [Phase 3] → [Phase 4]
   特定        評価        計算        改善
```

## フェーズ

| # | 目的 | 詳細 |
|---|------|------|
| 1 | 依存関係の特定 | [PHASE1_IDENTIFY.md](PHASE1_IDENTIFY.md) |
| 2 | 3次元評価 | [PHASE2_EVALUATE.md](PHASE2_EVALUATE.md) |
| 3 | 均衡度計算 | [PHASE3_CALCULATE.md](PHASE3_CALCULATE.md) |
| 4 | 改善提案 | [PHASE4_IMPROVE.md](PHASE4_IMPROVE.md) |

## 対象指定

| 形式 | 例 |
|------|-----|
| ファイル | `app/Services/OrderService.php` |
| クラス | `OrderService` |
| ディレクトリ | `app/Domains/Order/` |
| 直近変更 | `recent` |

## 3次元

| 次元 | 説明 | 低(1) | 高(10) |
|------|------|-------|--------|
| 統合強度(S) | 共有知識量 | 契約のみ | 内部依存 |
| 距離(D) | 分離度 | 同一クラス | 外部API |
| 変動性(V) | 変更頻度 | レガシー | コア |

## 評価式

| 指標 | 計算 | 良い |
|------|------|------|
| モジュール性 | \|S - D\| + 1 | ≥ 7 |
| 均衡度 | max(\|S-D\|, 10-V) + 1 | ≥ 7 |

## 判定

| 均衡度 | 評価 | 信頼度閾値 |
|--------|------|-----------|
| 7-10 | ✅ 優秀 | - |
| 4-6 | ⚠️ 要注意 | 報告: 80%+ |
| 1-3 | 🔴 問題 | 報告: 70%+ |

## 参照

| ファイル | 内容 |
|----------|------|
| [SCALE_STRENGTH.md](SCALE_STRENGTH.md) | 統合強度スケール |
| [SCALE_DISTANCE.md](SCALE_DISTANCE.md) | 距離スケール |
| [SCALE_VOLATILITY.md](SCALE_VOLATILITY.md) | 変動性スケール |
| [PATTERNS.md](PATTERNS.md) | 改善パターン集 |

## 参考

- https://coupling.dev/
- https://speakerdeck.com/vladikk/balancing-coupling-in-software-design
- https://connascence.io/
