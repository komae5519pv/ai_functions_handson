# AI Functions カタログ

Databricks の **AI 関数（AI Functions）22 個すべて**について、「どんな処理をしたいときに使うか」「何を指定するか」「何が返ってくるか」を、動かしながら確かめるためのノートブック集です。

各ノートブックは、自分でサンプルデータを作ってから関数を試すので、**どれから開いても単独で動きます**（例外: `42_ai_search` は `41_vector_search` で作るインデックスを使います）。

---

## AI 関数とは

SQL の `UPPER()` や `SUM()` と同じ感覚で、テーブルの列に対して AI の処理（分類・抽出・要約・翻訳・予測など）をかけられる組み込み関数です。モデルのデプロイや API キーの管理は要りません。

```sql
SELECT body, ai_classify(body, '["配送", "支払い", "故障"]') AS category
FROM inquiries
```

1 行ごとに AI モデルを呼ぶので、時間と料金がかかります。**結果は一度テーブルに保存して使い回す**のが基本です（どのノートブックも最後に保存の例があります）。

---

## ノートブックの番号の付け方

番号は 2 桁で、**十の位が分類、一の位がその分類の中での順番**です。そのため 1 番からではなく 11 番から始まります。

| 番号 | 分類 | 関数 |
|---|---|---|
| 00 | 準備 | `00_setup_guide`（画面での設定）、`00_config`（共通設定） |
| 11〜19 | 文章を処理する | `ai_analyze_sentiment` / `ai_classify` / `ai_extract` / `ai_mask` / `ai_fix_grammar` / `ai_translate` / `ai_summarize` / `ai_similarity` / `ai_decide` |
| 21〜23 | 文章を作る・モデルを自由に呼ぶ | `ai_gen` / `ai_query` / `ai_enrich` |
| 31〜33 | 書類・音声を読む | `ai_parse_document` / `ai_prep_search` / `ai_transcribe` |
| 41〜42 | 検索する | `vector_search` / `ai_search` |
| 51〜55 | 予測・分析する | `ai_predict_class` / `ai_predict_value` / `ai_forecast` / `ai_detect_anomalies` / `ai_top_drivers` |

同じ分類の関数が一覧で並び、「書類は 30 番台」のように番号から探せます。関数が増えたときも、後ろの番号を振り直さずに足せます。

---

## やりたいこと → 使う関数

### 11〜19 文章を処理する

| やりたいこと | 関数 | 返ってくるもの | 段階 |
|---|---|---|---|
| 文章の感情（良い / 悪い）を判定したい | `11_ai_analyze_sentiment` | positive / negative / neutral / mixed | Public Preview |
| 自分で決めたラベルで分類したい | `12_ai_classify` | ラベル（＋確信度・理由） | GA |
| 文章や書類から項目を取り出して表にしたい | `13_ai_extract` | 項目ごとの値（＋確信度・引用） | GA |
| 個人情報を伏せたい | `14_ai_mask` | `[MASKED]` に置き換えた文章 | Public Preview |
| 誤字・脱字を直したい | `15_ai_fix_grammar` | 直した文章 | Public Preview |
| 翻訳したい | `16_ai_translate` | 翻訳した文章（翻訳先は 8 言語） | Public Preview |
| 要約したい | `17_ai_summarize` | 要約 | Public Preview |
| 2 つの文章の近さを測りたい（名寄せ） | `18_ai_similarity` | 0〜1 のスコア | Public Preview |
| 確率・スコアで判断したい | `19_ai_decide` | 確率 / 選んだ候補 / 段階スコア | Beta |

### 21〜23 文章を作る・モデルを自由に呼ぶ

| やりたいこと | 関数 | 返ってくるもの | 段階 |
|---|---|---|---|
| 指示どおりに文章を作りたい | `21_ai_gen` | 生成した文章 | Public Preview |
| モデルを選んで自由に処理したい・画像を読ませたい | `22_ai_query` | モデルの回答（JSON の形も指定可） | GA |
| 表に新しい列を足したい（Web・社内文書を根拠にも） | `23_ai_enrich` | 列ごとの値＋理由＋出典 | Beta |

### 31〜33 書類・音声を読む

| やりたいこと | 関数 | 返ってくるもの | 段階 |
|---|---|---|---|
| PDF・Office・画像を読み取りたい | `31_ai_parse_document` | 本文・表・図・見出しの要素 | GA |
| 文書を検索（RAG）用に分割したい | `32_ai_prep_search` | 文脈付きのチャンク | Beta |
| 音声を文字に起こしたい | `33_ai_transcribe` | 時刻・話者付きのテキスト | Beta |

### 41〜42 検索する

| やりたいこと | 関数 | 返ってくるもの | 段階 |
|---|---|---|---|
| 意味の近い文書を探したい | `41_vector_search` | 近い順の文書 | Public Preview |
| 社内文書を検索して答えまで作りたい | `42_ai_search` | 関連文書＋根拠付きの答え | Beta |

### 51〜55 予測・分析する

| やりたいこと | 関数 | 返ってくるもの | 段階 |
|---|---|---|---|
| 過去の実績から分類を予測したい（解約など） | `51_ai_predict_class` | 予測＋確率 | Beta |
| 過去の実績から数値を予測したい（売上など） | `52_ai_predict_value` | 予測値 | Beta |
| 時系列の将来を予測したい | `53_ai_forecast` | 予測値＋上限・下限 | v1: Public Preview / v2: Beta |
| 時系列の異常を見つけたい | `54_ai_detect_anomalies` | 期待値・異常スコア・異常フラグ | Beta |
| 数値の変化の要因を探したい | `55_ai_top_drivers` | 効いた切り口のランキング | Beta |

段階は 2026-10-30 時点の公式ドキュメントによります。

---

## フォルダの構成

```
00_setup_guide     … 事前準備の手順（プレビューの有効化など、画面で設定すること。キャプチャ付き）
00_config          … 共通設定（カタログ・スキーマ・モデル名など）。ここだけ書き換えれば全ノートブックが動く。2 つ目のセルは片付け用
README.md          … このファイル
assets/            … サンプルファイル（請求書 PDF・製品マニュアル PDF・レシート画像・英語の通話録音）
01_SQL/            … SQL だけで書いた版（22 本）
02_Python_SQL/     … Python で変数を定義し、spark.sql() の中で SQL を書く版（22 本）
03_Python/         … Python（PySpark の DataFrame API）だけで書いた版（22 本）
```

3 つのフォルダには、**同じ番号・同じ構成・同じ処理**のノートブックが入っています。違うのは書き方だけです。番号の意味は「ノートブックの番号の付け方」を見てください。

### 3 つの書き方の違い

| | 01_SQL | 02_Python_SQL | 03_Python |
|---|---|---|---|
| 書き方 | `SELECT ai_classify(body, '[...]') FROM inquiries` | `spark.sql(f"SELECT ai_classify(body, :labels) FROM {CATALOG}.{SCHEMA}.inquiries", args={"labels": labels})` | `df.select(F.call_function("ai_classify", F.col("body"), F.lit(labels)))` |
| 向いている人・場面 | SQL を書く分析者。SQL エディタ・ダッシュボード・Genie・Lakeflow パイプライン（SQL）にそのまま持っていく | Python の処理の中で、SQL のほうが読みやすい部分だけ SQL で書きたいとき。値を変数にして使い回したいとき | データエンジニア。既存の PySpark のパイプラインに組み込むとき。関数で部品化してテストしたいとき |
| よいところ | いちばん短く読みやすい。SQL ウェアハウスでも動く | ラベル・スキーマ・しきい値などを Python の変数で管理でき、`:名前` のパラメータで安全に SQL に渡せる | 型の付いた DataFrame のまま処理をつなげられる。Python の関数・ループ・テストと組み合わせやすい |
| 注意するところ | 値を変えるには SQL の文字列を書き換える | SQL が文字列の中にあるので、エディタの補完や構文チェックが効きにくい | 名前付きの引数（`responseFormat =>` など）は `F.expr()` の中に書く。テーブル値関数（予測・検索系）は列関数として呼べないので `spark.sql(..., src=df)` を使う |

どの書き方でも、中で動く AI 関数と結果は同じです。チームでよく使う書き方に合わせて選んでください。

---

## 使い方

1. このフォルダごとワークスペースに置く（Git フォルダでクローン、またはインポート）
2. `00_setup_guide` を読み、画面での設定（Beta の関数のプレビューの有効化など）を済ませる
3. `00_config` の先頭の定数（`CATALOG`・`SCHEMA`・`LLM_ENDPOINT` など）を自分の環境に合わせて書き換える。`WAREHOUSE_ID` は空のままで構いません（使える SQL ウェアハウスを自動で選びます）
4. 使いたい書き方のフォルダから、試したい関数のノートブックを開き、**サーバレス**のコンピュートで上から順に実行する
   - `01_SQL/53_ai_forecast` と `01_SQL/54_ai_detect_anomalies` だけは **SQL ウェアハウス**で実行します（この 2 つは SQL ウェアハウスでしか動かない関数のため）。SQL ウェアハウスでは `%run` が使えないので、この 2 本は最初のセルで `00_config` と同じカタログ・スキーマを指定しています

各ノートブックの最初に、その関数の前提（必要なランタイム・プレビューの有効化）を書いてあります。

---

## 前提

- Unity Catalog が有効なワークスペースと、サーバレスのコンピュート（**環境バージョン 3 以上。既定の 1 では動きません**。作成時に 4 と 6 で全ノートブックを確認済み）
- `ai_forecast` / `ai_detect_anomalies` を実行する Pro または Serverless の SQL ウェアハウス（既存のもの）
- Beta の関数は、ワークスペース管理者が **画面上部のユーザー名 → プレビュー** で次をオンにする必要があります（手順は `00_setup_guide`）

| プレビュー名 | 対象の関数 |
|---|---|
| AI Decide | `ai_decide` |
| AI Enrich | `ai_enrich` |
| AI Transcribe | `ai_transcribe` |
| Predictive AI Functions | `ai_predict_class` / `ai_predict_value` / `ai_detect_anomalies` / `ai_top_drivers` / `ai_forecast`（v2） |

### 日本語について

- v1 の関数（感情・要約・翻訳など）は「英語向けに調整」と公式に書かれています。日本語でも動きますが、結果は必ず確認してください。作成時の実行では、`ai_summarize` に日本語を渡しても英語で返ることがありました
- `ai_translate` の翻訳先に日本語は含まれていません。日本語に訳すときは `ai_query` を使います（`16_ai_translate` に例があります）
- `ai_transcribe` は Beta 期間中、英語とスペイン語のみ対応です

---

## 実行すると作られるもの

`00_config` の `CATALOG.SCHEMA` に、次のテーブルなどが作られます。

| 種類 | 名前 | 中身 |
|---|---|---|
| サンプルデータ | `inquiries` | カスタマーサポートへの問い合わせ 8 件 |
| | `reviews` | 商品レビュー 8 件 |
| | `products` / `supplier_items` | 自社の商品マスタ 6 件 / 表記の違う仕入先の納品データ 7 件 |
| | `draft_texts` / `product_descriptions` / `visit_notes` | 誤字のある下書き / 商品説明と海外レビュー / 営業の訪問メモ |
| | `faq_articles` | FAQ 記事 10 件（AI Search のソース） |
| | `daily_sales` | 3 店舗の日次売上（2026 年 4〜9 月。異常値を 3 日分入れてある） |
| | `orders` | 注文明細 約 5,600 件（7〜9 月は「関西 × アプリ」が落ちるように作ってある） |
| | `members` / `store_locations` | 会員の利用状況と解約実績 / 既存店の実績と出店候補地 |
| ボリューム | `files` | `assets` の PDF・画像・音声のコピー |
| AI Search | `faq_articles_index` | FAQ の本文をベクトル化したインデックス（`41_vector_search` で作成） |
| 結果のテーブル | `*_sentiment` / `*_classified` / `*_extracted` / `*_forecast` など | 各ノートブックの最後で保存した結果 |

サンプルデータはすべて架空のものです。

---

## 作られるものと料金・片付け

| 作られるもの | どのノートブックで | 料金 |
|---|---|---|
| スキーマ・テーブル・ボリューム（`00_config` の `CATALOG.SCHEMA`） | 全ノートブック | 保存しているデータの分だけ（サンプルなのでごくわずか） |
| 💰 AI Search のエンドポイント（`VS_ENDPOINT`） | `41_vector_search`（同じ名前のものがなければ新しく作る） | **起動している間ずっとかかる**（使っていなくてもかかる） |
| 💰 AI Search のインデックス（`VS_INDEX`） | `41_vector_search` | 同期と保存の分 |
| AI 関数の呼び出し | 全ノートブック | 呼んだ分だけ |

SQL ウェアハウスとモデルのエンドポイントは、既にあるものを使うだけで新しくは作りません。

**片付けは `00_config` の 2 つ目のセル「作成したリソースの削除（リセット）」で行います。** ふだんはコメントアウトしてあり、何も起きません。片付けるときは、`00_config` を直接開き、消したいものの行のコメントを外して、そのセルだけを実行します（ほかのノートブックの `%run ../00_config` からは動かないようにしてあります）。

- スキーマの削除（`DROP SCHEMA ... CASCADE`）は、中のテーブル・ボリューム・ファイルがすべて消え、元に戻せません。このノートブック集のために新しく作ったスキーマのときだけ使ってください
- AI Search のエンドポイントは、ほかの用途でも使っているものは消さないでください
