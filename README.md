# parse-vector-pdf

ベクターPDFの「上部に横並びで存在する2つの機器表」を抽出し、1つに結合して出力するツールです。

## 前提

- Python 3
- `pdfplumber`
- 入力PDF（デフォルト）: `data/機器表1.pdf`

## 使い方

### 1. デフォルト実行

```bash
python3 extract_top_tables.py
```

デフォルトで以下を出力します。

- `data/ventilation_equipment_merged.xlsx`
- `data/ventilation_equipment_merged.csv`（2行ヘッダー）
- `data/ventilation_equipment_merged_flat.csv`（1行ヘッダー）

### 2. 4列版CSVも出力

```bash
python3 extract_top_tables.py --four-column
```

追加で以下を出力します。

- `data/ventilation_equipment_four_columns.csv`

列は次の4つです。

- `機器番号`
- `名称`
- `動力 (50Hz)_消費電力 (KW)`
- `台数`

### 3. 正解XLSXでヘッダー検証（検証専用）

```bash
python3 extract_top_tables.py \
  --validate-against-xlsx "/path/to/ventilation_equipment_final.xlsx"
```

`--validate-against-xlsx` は検証用途のみで、抽出処理には使いません。

## 主なオプション

- `pdf`: 入力PDF（省略時は `data/機器表1.pdf`）
- `--output-xlsx`: XLSX出力先
- `--output-csv`: 2行ヘッダーCSV出力先
- `--output-csv-flat`: 1行ヘッダーCSV出力先
- `--four-column`: 4列CSV出力を有効化
- `--output-csv-four`: 4列CSV出力先
- `--validate-against-xlsx`: ヘッダー検証用XLSX

## どのように処理しているか（簡潔）

1. PDF内の表候補から「幅が広く、ページ下部でない」2表のみを対象にする。  
2. 罫線（縦線・横線）を解析し、19列グリッドを確定してセルを抽出する。  
3. ヘッダーはPDF文字座標から2段構成で再構成する（テンプレート参照なし）。  
4. データは `機器番号` 行を起点に、継続行を同一レコードへ結合する。  
5. 左表→右表の順で縦結合し、XLSX/CSVへ出力する。  

