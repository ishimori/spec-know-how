# business_rules.md — 業務ロジック仕様書（旧システム事実）

> **信頼度**: [VERIFIED] — コードと照合済み（SP-004-01 調査）
> **対象システム**: Trial-20260302（JupyterLab + ipywidgets）
> **最終更新**: 2026-03-02

このファイルは旧システムのバリデーション・計算ロジック・フロー制御ルールを記録する。
新システムの設計判断は書かない。

---

## BR 採番ルール

- `BR-{3桁連番}` 形式（例: BR-001）
- カテゴリ分類: 「計算」「バリデーション」「フロー制御」

---

## 1. 物性計算ロジック

### BR-001: Solvent PHR 自動計算（TSC 逆算）

| 項目 | 内容 |
|------|------|
| ルール名 | Solvent PHR 自動計算（TSC 逆算） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/ui.py:618-636` |

**計算式**:
```
solid_mass = Σ(Polymer PHR + PAG PHR + PDB PHR + Additive PHR)  # Solvent を除くすべての成分 PHR 合計
total_sol = solid_mass × (100 - tsc%) / tsc%                     # TSC [%] から総溶剤量を逆算
amt_i = total_sol × (rat_i / Σrat)                               # 各溶剤に比率配分
```

**丸め処理**: `int(val/10 + 0.5) * 10` — 10の倍数に四捨五入（例: 53→50, 55→60）

**適用条件**: `solid_mass > 0` かつ `0 < tsc% < 100`
- solid_mass = 0 の場合: 溶剤 PHR は全て 0 にリセット
- tsc% が 0 または 100 以上の場合: 計算スキップ

**トリガー**: Polymer/PAG/PDB/Additive PHR の変更時、または TSC 入力値の変更時に即時実行

---

### BR-002: Polymer PHR 補完（主成分自動決定）

| 項目 | 内容 |
|------|------|
| ルール名 | Polymer PHR 補完（主成分自動決定） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/ui.py:497-527` |

**計算式**:
```
slot0_phr = max(0, polymer_total_phr - (slot1_phr + slot2_phr))
```

**定数**: `polymer_total_phr = 100`（DEFAULT_POLYMER_TOTAL_PHR。起動パラメータで変更可）

**制約**:
- slot0（最初の Polymer スロット）の PHR は **常に自動計算**（ユーザーが直接入力不可、disabled）
- slot1/slot2 の PHR を合計が 100 を超えるように入力しても slot0 は 0 になる（負にならない）
- Polymer スロットを何も選択していない場合（slot0 の DD が空）: slot0_phr = 0

**トリガー**: slot1/slot2 の PHR 変更時、または slot0/slot1/slot2 の DD 選択変更時に即時実行

---

### BR-003: ポリマー→モノマー展開（mol比→重量比変換）

| 項目 | 内容 |
|------|------|
| ルール名 | Polymer/FPolymer → モノマー展開（mol比→重量比変換） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/properties.py:46-130` |

**処理フロー**:
1. Polymer 総 PHR を `polymer_total_phr(=100)` に正規化: `poly_scale = 100 / Σ(Polymer PHR)`
2. シグネチャ `"MonA=50;MonB=50"` → `[(MonA, 50), (MonB, 50)]` にパース
3. 各モノマーの重量比: `W_i = mol_ratio_i × Mw_i`
4. actual_phr を重量比で分配: `mon_phr_i = actual_phr × (W_i / ΣW)`

**例外**:
- Mw = 0 のモノマー: 重量部 = 0（計算に寄与しない）
- シグネチャが空またはパース失敗: そのエントリはスキップ

**FPolymer の扱い**: Polymer と同じロジック。`category` は "FPolymer" だが、展開後のモノマーは `"Monomer"` カテゴリとして登録される

---

### BR-004: k / OP / MOP 計算（重量加重平均）

| 項目 | 内容 |
|------|------|
| ルール名 | k（吸光係数）/ OP / MOP 計算（重量加重平均） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/properties.py:172-183` |

**計算式**:
```
k = Σ(phr_i × k_i) / Σphr_i   # OP, MOP も同様
```

**対象**: 固形分のみ（Solvent は除外）
**例外**: 固形分合計 = 0 の場合は `None` を返す（UI では `"-"` 表示）

---

### BR-005: LogP 計算（体積加重平均）

| 項目 | 内容 |
|------|------|
| ルール名 | LogP 計算（体積加重平均） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/properties.py:185-194` |

**計算式**:
```
moles_i  = phr_i / Mw_i         # Mw = 0 なら moles = 0
volume_i = moles_i × vdw_i      # vdw: van der Waals 体積
LogP     = Σ(volume_i × logp_i) / Σvolume_i
```

**対象**: 固形分のみ（Solvent は除外）
**例外**: 総体積 = 0 の場合は `None` を返す

---

### BR-006: PDB/PAG モル比計算（p_p 値）

| 項目 | 内容 |
|------|------|
| ルール名 | PDB/PAG モル比計算 |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/properties.py:12,200-227` |

**定数**: `_SUFFIX_RE = re.compile(r'_(\d*)(pag|pdb)(?=[_\-\s]|$)', flags=re.IGNORECASE)`

**判定優先順位**:
1. **名前ベース（優先）**: 分子名のサフィックスで PAG/PDB 係数を判定
   - `_2pag` → pag_factor = 2; `_pag`（数字なし）→ pag_factor = 1
   - `_1pdb` → pdb_factor = 1; `_pdb`（数字なし）→ pdb_factor = 1
2. **カテゴリベース（フォールバック）**: 名前判定で両方 0 の場合のみ
   - category = `"pag"` → pag_factor = 1
   - category ∈ `{"pdb", "quencher", "base"}` → pdb_factor = 1

**計算式**:
```
p_p = total_pdb_moles / total_pag_moles   # PAG モル数 = 0 なら 0.0 を返す
```

**対象**: 全成分（Solvent 含む）

---

### BR-007: TSC 計算（固形分濃度）

| 項目 | 内容 |
|------|------|
| ルール名 | TSC 計算（Total Solid Content） |
| カテゴリ | 計算 |
| 信頼度 | High |
| コード根拠 | `app_core/properties.py:229-233` |

**計算式**:
```
TSC = total_solid_phr / (total_solid_phr + total_solvent_phr)  # 0〜1 の比率
```

**UI 表示**: `TSC × 100` [%]（例: 0.05 → 5%）

> ⚠️ **TSC の二重化（要確認）**: `_recalc_solvent()`（BR-001）は TSC を入力として溶剤量を逆算する。`compute_properties()`（BR-007）は現在の PHR 状態から TSC を算出する。丸め処理（10の倍数丸め、BR-001）により、BR-001 の入力 TSC と BR-007 の計算 TSC は厳密に一致しない可能性がある。新システムでは TSC を Single Source of Truth として一元管理することを推奨。

---

## 2. ID管理・永続化ロジック

### BR-008: ポリマーシグネチャ形式

| 項目 | 内容 |
|------|------|
| ルール名 | ポリマーシグネチャ形式 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ids.py:28-49` |

**形式**: `"MonomerA=50;MonomerB=50"` — `;` 区切り、`名前=整数mol%`

**生成規則**:
- 入力: 名前リスト（`/` 区切り）、比率リスト（`/` 区切り）
- 比率: `int(round(float(val)))` に変換
- 旧データの `/` 区切りシグネチャにもフォールバック対応（パース時）

---

### BR-009: ID 発番ルール

| 項目 | 内容 |
|------|------|
| ルール名 | ID 連番発番 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ids.py:89-125` |

**ID プレフィックス一覧**:

| カテゴリ | プレフィックス | 例 |
|---------|-------------|-----|
| Resist | R | R-1, R-2, ... |
| Polymer | P | P-1, P-2, ... |
| Monomer | M | M-1, M-2, ... |
| FPolymer | F | F-1, F-2, ... |
| PAG | A | A-1, A-2, ... |
| PDB | B | B-1, B-2, ... |
| Solvent | S | S-1, S-2, ... |
| Additive | D | D-1, D-2, ... |

**発番ルール**: 既存 ID の末尾数字の最大値 + 1（`{prefix}-{max_n+1}` 形式）

**重複チェック**: `id_registry.json` のシグネチャ/SMILES をキーに既存照合。既存なら既存 ID を返す（再登録しない）

**注意**: FPolymer を構成するモノマーは **Polymer のモノマーと同じ M-{n} 連番を共有**（`ids.py:335-338`）

---

### BR-010: canonical SMILES 正規化

| 項目 | 内容 |
|------|------|
| ルール名 | canonical SMILES 正規化 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/util.py:31-43`, `app_core/ids.py:109` |

**規則**:
- RDKit `Chem.MolToSmiles(mol, isomericSmiles=True)` で正規化（立体異性を含む）
- 無効な SMILES や空文字は **元の文字列をそのまま返す**（例外を発生させない）
- **Polymer/FPolymer のシグネチャには適用しない**（シグネチャは `名前=比率` 形式で SMILES ではないため）

---

### BR-011: PolymerDefinitions CSV 追記ルール

| 項目 | 内容 |
|------|------|
| ルール名 | polymer_definitions.csv 追記・重複抑制 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ids.py:163-191`, `app_core/ui.py:374-386` |

**規則**:
- Mw / Mw_Mn が既存値（最新行）と同一なら追記しない（浮動小数点比較: `abs(prev - curr) < 1e-12`）
- 追記方式: 同一 ID で複数行ある場合は**最後の行が有効**（`load_latest_map()` は後勝ち）

---

## 3. カタログ構築ロジック

### BR-012: CDXML → カテゴリマッピング

| 項目 | 内容 |
|------|------|
| ルール名 | カタログ構築（ファイル名キーワードでカテゴリ決定） |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/util.py:75-111` |

**`DEFAULT_CANON_ORDER`** (`ui.py:36-42`):

| カテゴリ | キーワード | 例となるファイル名 |
|---------|----------|-----------------|
| Monomer | `monomer` | `Monomer.cdxml` |
| PAG | `pag` | `PAG.cdxml` |
| PDB | `pdb` | `PDB.cdxml` |
| Solvent | `solvent` | `Solvent.cdxml` |
| Additive | `additive` | `Additive.cdxml` |

**マッチングロジック**: ファイル stem を小文字化し、キーワードが `in` 演算子で含まれるかどうかで判定（例: `"Monomer.cdxml"` → `"monomer"` → `Monomer` カテゴリ）

**同一カテゴリ複数ファイル**: concat して `Name` 列で dedup（`keep="first"`）

---

## 4. UI フロー制御ロジック

### BR-013: モノマースロット連鎖ロック

| 項目 | 内容 |
|------|------|
| ルール名 | ポリマー定義モノマースロットの連鎖ロック |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ui.py:504-527` |

**ルール**:
- slot0 の DD が空（未選択）→ slot1 の DD が disabled（選択不可）
- slot1 の DD が空（未選択）→ slot2 の DD が disabled（選択不可）
- 各スロットで DD を空に戻した場合、そのスロットの PHR = 0 にリセット
- slot0 の PHR は常に disabled（BR-002 の自動計算で決まる）
- slot1/2 の PHR は DD 選択後のみ有効（DD 未選択なら disabled）

---

### BR-014: 選択時PHRゼロリセット（Generic Card）

| 項目 | 内容 |
|------|------|
| ルール名 | 成分選択時 PHR ゼロリセット（PAG/PDB/Additive） |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ui.py:574-582` |

**ルール**: DD（成分名）を変更した瞬間、AMT（PHR）を **必ず 0 にリセット**。前の PHR 値が残って物性が意図せず変わるのを防ぐ。

---

### BR-015: Save 時のデータ構造（CSV Long 形式）

| 項目 | 内容 |
|------|------|
| ルール名 | mixture_*_components.csv の形式 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/ui.py:1005-1022` |

**列**: `Resist_ID` / `Type` / `Category` / `Name` / `Value`

**Type 列の値（リテラル文字列、Enum 管理なし）**:

| Type 値 | 意味 | Category 値の例 |
|--------|------|---------------|
| `"Meta"` | メタデータ行 | `"Label"`, `"Memo"`, `"Solvent_Ratios"` |
| `"Component"` | 成分行（PHR 値） | `"Polymer"`, `"PAG"`, `"PDB"`, `"Solvent"`, `"Additive"` |
| `"Property"` | 物性値行 | `"Custom"`, `"Calculated"` |

**特別な Resist_ID**: `"GLOBAL"` — Label 名定義行（全 Resist 共通）

**ファイル命名**: `mixture_{YYYYMMDD_HHMMSS}_components.csv`

> ⚠️ **Type/Category 列はリテラル管理（SP-002 引き継ぎ事項#3）**: スペルミスがデータ欠落として現れる。新システムでは Enum 管理を推奨。

---

### BR-016: DescriptorStore グローバル検索フォールバック

| 項目 | 内容 |
|------|------|
| ルール名 | 記述子取得のカテゴリ横断検索 |
| カテゴリ | フロー制御 |
| 信頼度 | High |
| コード根拠 | `app_core/descriptors.py:43-74` |

**優先順位**:
1. 指定カテゴリで名前を検索
2. 見つからない場合: 全カテゴリを順次検索
3. それでも見つからず名前に SMILES 文字（`=` `#` `c` `C`）が含まれる場合: 名前そのものを SMILES として解釈

---

## 5. 暗黙ルール候補（ドメインエキスパート確認が必要）

| # | ルール概要 | 確認すべき相手 |
|---|----------|-------------|
| 1 | Solvent PHR を 10 の倍数に丸める化学的・実験的根拠（試薬計量精度？） | 実験担当者 |
| 2 | `polymer_total_phr = 100` の物理的意味・変更ケースの有無 | 実験担当者 |
| 3 | 未使用 CDXML 材料（GBL、PDB B-1 以外）の業務上の扱い（候補登録 vs 削除忘れ） | データ管理者 |
| 4 | `Additive.cdxml` への PGME 混入の意図（比較用途 vs コピーミス） | データ管理者 |
| 5 | Polymer と FPolymer の業務上の区別（フォトレジスト化学における機能の違い） | 実験担当者 |
| 6 | ロック後に Mw / Mw_Mn のみ変更可能な設計の意図（仕様 vs 設計バグ） | 開発者 |
