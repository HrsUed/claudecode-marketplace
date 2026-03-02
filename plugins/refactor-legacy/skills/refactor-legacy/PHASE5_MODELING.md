# Phase 5: 事実と情報の分離

## 目的

保存すべき「事実」と計算で導出する「情報」を明確に分離する。

## 概念の違い

| 概念 | 説明 | 例 |
|------|------|-----|
| **事実** | 発生した出来事。不変。 | 注文された、支払われた |
| **情報** | 事実から導出。計算可能。 | 合計金額、ステータス |

## パターン

```php
// ❌ アンチパターン: 情報を直接保存
class Order {
    public int $totalAmount;  // 情報
    public string $status;    // 情報
}
// 問題: 計算ロジック分散、整合性維持困難

// ✅ 推奨: 事実を保存し、情報は計算
class Order {
    /** @var OrderItem[] 事実 */
    private array $items = [];

    /** @var Payment[] 事実 */
    private array $payments = [];

    // 情報（計算）
    public function getTotalAmount(): Money {
        return array_reduce($this->items,
            fn($sum, $item) => $sum->add($item->getSubtotal()),
            Money::zero()
        );
    }

    // 情報（計算）
    public function getStatus(): OrderStatus {
        return $this->getPaidAmount()->lessThan($this->getTotalAmount())
            ? OrderStatus::PENDING_PAYMENT
            : OrderStatus::PAID;
    }
}
```

## 識別のコツ

| 質問 | 事実 | 情報 |
|------|------|------|
| いつ発生したか？ | ✅ | - |
| 記録すべきか？ | ✅ | - |
| 他から計算できるか？ | - | ✅ |
| 現在状態を表すか？ | - | ✅ |

## 出力

```markdown
## Phase 5 完了: 事実/情報分離

### 事実（保存）
1. [事実1]
2. [事実2]

### 情報（計算）
| 情報 | 計算方法 |
|------|----------|
| [情報1] | [計算方法] |
| [情報2] | [計算方法] |

**次**: Phase 6
```
