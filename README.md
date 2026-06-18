# hrsued-cc-plugin-marketplace

Claude Code プラグインのマーケットプレイスリポジトリ。

## プラグイン一覧

| プラグイン | 説明 | バージョン |
|-----------|------|-----------|
| [coupling-analysis](./plugins/coupling-analysis/) | 均衡結合モデルでコンポーネントの結合バランスを評価し、設計改善を提案 | 1.0.0 |
| [dandori](./plugins/dandori/) | 検討着手直後に前提シートを固め、構造変更は提案→承認→一括実装で手戻りを防ぐプロトコル | 1.0.0 |
| [refactor-legacy](./plugins/refactor-legacy/) | レガシーコードを6フェーズで段階的に安全にリファクタリング | 1.0.0 |
| [refactor-suggest](./plugins/refactor-suggest/) | Martin Fowlerのリファクタリングカタログに基づくコード改善提案 | 1.0.0 |

## インストール

### マーケットプレイスとして追加

```bash
claude plugin marketplace add HrsUed/claudecode-marketplace
```

### プラグインをインストール

```bash
claude plugin install coupling-analysis@hrsued-cc-plugin-marketplace
```

### ローカルで試す

```bash
claude --plugin-dir ./plugins/coupling-analysis
```


## License

MIT
