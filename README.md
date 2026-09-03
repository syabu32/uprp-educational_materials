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

## ドキュメントの書き方

資料は `docs/` 配下に Markdown ファイルとして追加していきます。基本の流れは次のとおりです。

1. `docs/` 配下にフォルダ・ファイルを作成する(例: `docs/サカロボ/はじめに.md`)
2. ファイル先頭に frontmatter でアイコンなどを指定できます

   ```markdown
   ---
   icon: lucide/rocket
   ---

   # ページタイトル
   ```

3. `zensical.toml` の `nav` に追加したページを登録する

   ```toml
   nav = [
     { "サカロボ" = [
       { "はじめに" = "サカロボ/はじめに.md" },
     ] },
   ]
   ```

   **`nav` に登録しないとナビゲーションに表示されず、直接URLを知らないと辿り着けません。** 新しいページを追加したら必ずセットで編集してください。

4. `uv run zensical serve` で見た目を確認しながら書く

見出し・強調・表・タブ切り替え・admonition(`!!! note` など)・コードブロックのシンタックスハイライトといったZensicalのMarkdown記法は公式ドキュメントにまとまっています。

- 記法一覧: https://zensical.org/docs/authoring/markdown/
- 実例(初期生成された `docs/index.md` にサンプルが一通り載っています)

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
