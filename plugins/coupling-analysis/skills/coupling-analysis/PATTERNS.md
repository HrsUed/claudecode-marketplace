# 改善パターン集

## パターン早見表

| 問題 | 条件 | 解決策 |
|------|------|--------|
| 分散モノリス | S高 & D遠 | 統合 or 弱結合化 |
| 過度な抽象化 | S低 & D近 | インライン化 |
| 不安定依存 | V高 & S高 | ACL, イベント駆動 |
| 距離不足 | S高 & D近 | モジュール分離 |
| 距離過剰 | S低 & D遠 | コロケーション |

---

## 統合強度を下げる

### 1. インターフェース導入

**効果**: S -2〜-4

```php
// Before: 具象依存 (S=6)
class OrderService {
    public function __construct(
        private InventoryService $inventory
    ) {}
}

// After: インターフェース経由 (S=3)
class OrderService {
    public function __construct(
        private InventoryServiceInterface $inventory
    ) {}
}
```

### 2. DTO導入

**効果**: S -2〜-3

```php
// Before: Entityを直接共有 (S=3)
class ReportService {
    public function generate(Order $order): Report {}
}

// After: DTOで分離 (S=1)
class ReportService {
    public function generate(OrderSummaryDto $dto): Report {}
}
```

### 3. イベント駆動化

**効果**: S -3〜-5

```php
// Before: 直接呼び出し (S=7)
class OrderService {
    public function complete(Order $order): void {
        $this->inventory->release($order);
        $this->notification->send($order);
    }
}

// After: イベント発行 (S=2)
class OrderService {
    public function complete(Order $order): void {
        $this->events->dispatch(new OrderCompleted($order->id));
    }
}
```

### 4. 依存性逆転（DIP）

**効果**: S -2〜-3

```php
// Before: 上位が下位に依存 (S=5)
// Domain → Infrastructure

// After: 両方が抽象に依存 (S=2)
// Domain → Interface ← Infrastructure
```

---

## 距離を調整する

### 5. コロケーション（近づける）

**効果**: D -2〜-4

```
// Before: バラバラ (D=6)
App\Services\OrderService
App\Repositories\OrderRepository
App\Events\OrderCompleted

// After: 同一ディレクトリ (D=2)
App\Domains\Order\
├── OrderService
├── OrderRepository
└── Events\OrderCompleted
```

### 6. モジュール分離（遠ざける）

**効果**: D +2〜+4

```
// Before: 同一名前空間 (D=2)
App\Services\
├── OrderService
├── BillingService  ← 異なる関心

// After: 明確な境界 (D=5)
App\Domains\Order\OrderService
App\Domains\Billing\BillingService
```

### 7. 境界コンテキスト定義

**効果**: D +2〜+3, S -1〜-2

```
// Before: 暗黙の境界
App\Services\*

// After: 明示的なBC
App\Domains\
├── Order\       ← BC1
├── Shipping\    ← BC2
└── Billing\     ← BC3
```

---

## 変動性に対応する

### 8. 腐敗防止層（ACL）

**効果**: 変更波及を遮断

```php
// Before: コアに直接依存
class ReportService {
    public function generate(Order $order): Report {}
}

// After: ACLで保護
class ReportService {
    public function generate(OrderSnapshot $snapshot): Report {}
}

class OrderTranslator {  // ACL
    public function toSnapshot(Order $order): OrderSnapshot {}
}
```

### 9. 公開ホストサービス

**効果**: 安定したAPIを提供

```php
// コアドメインの変動を隠蔽
interface OrderQueryService {  // 公開API（安定）
    public function findById(int $id): OrderView;
}

class OrderQueryServiceImpl implements OrderQueryService {
    // 内部実装は自由に変更可能
}
```

### 10. 共有カーネル最小化

**効果**: 影響範囲を限定

```php
// Before: 大きな共有モデル
class Order {  // 全20プロパティを共有
}

// After: 最小限の共有
class OrderId {  // IDのみ共有
    public function __construct(public readonly int $value) {}
}
```

---

## アーキテクチャレベル

| 現状 | 改善案 | 条件 |
|------|--------|------|
| 密結合モノリス | モジュラーモノリス | 大規模単一コードベース |
| 分散モノリス | 境界再定義 | マイクロサービス失敗 |
| 泥だんご | レイヤード/ヘキサゴナル | 構造なし |

---

## 選択ガイド

```
均衡度が低い原因は?
       │
   ┌───┴───┐
   │       │
S > D    D > S
   │       │
   ↓       ↓
統合強度  距離を
を下げる  近づける
   │       │
   ├─ インターフェース導入
   ├─ DTO導入
   ├─ イベント駆動化
   └─ 依存性逆転

+ 変動性が高い場合
   └─ ACL / 公開ホストサービス
```
