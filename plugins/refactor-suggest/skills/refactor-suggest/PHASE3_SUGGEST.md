# Phase 3: リファクタリング提案

## 目的

検出した臭いに適切な手法を提案する。

## 臭い → 手法マッピング

### メソッド系

| 臭い | 第1候補 | 第2候補 |
|------|---------|---------|
| 長いメソッド | Extract Function | Split Phase |
| 長いパラメータ | Introduce Parameter Object | Preserve Whole Object |
| 重複コード | Extract Function | Extract Superclass |
| フラグ引数 | Remove Flag Argument | - |

### 条件分岐系

| 臭い | 第1候補 | 第2候補 |
|------|---------|---------|
| ネスト | Guard Clauses | Decompose Conditional |
| 複雑な条件 | Consolidate Conditional | Extract Variable |
| switch散在 | Replace with Polymorphism | Introduce Special Case |
| 型コード | Replace Type Code with Subclasses | - |

### データ系

| 臭い | 第1候補 |
|------|---------|
| プリミティブ執着 | Replace Primitive with Object |
| データの塊 | Introduce Parameter Object |
| マジック値 | Replace Magic Literal |

### クラス系

| 臭い | 第1候補 | 第2候補 |
|------|---------|---------|
| 巨大クラス | Extract Class | Extract Superclass |
| 特性の横恋慕 | Move Function | Move Field |
| メッセージ連鎖 | Hide Delegate | - |
| 中間者 | Remove Middle Man | Inline Class |

## 提案テンプレート

```markdown
### 提案N: [手法名]

**臭い**: [検出した臭い]
**場所**: L42-L58

**Before:**
```php
// 現在のコード
```

**After:**
```php
// 改善後のコード
```

**手順:**
1. [ステップ]
2. テスト実行
```

## カタログ参照

全66手法の詳細: [CATALOG.md](CATALOG.md)

## 出力

```markdown
## Phase 3 完了: 提案

**提案数**: N件

| # | 手法 | 対象臭い | 場所 |
|---|------|----------|------|
| 1 | Extract Function | 長いメソッド | L45 |
| 2 | Guard Clauses | ネスト | L30 |

### 提案1: Extract Function
...

**次**: Phase 4
```
