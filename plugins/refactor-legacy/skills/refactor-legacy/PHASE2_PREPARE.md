# Phase 2: 準備

## 目的

接合部を特定し、テスト戦術を選択する。

## 接合部の特定

テストを書く境界を見つける。

| 優先度 | 種類 | 例 |
|--------|------|-----|
| 1 | リクエスト/レスポンス境界 | Controller入出力 |
| 2 | パブリックAPI境界 | サービスのpublicメソッド |
| 3 | データ変換境界 | DTO変換処理 |

### 良い接合部の条件

- ✅ 入出力が明確
- ✅ 副作用が制御可能
- ✅ 実装詳細に依存しない

## 戦術選択

```
既存コードにテストを書ける？
├── Yes → Extract（推奨）
└── No  → Sprout
```

| 戦術 | 内容 |
|------|------|
| Extract | テストで保護 → 新コード抽出 |
| Sprout | 新規コードのみテスト |

## 非決定的要素の対処

```php
// 乱数
public function __construct(?callable $randomizer = null) {
    $this->randomizer = $randomizer ?? fn($max) => rand(0, $max);
}

// 時刻
public function __construct(?ClockInterface $clock = null) {
    $this->clock = $clock ?? new SystemClock();
}
```

## 出力

```markdown
## Phase 2 完了: 準備

### 接合部
- [場所]: [種類]

### 戦術: Extract / Sprout

### 非決定的要素
| 要素 | 対処 |
|------|------|
| 乱数 | Callable注入 |

### 最初のテスト対象
- [メソッド名]

**次**: Phase 3
```
