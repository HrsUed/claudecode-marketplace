# claudecode-marketplace

Claude Code プラグインのマーケットプレイスリポジトリ。

## プラグイン一覧

| プラグイン | 説明 | バージョン |
|-----------|------|-----------|
| [coupling-analysis](./plugins/coupling-analysis/) | 均衡結合モデルでコンポーネントの結合バランスを評価し、設計改善を提案 | 1.0.0 |
| [drawio](./plugins/drawio/) | draw.io形式のダイアグラムを生成し、PNG/SVG/PDFへのエクスポートに対応 | 1.0.0 |
| [refactor-legacy](./plugins/refactor-legacy/) | レガシーコードを6フェーズで段階的に安全にリファクタリング | 1.0.0 |
| [refactor-suggest](./plugins/refactor-suggest/) | Martin Fowlerのリファクタリングカタログに基づくコード改善提案 | 1.0.0 |

## インストール

### マーケットプレイスとして追加

```bash
claude plugin marketplace add HrsUed/claudecode-marketplace
```

### プラグインをインストール

```bash
claude plugin install coupling-analysis@claudecode-marketplace
```

### ローカルで試す

```bash
claude --plugin-dir ./plugins/coupling-analysis
```


## License

MIT
