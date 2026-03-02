# 距離スケール (Distance)

コンポーネント間の物理的・組織的分離度。

## クイックリファレンス

| D | 位置 | 例 |
|---|------|-----|
| 1 | 同一クラス | private method |
| 2 | 同一ディレクトリ | Services/A, Services/B |
| 3-4 | 同一名前空間 | App\Services\* |
| 5-6 | 異なるBC | Order ↔ Shipping |
| 7-8 | 異なるパッケージ | packages/*, vendor/* |
| 9 | 分散サービス | HTTP/gRPC |
| 10 | 外部ベンダー | Stripe, AWS |

## 詳細スケール

### 1: 同一オブジェクト内

```
app/Services/OrderService.php
├── public process()
└── private validate()  ← D=1
```

### 2: 同一ディレクトリ

```
app/Services/
├── OrderService.php
└── InventoryService.php  ← D=2
```

### 3-4: 同一名前空間

```
App\Domains\Order\
├── Services\OrderService.php
└── Repositories\OrderRepository.php  ← D=3-4
```

### 5-6: 異なるバウンデッドコンテキスト

```
App\Domains\Order\*      ← D=5-6
App\Domains\Shipping\*   ← 異なるBC
```

### 7-8: 異なるパッケージ/ライブラリ

```
packages/billing/         ← D=7
vendor/stripe/stripe-php  ← D=8
```

### 9: 分散サービス

```php
// HTTP経由の通信
$response = Http::get('http://inventory-service/api/stock');
```

### 10: 外部ベンダーシステム

```php
// サードパーティAPI
$stripe = new \Stripe\StripeClient($apiKey);
$stripe->charges->create([...]);
```

## 判定フロー

```
外部API? ─Yes→ 10
    │No
HTTP/gRPC? ─Yes→ 9
    │No
vendor or packages? ─Yes→ 7-8
    │No
異なるBC? ─Yes→ 5-6
    │No
異なるディレクトリ? ─Yes→ 3-4
    │No
同一ディレクトリ? ─Yes→ 2
    │No
同一クラス → 1
```

## 距離と組織の関係

| D | デプロイ | チーム |
|---|----------|--------|
| 1-4 | 同一 | 同一 |
| 5-6 | 同一 | 同一/異なる |
| 7-8 | 同一/異なる | 異なる |
| 9-10 | 異なる | 異なる |
