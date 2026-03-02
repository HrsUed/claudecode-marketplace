---
name: refactor-suggest
description: コードを分析し、Martin Fowlerのリファクタリングカタログ（66手法）に基づいた改善提案を行います。コードの「臭い」を検出し、適用可能なリファクタリング手法をBefore/After例付きで提案します。コードの改善、リファクタリング提案、コード品質チェックを求められた場合に使用してください。
---

# リファクタリング提案スキル

## フロー

```
[Phase 1] → [Phase 2] → [Phase 3] → [Phase 4]
   分析        検出        提案        優先度
```

## フェーズ

| # | 目的 | 詳細 |
|---|------|------|
| 1 | コード分析 | [PHASE1_ANALYZE.md](PHASE1_ANALYZE.md) |
| 2 | 臭い検出 | [PHASE2_DETECT.md](PHASE2_DETECT.md) |
| 3 | 手法提案 | [PHASE3_SUGGEST.md](PHASE3_SUGGEST.md) |
| 4 | 優先度評価 | [PHASE4_PRIORITIZE.md](PHASE4_PRIORITIZE.md) |

## 対象指定

| 形式 | 例 |
|------|-----|
| ファイル | `app/Services/OrderService.php` |
| メソッド | `OrderService::calculate` |
| ディレクトリ | `app/Services/` |
| 直近変更 | `recent` |

## 検出する臭い

| カテゴリ | 臭い |
|----------|------|
| メソッド | 長い / パラメータ多 / 重複 / フラグ引数 |
| 条件分岐 | ネスト深 / 複雑 / switch散在 / 型コード |
| データ | プリミティブ執着 / 塊 / マジック値 |
| クラス | 巨大 / 横恋慕 / 連鎖 / 中間者 |

## カタログ

全66手法: [CATALOG.md](CATALOG.md)

## 原則

| 原則 | 説明 |
|------|------|
| 小さく | 1つずつ適用 |
| 安全に | テストで保護 |
| 不変に | 外部動作を変えない |

## 関連スキル

| スキル | 用途 |
|--------|------|
| [coupling-analysis](../coupling-analysis/SKILL.md) | リファクタリング後の結合バランス評価。優先度判定（Phase 4）で活用 |

> **💡 推奨**: リファクタリング提案後、`/coupling-analysis` で改善前後の均衡度を比較し、設計改善の効果を定量化できます。
