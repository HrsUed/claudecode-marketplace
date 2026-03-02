# 変動性スケール (Volatility)

コンポーネントの変更頻度（予想）。

## クイックリファレンス

| V | サブドメイン | 特徴 |
|---|--------------|------|
| 1-2 | レガシー/外部 | 変更なし, バージョン固定 |
| 3-4 | 汎用/インフラ | 認証, ログ, Repository実装 |
| 5-6 | 支援 | 通知, レポート, 管理画面 |
| 7-8 | 重要 | 注文処理, 在庫管理 |
| 9-10 | コア | 差別化機能, 競争優位 |

## 詳細スケール

### 1-2: レガシー/外部ライブラリ

```php
// 変更予定なし
use Illuminate\Support\Collection;  // V=1
use Carbon\Carbon;                   // V=1
```

### 3-4: 汎用サブドメイン

```php
// 認証、ログ、共通ユーティリティ
App\Infrastructure\Auth\*
App\Infrastructure\Logger\*
App\Repositories\*  // Repository実装
```

### 5-6: 支援サブドメイン

```php
// ビジネスを支援するが差別化要素ではない
App\Domains\Notification\*
App\Domains\Report\*
App\Domains\Admin\*  // 管理画面
```

### 7-8: 重要サブドメイン

```php
// ビジネス上重要だが業界標準的
App\Domains\Order\*      // 注文処理
App\Domains\Inventory\*  // 在庫管理
App\Domains\Payment\*    // 決済
```

### 9-10: コアサブドメイン

```php
// 競争優位の源泉、頻繁に進化
App\Domains\Pricing\*      // 独自価格戦略
App\Domains\Matching\*     // 独自マッチングアルゴリズム
App\Domains\Fulfillment\*  // 独自フルフィルメント最適化
```

## 判定フロー

```
競争優位の源泉? ─Yes→ 9-10
       │No
主要ビジネス機能? ─Yes→ 7-8
       │No
ビジネス支援機能? ─Yes→ 5-6
       │No
インフラ/共通機能? ─Yes→ 3-4
       │No
外部/レガシー → 1-2
```

## 変動性と依存の関係

**原則**: 高変動性への依存は最小限に

| 依存元V | 依存先V | 評価 |
|---------|---------|------|
| 高(8+) | 低(1-4) | ✅ 安全 |
| 高(8+) | 高(8+) | ⚠️ 注意 |
| 低(1-4) | 高(8+) | 🔴 危険 |

```
安定した方向に依存せよ（SDP: Stable Dependencies Principle）
```
