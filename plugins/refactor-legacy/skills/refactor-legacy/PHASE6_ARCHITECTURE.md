# Phase 6: アーキテクチャ定義

## 目的

レイヤー構造を定義し、依存方向を整理する。

## 基本原則

> クリーンアーキテクチャは最初から目指すのではなく、原理原則ベースでリファクタリングしていくと次第に近づいていく。

| 原則 | 要点 |
|------|------|
| SRP | 変更理由は一つだけ |
| DIP | 抽象に依存、具象に依存しない |
| CCP | 同時に変わるものは同じ場所に |
| SDP | 安定度の高い方向に依存 |

## レイヤー構造

```
┌─────────────────────────┐
│ Presentation (不安定)   │ Controllers, CLI
├─────────────────────────┤
│ Application            │ Use Cases
├─────────────────────────┤
│ Domain (安定)          │ Entities, Value Objects
├─────────────────────────┤
│ Infrastructure         │ Repositories, External
└─────────────────────────┘

依存: Presentation → Application → Domain ← Infrastructure
```

## ディレクトリ例

```
app/
├── Domain/Order/
│   ├── Order.php
│   ├── OrderId.php
│   └── OrderRepositoryInterface.php
├── Application/Order/
│   └── PlaceOrderUseCase.php
├── Infrastructure/Persistence/
│   └── EloquentOrderRepository.php
└── Presentation/Http/Controllers/
```

## チェックリスト

- [ ] Domain層が外部依存なし（純粋PHP）
- [ ] RepositoryインターフェースがDomain層にある
- [ ] Controllerが薄い（調整役のみ）
- [ ] 均衡度が目標値以上（`/coupling-analysis` でB≥7を確認）

## 結合バランス検証

アーキテクチャ定義後、`/coupling-analysis` で設計の健全性を定量評価:

| 評価対象 | 期待値 |
|----------|--------|
| Domain ← Infrastructure | B ≥ 7（Interface経由でS低、D適切） |
| Application → Domain | B ≥ 7（モデル共有でS中、D近） |
| Presentation → Application | B ≥ 7（薄いController、S低） |

**問題パターン検出**:
- Domain直接参照（S高 & D低 → B低）→ Interface導入を検討
- 層間の循環依存 → 依存方向を再整理

## 出力

```markdown
## Phase 6 完了: アーキテクチャ定義

### レイヤー
| 層 | 責務 | 主要クラス |
|----|------|-----------|
| Domain | [責務] | [クラス] |
| Application | [責務] | [クラス] |
| Infrastructure | [責務] | [クラス] |
| Presentation | [責務] | [クラス] |

### 依存方向
[図]

### 今後の改善
1. [項目]

---

## 完了
```
