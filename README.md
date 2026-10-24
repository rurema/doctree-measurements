# doctree-measurements

[rurema/doctree](https://github.com/rurema/doctree)(Ruby リファレンスマニュアル刷新計画の原稿)を
実際の Ruby 処理系と突き合わせた実測データと、その再生成スクリプトの置き場。

2026-10 に doctree の `tools/` 配下から移した(移す前のものは doctree の履歴、2026-10-24 の 5f0f77d5d まで)。実測データ(docker の実 Ruby から採った TSV や
doctree の BitClust DB からの抽出)が doctree の clone を膨らませるためで、原稿のビルドには使われない。
doctree では `.gitignore` にしていた `method-params/db-extract/`(版別 DB からの抽出・doctree 5f0f77d5d の DB から)と
`method-params/result/*/lib-mismatch.tsv` も、ここでは含めている。

## データ一式

| ディレクトリ | 内容 | 対象の版 | 関連 issue |
|--------------|------|----------|------------|
| [method-versions](method-versions/README.md) | 組み込みメソッドの版ごとの存在と、`{: since=}`/`{: until=}` バッジの検証 | 1.8.6〜4.0(各 minor の最新 teeny。4.1 は未実測) | rurema/bitclust#132・rurema/doctree#3269 |
| [library-versions](library-versions/README.md) | 標準添付ライブラリのライブラリ/ファイル/メソッド単位の存在 | 3.0〜4.1(4.1 はソースツリーでの判定のみ) | rurema/doctree#3515〜#3531(修正 PR)・rurema/doctree#3532(データ追加) |
| [method-coverage](method-coverage/README.md) | 版別 DB の全メソッドエントリと実 Ruby のメソッドの過不足(組み込み+標準添付) | 3.0〜4.1(x.y.0。4.1 は master) | rurema/doctree#3548・rurema/doctree#3566 |
| [method-params](method-params/README.md) | メソッドの引数(シグネチャ)と実 Ruby・RBS の突き合わせ、`check_params` | 3.0〜4.1(x.y.0。4.1 は master) | rurema/doctree#3645・rurema/doctree#3589 |

各ディレクトリに README.md(データの生成時点・ファイル構成・再生成手順・既知の限界)、
report.md(分析の本文)、`tools/`(スクリプト)がある。

## 使い方

doctree と bitclust を同じ階層に clone しておく前提で、スクリプトはこのリポジトリのルートで実行する。

```
rurema/
├── bitclust/
├── doctree/
└── doctree-measurements/   ← ここで実行
```

- doctree の原稿(`manual/api` など)は環境変数 `DOCTREE_PATH`(既定 `../doctree`)から読む
- bitclust は `BITCLUST_PATH`(既定 `../bitclust`。doctree の Gemfile と同じ)。
  `Gemfile` は `bitclust-core` だけを入れる(`BITCLUST_PATH` のディレクトリが無ければ GitHub から取る)

```sh
bundle install
bundle exec ruby method-params/tools/extract_db_sigs.rb <dbpath> method-params <版>
# bundle を使わない場合
ruby -I${BITCLUST_PATH:-../bitclust}/lib method-params/tools/extract_db_sigs.rb <dbpath> method-params <版>
```

## 再生成

手順は各 README の「再生成手順」(method-versions は「再現方法」)を参照。

- [method-versions](method-versions/README.md#再現方法)
- [library-versions](library-versions/README.md#再生成手順)
- [method-coverage](method-coverage/README.md#再生成手順)
- [method-params](method-params/README.md#再生成手順)

実 Ruby の測定には docker イメージ `ghcr.io/ruby/all-ruby`(各版のバイナリ)と
`ghcr.io/ruby/ruby:master`(4.1 の開発版)などを使う。docker は 1 版ずつ順に起動する(メモリの少ない環境で並列にすると落ちる)。

## ライセンス

doctree と同じ CC BY 3.0。[LICENSE](LICENSE) を参照。
