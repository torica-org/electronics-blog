# electronics-blog
Blog用リポジトリです．
> GitHub Pages: https://torica-org.github.io/electronics-blog/

## 書き方
- ファイル名は，`タイトル.md`としてください．
- フロントマター(Front Matter)を次のように設定してください．
    ```markdown
    ---
    layout: default
    title: 「kenno」の寝言
    date: 2026-06-13
    ---
    ```
- 一番大きな見出し(h1)`#`には，タイトルを設定してください．
- サークルの作業に関する記事は`work/`内に追加してください．
- 個人の記事は`negoto/`内に追加してください．
- スポンサー・提供など，PR目的の記事は`commercials/`に追加してください．

## JekyllとLiquidを利用した自動列挙
以下のディレクトリ配下にあるマークダウン(`.md`)ファイルは，
ブログのトップページに自動的に列挙されます．
その際，設定したフロントマターの
`title`と`date`が使用され，最新のものが最上位に表示されるようになります．
- `commercials/`
- `work/`
- `negoto/`
