# state_transitions.md — 状態遷移仕様書（旧システム事実）

> **信頼度**: [VERIFIED] — コードと照合済み（SP-004-02 調査）
> **対象システム**: Trial-20260302（JupyterLab + ipywidgets）
> **最終更新**: 2026-03-02

このファイルは旧システムのフラグ・ステータスの状態遷移を記録する。
新システムの設計判断は書かない。

---

## 対象の概要

このシステムは DB を持たない（CSV ファイルベース）。
フラグ・ステータス管理の対象は以下の3種類に限られる。

| # | 対象 | 永続化 | 対象基準 |
|---|------|--------|---------|
| 1 | `mixture_*_components.csv` の `Type` 列 | あり（CSV） | 複数値あり（Meta/Component/Property） |
| 2 | `PolymericDefRowWidget.is_locked` | なし（インメモリ） | 複数箇所から書き込みあり |
| 3 | `App.registry_view_mode` | なし（インメモリ） | 複数値あり（used/all_structures） |

---

## 1. mixture_*_components.csv: Type 列

### 値の定義

| Type 値 | 業務的意味 | 発生する行の例 |
|--------|----------|-------------|
| `"Meta"` | メタデータ行（設定情報・構造データ） | Label名定義行、Memo行、Solvent比率JSON行 |
| `"Component"` | 配合成分行（各素材の PHR 値） | Polymer/PAG/PDB/Solvent/Additive の各成分行 |
| `"Property"` | 物性値行（計算済み・手動入力） | k/LogP/OP/p_p/tsc/custom{n} の各値行 |

### 状態遷移

```
（なし）
```

> **注**: Type 列に状態遷移はない。1回の Save 操作で値が確定的に書き込まれ、その後変更されない（CSV は append ではなく新規ファイル作成）。Save ボタンを押すたびに新しいタイムスタンプ付きファイルが生成される。

### 書き込みパス

| Type 値 | 書き込む関数 | 行番号 |
|--------|------------|--------|
| `"Meta"` (Label) | `App._create_records_from_ui()` | `ui.py:1008` |
| `"Component"` | `App._create_records_from_ui()` | `ui.py:1013` |
| `"Property"` (Custom) | `App._create_records_from_ui()` | `ui.py:1015` |
| `"Meta"` (Memo) | `App._create_records_from_ui()` | `ui.py:1016` |
| `"Meta"` (Solvent_Ratios) | `App._create_records_from_ui()` | `ui.py:1018` |
| `"Property"` (Calculated) | `App._create_records_from_ui()` | `ui.py:1020-1021` |

---

## 2. PolymericDefRowWidget.is_locked

### 値の定義

| 値 | 業務的意味 |
|----|----------|
| `False` | 新規行（編集可能状態）。モノマー組成・比率・Mw/Mw_Mn を変更できる |
| `True` | 登録済み行（部分的に編集不可状態）。モノマー組成・比率は変更不可。Mw/Mw_Mn のみ `Save Mw` ボタンで変更可能 |

### 状態遷移図

```
[False（新規）] → Register ボタン押下 → [True（登録済み）]
                                               ↑
[False（ロード）] → from_data() 呼び出し ─────┘

※ True → False の逆遷移は存在しない（ロック解除不可）
```

### 書き込みパス

| 遷移 | 書き込む関数 | 行番号 | トリガー |
|------|------------|--------|---------|
| `False`（初期化） | `__init__()` | `ui.py:167` | 新規行作成時 |
| `True`（Register） | `lock()` ← `on_click_register()` ← Register ボタン | `ui.py:245-258` | ユーザーが Register ボタンを押した時 |
| `True`（ロード） | `lock()` ← `from_data()` ← `load_all()` | `ui.py:243, 300-316` | アプリ起動時・`load_all()` 呼び出し時 |

### 設計バグ候補

| # | 問題の概要 | 重要度 |
|---|----------|--------|
| 1 | ロック後も Mw / Mw_Mn は変更可能（`mw_input` / `pd_input` は `lock()` で disabled にしていない）。これが意図的（Mw は実測値として後から入力できる）かどうかは不明 | Medium |

---

## 3. App.registry_view_mode

### 値の定義

| 値 | 業務的意味 |
|----|----------|
| `"used"` | 「使用中のみ」モード: 現在の配合で PHR > 0 の構造のみ一覧表示 |
| `"all_structures"` | 「全構造」モード: id_registry.json に登録された全構造を表示 |

### 状態遷移図

```
[初期値: "used"] ⇌ "all_structures"
                  ↑↓
             ボタン押下で相互遷移
```

### 書き込みパス

| 遷移先 | 書き込む関数 | 行番号 | トリガー |
|--------|------------|--------|---------|
| `"used"` | `_set_registry_mode("used")` | `ui.py:789-798, 800` | 「使用中のみ」ボタン押下 |
| `"all_structures"` | `_set_registry_mode("all_structures")` | `ui.py:789-798, 801` | 「全構造」ボタン押下 |

> **注**: 永続化されない。セッション再起動（Jupyter カーネル再起動）で常に初期値 `"used"` に戻る。
