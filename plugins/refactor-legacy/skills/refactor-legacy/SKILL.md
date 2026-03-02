---
name: refactor-legacy
description: レガシーコードを段階的に改善します。現状確認、テスト戦略決定、TDDサイクル、モデル分離、アーキテクチャ定義の6フェーズで安全にリファクタリングを進めます。レガシーコードの改善、リファクタリング、テスト追加を求められた場合に使用してください。
---

# レガシーコード改善スキル

## フロー

```
[1] → [2] → [3] ⟲ → [4] → [5] → [6]
評価   準備   TDD    分離   モデル  アーキ
```

## フェーズ

| # | 目的 | 詳細 |
|---|------|------|
| 1 | 3本柱の状態把握 | [PHASE1_ASSESS.md](PHASE1_ASSESS.md) |
| 2 | テスト戦略決定 | [PHASE2_PREPARE.md](PHASE2_PREPARE.md) |
| 3 | Red→Green→Refactor | [PHASE3_TDD.md](PHASE3_TDD.md) |
| 4 | Humble Object適用 | [PHASE4_SEPARATE.md](PHASE4_SEPARATE.md) |
| 5 | 事実/情報の分離 | [PHASE5_MODELING.md](PHASE5_MODELING.md) |
| 6 | レイヤー構造定義 | [PHASE6_ARCHITECTURE.md](PHASE6_ARCHITECTURE.md) |

## 3本柱（Phase 1）

| 柱 | 優先度 |
|----|--------|
| Version Control | 1位 |
| Automation | 2位 |
| Testing | 3位 |

## 戦術（Phase 2）

| 戦術 | 条件 |
|------|------|
| Extract | 既存コードにテスト可能（推奨） |
| Sprout | テスト追加が困難 |

## TDD（Phase 3）

```
[Red] → [Green] → [Refactor] → 繰返
```

> テストを書いただけでは質は上がらない。質を上げるのはリファクタリング。

## 原則

| 出典 | 原則 |
|------|------|
| Feathers | テストのないコードは悪いコード |
| 和田 | 仕様が固まらないからこそテストを書く |
| Beck | テストと手入れされたコードはひらめきを具現化する備え |

## 関連スキル

| スキル | 用途 |
|--------|------|
| [coupling-analysis](../coupling-analysis/SKILL.md) | Phase 4（分離）、Phase 6（アーキテクチャ）で結合バランスを評価 |
| [refactor-suggest](../refactor-suggest/SKILL.md) | Phase 3（TDD）のリファクタリングステップで手法選定 |

> **💡 推奨**: Phase 6完了後、`/coupling-analysis` で新アーキテクチャの均衡度を検証し、目標値（B≥7）を達成しているか確認できます。
