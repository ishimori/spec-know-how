---
name: verify
description: 既存コードから事実を機械的に抽出・検証する
---

# Verify スキル

Pythonスクリプトによる静的解析を行い、**「事実（Ground Truth）」** を抽出します。
LLMのハルシネーションを防ぐため、仕様書作成やコード実装の前に必ず実行し、出力結果を正として扱ってください。

## 基本的な使い方

### 1. 関数・クラス構造の抽出

```bash
/verify functions <ファイルパス>
```

- **用途**: 仕様書作成前、リファクタリング前
- **実行されるコマンド**: `uv run python -m verify.ast_extractor <ファイルパス> --pretty`
- **確認事項**:
    - 関数名、引数、戻り値の型ヒント
    - デコレータの有無（`@trace`, `@st.cache_data` 等）
    - クラス継承関係

### 2. データモデルの抽出

```bash
/verify models <ファイルパス>
```

- **用途**: DB設計、型定義の確認
- **実行されるコマンド**: `uv run python -m verify.model_extractor <ファイルパス> --pretty`
- **確認事項**:
    - `@dataclass` フィールド定義
    - `get_pk_name` (主キー判定)
    - `ClassVar` や `Enum` 定義

### 3. SQLクエリの抽出

```bash
/verify sql <ファイルパス>
```

- **用途**: DB移行、クエリロジックの把握
- **実行されるコマンド**: `uv run python -m verify.sql_extractor <ファイルパス> --pretty`
- **確認事項**:
    - 発行されるSQL文（SELECT/INSERT/UPDATE/DELETE）
    - 依存テーブル
    - パラメータスタイル（named/positional）

## 応用的な使い方（ワークフロー連携）

### 仕様書とのクロスチェック（Cross-Check）

現状は手動での突き合わせを推奨します。

1. `/verify` で事実を抽出
2. 仕様書（Markdown）の内容と比較
3. 差分があれば仕様書を修正（「機械抽出の結果、関数Xが不足していました」と報告）

## トラブルシューティング

- **FileNotFoundError**: パスはプロジェクトルートからの相対パス（例: `src/main.py`）で指定してください。Unix形式（`/`区切り）推奨。
- **ModuleNotFoundError**: `uv run` を忘れていませんか？必ず `uv run` 経由で実行してください。
- **Encoding Error**: 移行元ファイルは `cp932` の可能性がありますが、ツールは `utf-8` を前提としています。文字化け時は手動で変換してください。
