# uprp-educational_materials

教育用資料を [Zensical](https://zensical.org/) で作成し、GitHub Pages で公開するためのリポジトリです。

- 公開URL: https://syabu32.github.io/uprp-educational_materials/
- Markdown: `docs/` 配下に配置
- 設定ファイル: `zensical.toml`

## セットアップ

[uv](https://docs.astral.sh/uv/) を使って仮想環境を構築します。

```bash
uv sync
```

## ローカルプレビュー

```bash
uv run zensical serve
```

`http://localhost:8000` で確認できます。

## ビルド

```bash
uv run zensical build --clean
```

`site/` ディレクトリに静的サイトが出力されます。

## 公開

`main` ブランチに push すると、GitHub Actions ([.github/workflows/docs.yml](.github/workflows/docs.yml)) が自動でビルドし、GitHub Pages に公開します。
