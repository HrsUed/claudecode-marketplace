# Phase 3: TDDサイクル

## 目的

Red→Green→Refactorのサイクルでテストと実装を進める。

## サイクル

```
[Red] → [Green] → [Refactor] → 繰返
```

## Red（失敗テスト）

**目標**: 失敗するテストを書く（まだ実装しない）

### AAAパターン

```php
public function test_シナリオ(): void
{
    // Arrange（準備）
    $input = [...];

    // Act（実行）
    $result = $this->sut->execute($input);

    // Assert（検証）
    $this->assertEquals($expected, $result);
}
```

### 段階的に厳密化

1. `assertNotNull` - 結果が返る
2. `assertInstanceOf` - 正しい型
3. `assertEquals` - 期待値一致

## Green（最小実装）

**目標**: テストを通す最小限の実装

```php
// 最初は定数を返すだけでもOK
public function execute($input) {
    return 'expected';
}
```

## Refactor（設計改善）

**目標**: テスト成功を維持しながら設計改善

> テストを書いただけでは質は上がらない。質を上げるのはリファクタリング。

### 進め方

```
変更 → テスト実行 → ✅確認 → コミット → 次の変更
```

**一度に一つだけ変える**

## 出力

```markdown
## Phase 3: TDDサイクル #[番号]

### Red
- テスト: `test_xxx()`
- 結果: ❌

### Green
- 実装: [内容]
- 結果: ✅

### Refactor
- 改善: [内容]
- 結果: ✅

### 次
- [ ] [次のテスト]

**次**: Phase 3 継続 / Phase 4
```
