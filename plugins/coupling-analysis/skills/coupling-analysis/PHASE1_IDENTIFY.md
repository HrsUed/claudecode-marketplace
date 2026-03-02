# Phase 1: 依存関係の特定

## 目的

対象コンポーネントの役割を理解し、依存関係を特定する。

## 手順

### 1. 対象特定

| 入力形式 | 実行 |
|----------|------|
| ファイル | 該当ファイルを読み込み |
| クラス | `find_symbol` で特定 |
| ディレクトリ | `Glob "dir/**/*.php"` |
| `recent` | `git diff HEAD~1 --name-only` |

### 2. 基本情報を把握

```
- クラス名と責務（1文）
- レイヤー: Controller / Service / Repository / Domain / Infrastructure
- サブドメイン: コア(V=9-10) / 重要(V=7-8) / 支援(V=5-6) / 汎用(V=3-4) / レガシー(V=1-2)
```

### 3. 依存関係を抽出

**チェックポイント**:

```php
use App\Services\XxxService;        // use文
class MyClass extends BaseClass     // 継承
private XxxRepository $repository;  // プロパティ
public function __construct(Xxx $x) // DI
$this->service->method();           // メソッド呼び出し
```

**依存の種類**:

| 種類 | 記号 | 強度傾向 |
|------|------|----------|
| 継承 | ◀━━ | 高 |
| コンポジション | ◀── | 中〜高 |
| 利用 | ◀┄┄ | 低〜中 |
| データ共有 | ◀═══ | 中 |
| イベント | ◀ ─ ─ | 低 |

### 4. 下流依存も確認（重要な場合のみ）

```bash
# このクラスを参照している箇所
Grep "use.*ClassName" --type php
Grep "new ClassName" --type php
```

## 出力

```markdown
## Phase 1: 依存関係の特定

**対象**: `path/to/File.php`
**責務**: [1文]
**レイヤー**: Service
**サブドメイン**: 重要 (V=8)

### 上流依存

| # | 依存先 | 種類 | 用途 |
|---|--------|------|------|
| 1 | OrderRepository | コンポ | 永続化 |
| 2 | Order | データ | ドメインモデル |

### 下流依存（任意）

| # | 依存元 | 種類 |
|---|--------|------|
| 1 | OrderController | 利用 |

**次**: Phase 2
```
