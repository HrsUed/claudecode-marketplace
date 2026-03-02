# Phase 4: モデル分離

## 目的

Humble Object Patternでテストしにくい部分とビジネスロジックを分離。

## パターン

```
Before:
┌─────────────────────────────┐
│ [外部依存] [ロジック] [インフラ] │
└─────────────────────────────┘

After:
┌─────────────┐    ┌───────────────┐
│ Humble(薄い) │───►│ Domain(純粋)  │
│ ・調整役のみ │    │ ・副作用なし  │
└─────────────┘    └───────────────┘
```

## 実装例

```php
// ❌ Before: Controllerにロジック混在
class OrderController {
    public function store(Request $request) {
        $total = 0;
        foreach ($request->items as $item) {
            $total += $item['price'] * $item['quantity'];
        }
        // ...
    }
}

// ✅ After: ロジックをドメインに移動
class OrderCalculator {
    public function calculateTotal(array $items): Money { /* 純粋 */ }
}

class OrderController {  // Humble
    public function store(Request $request) {
        $total = $this->calculator->calculateTotal($items);
    }
}
```

## 依存性の注入

| 要素 | 注入方法 |
|------|----------|
| 乱数 | `?callable $randomizer` |
| 時刻 | `?ClockInterface $clock` |
| 外部API | `ApiClientInterface $client` |

## 出力

```markdown
## Phase 4 完了: モデル分離

### 分離後
| 役割 | クラス |
|------|--------|
| Humble | [調整役] |
| Domain | [純粋ロジック] |

### 注入した依存
| 要素 | インターフェース |
|------|-----------------|
| 乱数 | Callable |
| 時刻 | ClockInterface |

**次**: Phase 5
```
