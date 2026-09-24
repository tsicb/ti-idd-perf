# ti-idd-perf

共有フォルダ / ローカルフォルダから Indeed PLUS 分析用 Parquet を読み込むブラウザ版です。

## 役割

このリポジトリは共有フォルダ版UIのdeployment先です。

```text
index.html
  -> ユーザーが共有フォルダ / ローカル output_dataset を選択
  -> DuckDB-Wasm
  -> Parquetをブラウザ内で集計
```

Cloudflare Worker / R2 の自動remote読込は含めません。

Cloudflare版の正本は別リポジトリです。

```text
tsicb/ti-indeed-parquet-build
  cloudflare/worker/public/index.html
```

## 共通UI改修の同期ルール

集計・検索・表示など、Cloudflare固有ではないUIロジックを変更した場合は、次の2ファイルへ同じ修正を反映します。

```text
tsicb/ti-indeed-parquet-build
  cloudflare/worker/public/index.html

tsicb/ti-idd-perf
  index.html
```

Cloudflare固有の次の処理は、この共有フォルダ版には追加しません。

- R2 current release 自動読込
- `/api/data/*` アクセス
- Worker / Access 前提のremote loading
- Cloudflare固有のrelease pointer処理

## データセット

共有フォルダ版では、次のような `output_dataset` を選択します。

```text
output_dataset/
├─ manifest.json
├─ fact/
│  └─ fact_YYYYMM.parquet
└─ master/
   ├─ company_master.parquet
   ├─ company_master.csv
   ├─ month_master.parquet
   ├─ month_master.csv
   ├─ job_title_master.parquet
   └─ job_title_master.csv
```

R2 productionから取得する場合は、`tsicb/ti-indeed-parquet-build` の GitHub Actions:

`Export R2 Parquet release`

を使用すると、共有フォルダ版互換の `output_dataset/` を生成できます。

## 関連職種の集計仕様

関連職種バッジの候補生成と、選択後の表・絞り込み・月別推移では同じ母集団を使用します。

```text
関連職種_集計用
  fallback: 関連職種
+
Indeed職種
```

これにより、Indeed職種由来で候補に出たバッジも、選択後の集計で0件になることなく同じ条件で判定されます。

## 関連プロジェクト

全体アーキテクチャ、R2、ETL、release、Cloudflare Worker、Access、運用手順については以下を参照してください。

`tsicb/ti-indeed-parquet-build/PROJECT_HANDOFF.md`
