# data_schema.md — データ層仕様書（旧システム事実）

> **信頼度ラベル**: [VERIFIED]
> **作成日**: 2026-03-02
> **対応SP**: SP-002-01 / SP-002-02 / SP-002-03
> **書き分けルール**: 旧システムの事実のみ記載。新システムのDB設計・技術選定は書かない。

---

## 概要

このシステムはリレーショナルDBを持たない。データの永続化はすべて **CSV / CDXML / JSON ファイル**で行われる。

| 種別 | ファイル数 | 主な役割 |
|------|----------|---------|
| CSV | 8 | レジスト配合・ポリマー定義・物性計算結果の保存 |
| CDXML（ChemDraw XML） | 10 | 化学材料マスタ（入力5・出力5） |
| JSON | 1 | 化合物IDの採番台帳 |
| **合計** | **19** | |

> `.ipynb_checkpoints/` 配下の自動バックアップファイルは除外。

---

## 1. ファイル間のキー関係（全体像）

```
[入力マスタ CDXML] ──(化学構造)──→ id_registry.json ──(ID採番)──→ [出力 CDXML + 各CSV]
  Solvent.cdxml                        Intermediate/                  Result_260202/
  Additive.cdxml                                                      result_with_id.csv
  Monomer.cdxml
  PAG.cdxml
  PDB.cdxml

[レジスト配合UI操作]
  ──→ Intermediate/mixture_*_components.csv  (セッション毎生成)
  ──→ Intermediate/polymer_definitions.csv
  ──→ Result_260202/*.csv
  ──→ result_with_id.csv（ルート・サマリ）
```

**主キー**:
- レジスト: `Resist_ID`（例: R-1, R-2, R-3）
- 化合物: カテゴリ別プレフィックス + 連番（例: P-1, M-1, A-2, S-1）

---

## 2. CSV ファイル一覧

### 2-1. result_with_id.csv（ルート）

| 項目 | 値 |
|------|-----|
| 役割 | 全レジストの計算結果サマリ（1レジスト1行） |
| パス | `result_with_id.csv`（コードベースルート） |
| 行数（実測） | 3行（R-1〜R-3） |
| 信頼度 | High |

| カラム名 | dtype | 備考 |
|---------|-------|------|
| Resist_ID | str | 例: R-1 |
| LWR | float | 線幅ラフネス（性能指標） |
| Field 2〜4 | float | ユーザー定義カスタムフィールド（空値はNaN） |
| Memo | float | メモ欄（空値がNaN化 — 設計上はstr） |
| k | float | 計算値（消衰係数） |
| OP | float | 計算値（光学定数） |
| MOP | float | 計算値 |
| LogP | float | 計算値（疎水性） |
| PDB/PAG | float | 計算値（PDB/PAG比） |
| TSC | float | 計算値（TSC） |
| Polymer | str | 例: `P-1 (HydroxyStyrene=70;Styrene=30) [100]` |
| PAG | str | 例: `GoodPAG_pag [2]` |
| PDB | str | 例: `GoodPDB_pag [3]` |
| Solvent | str | 例: `PGME [1640] / PGMEA [410]` |
| FPolymer | str | 例: `F-1 (Styrene=100) [2]` |
| Additive | str | 例: `Acetic acid [1]` |

---

### 2-2. Intermediate/mixture_\*_components.csv

| 項目 | 値 |
|------|-----|
| 役割 | レジスト配合をロング形式に展開した中間データ |
| パス | `Intermediate/mixture_{タイムスタンプ}_components.csv` |
| 生成タイミング | セッション実行時（都度生成・累積）|
| 実測ファイル | 2件（073645: 42行 / 073719: 61行） |
| 信頼度 | High |

| カラム名 | dtype | 取り得る値 |
|---------|-------|---------|
| Resist_ID | str | R-* / `GLOBAL`（全体設定行） |
| Type | str | `"Meta"` / `"Component"` / `"Property"` |
| Category | str | `"Polymer"`, `"PAG"`, `"PDB"`, `"Solvent"`, `"FPolymer"`, `"Additive"`, `"Label"`, `"Memo"`, `"Solvent_Ratios"`, `"Calculated"`, `"Custom"` |
| Name | str | 材料名・プロパティ名 |
| Value | str | 数値・文字列（すべて文字列型で保存） |

> **注意**: `Type` / `Category` の値はコード内文字列リテラルで管理されており、定数（Enum）定義は存在しない。

---

### 2-3. Intermediate/polymer_definitions.csv

| 項目 | 値 |
|------|-----|
| 役割 | Polymer / FPolymer の定義情報（ID・組成・分子量） |
| パス | `Intermediate/polymer_definitions.csv` |
| 生成タイミング | セッション実行時（追記方式。最新行が優先） |
| 行数（実測） | 2行（P-1, F-1） |
| 信頼度 | High |

| カラム名 | dtype | 備考 |
|---------|-------|------|
| Category | str | `"Polymer"` または `"FPolymer"` |
| ID | str | 例: P-1, F-1 |
| Signature | str | 例: `HydroxyStyrene=70;Styrene=30` |
| Mw | float | 重量平均分子量（0.0 = 未設定） |
| Mw_Mn | float | 分子量分布（0 = 未設定） |

---

### 2-4. Result_260202/resist_table.csv

| 項目 | 値 |
|------|-----|
| 役割 | レジスト配合表（成分・量） |
| パス | `Result_260202/resist_table.csv` |
| 行数（実測） | 3行（R-1〜R-3） |
| 信頼度 | High |

| カラム名 | dtype | 備考 |
|---------|-------|------|
| レジスト種 | str | R-* |
| ポリマー種 | str | P-* |
| ポリマー質量 | int | |
| PAG種 | str | A-*（空あり） |
| PAG量 | float | |
| PDB種 | str | B-* |
| PDB量 | int | |
| 溶剤種 | str | `S-1/S-2` 形式（複数スラッシュ区切り） |
| 溶剤量 | str | `1640/410` 形式 |
| フッ素ポリマー種 | str | F-* |
| フッ素ポリマー量 | int | |
| 添加剤種 | str | D-* |
| 添加剤量 | int | |

---

### 2-5. Result_260202/polymer_table.csv

| 項目 | 値 |
|------|-----|
| 役割 | ポリマーのモノマー組成表 |
| 行数（実測） | 1行（P-1） |
| 信頼度 | High |

| カラム名 | dtype |
|---------|-------|
| ポリマー種 | str |
| モノマー種1 | str |
| モノマー割合1 | int |
| モノマー種2 | str |
| モノマー割合2 | int |
| Mw | float |
| Mw/Mn | int |

---

### 2-6. Result_260202/fpolymer_table.csv

| 項目 | 値 |
|------|-----|
| 役割 | フッ素ポリマーのモノマー組成表 |
| 行数（実測） | 1行（F-1） |
| 信頼度 | High |

| カラム名 | dtype |
|---------|-------|
| フッ素ポリマー種 | str |
| モノマー種1 | str |
| モノマー割合1 | int |
| Mw | float |
| Mw/Mn | int |

---

### 2-7. Result_260202/performance_table.csv

| 項目 | 値 |
|------|-----|
| 役割 | レジストの性能評価結果（LWR） |
| 行数（実測） | 3行（R-1〜R-3） |
| 信頼度 | High |

| カラム名 | dtype |
|---------|-------|
| レジスト種 | str |
| LWR | int |

---

## 3. CDXML ファイル一覧

CDXML は ChemDraw XML 形式の化学構造ファイル。主要 XML 要素: `<fragment>`（分子構造）, `<n>`（原子・Element属性）, `<b>`（結合）, `<t>`（テキストラベル）。

### 3-1. 入力マスタ（ルート 5 件）

| ファイル名 | 材料カテゴリ | IDプレフィックス | 定義分子数（実測） | 使用済み分子数 | 備考 |
|----------|-----------|-------------|----------------|------------|------|
| `Solvent.cdxml` | 溶剤 | S | **3**（PGME, PGMEA, GBL） | 2（S-1, S-2） | **GBL は未使用**（ID 未付与） |
| `Additive.cdxml` | 添加剤 | D | 2（PGME構造混入, Acetic acid） | 1（D-1） | **PGME構造の混入あり**（意図不明・ID未付与） |
| `Monomer.cdxml` | モノマー | M | 2（HydroxyStyrene, Styrene） | 2（M-1, M-2） | 全使用 |
| `PAG.cdxml` | 光酸発生剤 | A | 4（GreatPAG, GoodPAG, BadPAG + 1） | 3（A-1〜A-3） | 4番目は未使用 |
| `PDB.cdxml` | 塩基性化合物 | D | 4（GreatPDB, GoodPDB, BadPDB + 1） | 1（B-1） | 3種は未使用 |

> **重要**: 入力 CDXML マスタには「定義されているが一度も使われていない材料」が存在する。これらは候補材料として登録されていると推定されるが、業務上の扱いは SP-004 で確認が必要。

---

### 3-2. 出力（Result_260202/ 5 件）

| ファイル名 | 内容 | 備考 |
|----------|------|------|
| `Solvent_ID.cdxml` | 使用済み溶剤（S-1, S-2）の構造＋ID付きラベル | GBL は含まれない |
| `Additive_ID.cdxml` | 使用済み添加剤（D-1）の構造＋ID付きラベル | |
| `Monomer_ID.cdxml` | 使用済みモノマー（M-1, M-2）の構造＋ID付きラベル | |
| `PAG_ID.cdxml` | 使用済みPAG（A-1〜A-3）の構造＋ID付きラベル | |
| `PDB_ID.cdxml` | 使用済みPDB（B-1）の構造＋ID付きラベル | |

---

## 4. JSON ファイル

### 4-1. Intermediate/id_registry.json

| 項目 | 値 |
|------|-----|
| 役割 | 全材料カテゴリの化合物ID採番台帳 |
| 生成タイミング | 初回実行時生成・以降は追記（累積更新型） |
| 信頼度 | High |

**フィールド構成:**
```json
{
  "カテゴリ名": {
    "SMILES文字列 or 組成シグネチャ": "ID文字列"
  }
}
```

**カテゴリとIDプレフィックスの対応:**

| カテゴリ | IDプレフィックス | キーの形式 | コード定数（ids.py） |
|---------|-------------|---------|----------------|
| Polymer | P | 組成シグネチャ（例: `HydroxyStyrene=70;Styrene=30`） | `ID_PREFIX_MAP["Polymer"] = "P"` |
| Monomer | M | 正規化SMILES | `ID_PREFIX_MAP["Monomer"] = "M"` |
| FPolymer | F | 組成シグネチャ | `ID_PREFIX_MAP["FPolymer"] = "F"` |
| PAG | A | 正規化SMILES | `ID_PREFIX_MAP["PAG"] = "A"` |
| PDB | B | 正規化SMILES | `ID_PREFIX_MAP["PDB"] = "B"` |
| Solvent | S | 正規化SMILES | `ID_PREFIX_MAP["Solvent"] = "S"` |
| Additive | D | 正規化SMILES | `ID_PREFIX_MAP["Additive"] = "D"` |

**現在登録済みの具体値（2026-03-02 実測）:**

| カテゴリ | ID | 化合物（SMILES / シグネチャ） |
|---------|-----|--------------------------|
| Polymer | P-1 | HydroxyStyrene=70;Styrene=30 |
| Monomer | M-1 | C=Cc1ccc(O)cc1（HydroxyStyrene） |
| Monomer | M-2 | C=Cc1ccccc1（Styrene） |
| FPolymer | F-1 | Styrene=100 |
| PAG | A-1 | CCC(F)(F)C(F)(F)S(=O)(=O)[O-].c1ccc([S+](c2ccccc2)c2ccccc2)cc1 |
| PAG | A-2 | CCC(F)(F)C(F)(F)S(=O)(=O)[O-].Fc1ccc([S+](c2ccc(F)cc2)c2ccc(F)cc2)cc1 |
| PAG | A-3 | CCC(F)(F)C(F)(F)S(=O)(=O)[O-].Cc1ccc([S+](c2ccc(C)cc2)c2ccc(C)cc2)cc1 |
| PDB | B-1 | CCC(F)(F)C(F)(F)C(=O)[O-].c1ccc([S+](c2ccccc2)c2ccccc2)cc1 |
| Solvent | S-1 | COCC(C)O（PGME） |
| Solvent | S-2 | COCC(C)OC(C)=O（PGMEA） |
| Additive | D-1 | CC(=O)O（Acetic acid） |

---

## 5. 既知の不確実性・要確認事項

| # | 事項 | 重要度 | 確認先 |
|---|------|--------|--------|
| 1 | 入力 CDXML マスタに未使用材料が存在する（GBL, PDB3種 等）。「候補として登録されたが未使用」か「削除忘れ」かが不明 | High | SP-004（業務ロジック抽出）でユーザーに確認 |
| 2 | `Additive.cdxml` に PGME の化学構造が混入している。意図的（比較目的）か誤り（コピーミス）かが不明 | Medium | SP-004 で確認 |
| 3 | `mixture_*_components.csv` はセッション毎に累積生成されるが、削除ポリシーが存在しない | Low | SP-004 で運用ルールを確認 |
| 4 | `Type` / `Category` 列のコード値が文字列リテラルのみで管理（Enum定数なし） | Medium | SP-004 でコードの利用箇所を確認 |
