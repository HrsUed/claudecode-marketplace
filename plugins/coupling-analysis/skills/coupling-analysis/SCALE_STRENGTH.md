# 統合強度スケール (Integration Strength)

コンポーネント間で共有される知識の量。**低いほど良い**。

## クイックリファレンス

| S | タイプ | キーワード |
|---|--------|-----------|
| 1 | コントラクト | DTO, Response, Request |
| 2-3 | モデル | 共有Entity, Value Object |
| 4-5 | シーケンシャル | パイプライン, 順序依存 |
| 6-7 | トランザクション | 同一TX, ユニットオブワーク |
| 8-9 | 対称機能 | 双方向, 相互参照 |
| 10 | 侵入 | リフレクション, private |

## 詳細スケール

### 1: コントラクト結合 ✅

```php
// 統合専用モデル経由のみ
class OrderController {
    public function show(int $id): OrderResponse {
        return new OrderResponse($this->service->find($id));
    }
}
```

### 2-3: モデル結合

```php
// 同じドメインモデルを共有
class OrderService {
    public function process(Order $order): void { }
}
class ShippingService {
    public function ship(Order $order): void { }  // 同じOrder
}
```

### 4-5: シーケンシャル機能結合

```php
// 処理順序に依存
class Workflow {
    public function execute(): void {
        $this->validator->validate();  // 必ず先
        $this->processor->process();   // 必ず後
    }
}
```

### 6-7: トランザクション機能結合

```php
// 同一トランザクション内で協調
DB::transaction(function () {
    $this->orderRepo->save($order);
    $this->inventoryService->reserve($order->items);  // 同一TX
});
```

### 8-9: 対称機能結合 ⚠️

```php
// 双方向に依存
class Order {
    public function getCustomer(): Customer { }
}
class Customer {
    public function getOrders(): Collection { }  // 相互参照
}
```

### 10: 侵入結合 🔴

```php
// 内部実装に依存（リフレクション等）
$reflection = new ReflectionClass($service);
$prop = $reflection->getProperty('privateData');
$prop->setAccessible(true);
```

## 判定フロー

```
内部実装に依存? ─Yes→ 9-10
       │No
双方向依存? ─Yes→ 8-9
       │No
同一TX協調? ─Yes→ 6-7
       │No
順序依存? ─Yes→ 4-5
       │No
モデル共有? ─Yes→ 2-3
       │No
DTO/IF のみ → 1
```
