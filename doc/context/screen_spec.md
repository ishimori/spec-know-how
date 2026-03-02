# screen_spec.md — 画面仕様書（旧システム事実）

> **ドキュメントラベル**: [DRAFT]
> **対象システム**: Trial-20260302（`C:\tmp\260202_app_with_data`）
> **作成日**: 2026-03-02
> **調査元**: SP-003-01, SP-003-02, SP-003-03
> **書き分けルール**: 旧システムの事実のみ記録。新システム設計は書かない。

---

## 1. 画面構成の概要

このアプリは JupyterLab 上で動作する **単一 Python プロセス**。
URL ルーティング・ページ遷移は存在せず、**タブ切り替え**が唯一の「画面遷移」。
すべての画面状態は `App` オブジェクト（`app_core/ui.py`）がメモリに保持する。

```
App（単一インスタンス）
└─ トップ Tab（self.tabs）
   ├─ [0] Formulation
   │   └─ サブ Tab（self.formulation_tabs）
   │       ├─ [0] Polymer        — ポリマー定義エディタ
   │       ├─ [1] F-Polymer      — フッ素ポリマー定義エディタ
   │       └─ [2] Resist         — レジスト配合設計
   ├─ [1] Table
   │   └─ サブ Tab（self.bottom_tabs）
   │       ├─ [0] Resist Table
   │       ├─ [1] Performance Table
   │       ├─ [2] Polymer Table
   │       └─ [3] F-Polymer Table
   └─ [2] Structures
       └─ 動的サブ Tab（render_registry() 生成）
           ├─ PAG / PDB / Solvent / Additive / Monomer
           └─ FPolymer_Monomer / Polymer / FPolymer（カテゴリ数に依存）
```

---

## 2. 画面一覧

### 2-1. Formulation > Polymer / F-Polymer（定義エディタ）

| 項目 | 内容 |
|------|------|
| **何ができるか** | ポリマー（または F-Polymer）の構成モノマー・mol% を定義し、ID を付与して登録する |
| **入力** | モノマー名（ドロップダウン）× 最大 4 スロット、mol%（IntText）、Mw、Mw/Mn |
| **操作** | Add New / Register（ID 付与）/ Copy / Save Mw |
| **出力・副作用** | `Intermediate/id_registry.json` に PID（Polymer: `P-N`、FPolymer: `M-N` 共通連番）を登録。`Intermediate/polymer_definitions.csv` に定義を追記 |
| **信頼度** | High |
| **実装** | `PolymericDefBuilder`, `PolymericDefRowWidget`（`ui.py:260〜391`） |

### 2-2. Formulation > Resist（配合設計）

| 項目 | 内容 |
|------|------|
| **何ができるか** | レジスト配合を行（R-1, R-2, ...）単位で入力し、リアルタイムで物性を計算・表示する |
| **入力** | 各行: Polymer/FPolymer（登録済み PID から選択）、PAG/PDB/Solvent/Additive（名前・PHR）、TSC%、カスタムフィールド（最大 4 項目）、メモ |
| **自動計算（UI層）** | Solvent PHR 自動計算（TSC% から逆算）、Polymer 主成分 PHR 自動補完 |
| **物性表示（リアルタイム）** | k / LogP / OP / PDB-PAG比 / TSC（`properties.py` で計算） |
| **操作** | Add Row / Delete / Up / Down / Copy / Save |
| **出力・副作用** | Save 時: `Intermediate/mixture_YYYYMMDD_HHMMSS_components.csv` + `result_with_id.csv` |
| **信頼度** | High |
| **実装** | `ResistRowWidget`（`ui.py:392〜697`）、`App.save()`（`ui.py:940`） |

### 2-3. Table（テーブル閲覧・エクスポート）

| 項目 | 内容 |
|------|------|
| **何ができるか** | 配合結果の各テーブルを閲覧し CSV エクスポートする |
| **サブタブ** | Resist Table / Performance Table / Polymer Table / F-Polymer Table |
| **操作** | Refresh Tables（再描画）/ Export Tables（CSV 出力） |
| **出力・副作用** | Export 時: `Result_YYMMDD/resist_table.csv`, `performance_table.csv`, `polymer_table.csv`, `fpolymer_table.csv` |
| **信頼度** | High |
| **実装** | `App.render_all_tables()`, `App.export_tables()`（`ui.py:1339〜1050`） |

### 2-4. Structures（ID-構造一覧・CDXML エクスポート）

| 項目 | 内容 |
|------|------|
| **何ができるか** | 登録済み構造を ID と分子構造画像で一覧し、使用中構造の CDXML を出力する |
| **表示モード** | 「使用中のみ」（現在の Resist 配合で phr > 0 の構造のみ）/ 「全構造」（カタログ + registry 全件） |
| **操作** | モード切り替えボタン 2 つ / Export Structures |
| **出力・副作用** | Export 時: `Result_YYMMDD/*_ID.cdxml`（未使用構造を削除し、名称を ID に置換した CDXML） |
| **信頼度** | High |
| **実装** | `App.render_registry()`（`ui.py:〜2312`）、`App.export_structures()`（`ui.py:1052〜1337`） |

---

## 3. 権限・ロール制御

**なし**（信頼度: High）

JupyterLab の起動権限 = アプリへのアクセス権限。アプリ内に権限分岐は存在しない。
すべての画面・操作が全ユーザーに対して無条件に有効。

---

## 4. 主要操作フロー（Happy Path）

### フロー 1: 初回起動・状態ロード

```
[Notebook セル実行] launch_app("./")
  → App.__init__() — UI 構築・カタログ読み込み
  → app.load_latest() — 最新 mixture_*.csv を自動読み込み
  → Formulation > Resist に配合行を復元
```

### フロー 2: ポリマー定義 → ID 登録

```
Formulation > Polymer タブ
  → モノマー選択（Dropdown × 4）
  → mol% 入力（mol% の合計は第1スロット自動補完）
  → Mw / Mw-Mn 入力
  → Register ボタン
      → polymer_signature 生成（"MonA=70/MonB=30" 形式）
      → IdRegistry.assign() → PID 付与（P-N）
      → id_registry.json 更新
      → polymer_definitions.csv に追記
```

### フロー 3: レジスト配合設計 → 保存

```
Formulation > Resist タブ
  → Add Row で配合行追加
  → Polymer（PID 選択）/ PAG / PDB / Solvent（比率・TSC%入力）/ Additive 入力
      → Solvent PHR 自動計算（observe: TSC% 変化 → _recalc_solvent()）
      → 物性リアルタイム計算（observe: 値変化 → refresh_properties() → compute_properties()）
  → Save ボタン
      → polymer_definitions.csv に Mw 差分追記（_sync_polymer_definitions_from_ui()）
      → mixture_YYYYMMDD_HHMMSS_components.csv 保存
      → result_with_id.csv 生成
```

### フロー 4: テーブル確認 → CSV エクスポート

```
Table タブ → Refresh Tables → 4 テーブル表示
  → Export Tables → Result_YYMMDD/ に 4 CSV 出力
```

### フロー 5: CDXML エクスポート

```
Structures タブ → 使用中のみ モード確認
  → Export Structures
      → 使用中構造（phr > 0）のみ残し未使用 group を削除
      → 名称テキストを ID（P-1, A-1 等）に置換
      → Result_YYMMDD/*_ID.cdxml 出力
```

---

## 5. UI 層に埋め込まれたビジネスロジック（SP-004 仕様化対象）

> SP-003-03 の SP-004 引き継ぎリストを転記。詳細仕様化は SP-004 で行う。

| 関数名 | ファイル:行 | 種別 | 概要 |
|-------|-----------|------|------|
| `_recalc_solvent()` | `ui.py:618` | 業務計算（UI層） | Solvent PHR 自動計算: `total_sol = solid_mass × (100 - tsc) / tsc`、比率で各溶剤に配分 |
| `_update_balance()` | `ui.py:497` | 業務計算（UI層） | Polymer PHR 補完: `rem = max(0, polymer_total_phr - sub_sum)` で主成分 PHR を自動決定 |
| `compute_properties()` | `properties.py:146` | 物性計算（分離済み） | k/LogP/OP/MOP/PDB-PAG ratio/TSC 計算（固形分の重量加重・体積加重平均） |
| `_resolve_composition()` | `properties.py:33` | 物性計算補助（分離済み） | Polymer→モノマー分解・mol比→重量比変換・PHR スケーリング |
| `PolymericDefBuilder.register_row()` | `ui.py:329` | ID管理 | ポリマーシグネチャ生成・ID 付与・モノマー ID 連番管理 |

---

## 6. 永続化ファイルと画面の対応

| ファイル | 生成タイミング | 対応する画面操作 |
|---------|-------------|--------------|
| `Intermediate/id_registry.json` | Polymer/F-Polymer Register ボタン | Formulation > Polymer/F-Polymer |
| `Intermediate/polymer_definitions.csv` | Register + Save Mw + Save（Resist） | Formulation > Polymer/F-Polymer/Resist |
| `Intermediate/mixture_*_components.csv` | Resist の Save ボタン | Formulation > Resist |
| `result_with_id.csv`（DATA_DIR直下） | Resist の Save ボタン（兼: Auto Refresh） | Formulation > Resist |
| `Result_YYMMDD/resist_table.csv` 等 4 件 | Table の Export Tables ボタン | Table |
| `Result_YYMMDD/*_ID.cdxml` | Structures の Export Structures ボタン | Structures |
