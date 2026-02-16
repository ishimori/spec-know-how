# ドキュメントマップ

> CLAUDE.md から切り出した参照用カタログ。必要時に読み込むこと。

## 移行計画

| ファイル | 内容 |
|---------|------|
| `doc/移行検討/00_検討経緯まとめ.md` | 移行判断の経緯（Streamlit→React+FastAPI） |
| `doc/移行検討/01_高信頼リエンジニアリング基本方針.md` | 品質保証の基本方針 |
| `doc/移行検討/02_移行計画書.md` | 5フェーズの移行計画 |

## DD設計書

- **アクティブ**: `doc/DD/`
- **アーカイブ済み**: `doc/archived/DD/`
- 一覧は `/dd list` スキルで取得可能

## 仕様書（`doc/spec/`）

- **業務ロジック**: `doc/spec/business-logic/` — 残業・歩合・売上計算・勤怠バリデーション等
  - 仕様書一覧・依存グラフ・テンプレート: `doc/spec/business-logic/README.md`
- **DB設計**: `doc/spec/database/` — 新スキーマDDL・移行マッピング・ER図・インデックス設計
- **マスタデータ**: `doc/spec/master-data/` — 店舗・役職・給与体系等12テーブル + コード値9ビュー + 社員マスタ（匿名化）
- **UI/UXデザイン**: `doc/spec/ui-design-guideline.md` — カラー・タイポグラフィ・コンポーネント・レイアウト規約
- **画面仕様**: `doc/spec/screens/` — 画面ごとの構成・項目定義・操作仕様
  - 一覧・ナビ構造・運用ルール: `doc/spec/screens/README.md`
- **インフラ・非機能要件**: `doc/spec/infrastructure/` — ログ基盤・セキュリティ等のシステム基盤仕様
  - 一覧: `doc/spec/infrastructure/README.md`

## 発表資料（`doc/presentation/`）

AI駆動リエンジニアリング手法のまとめ

- **README**: `doc/presentation/README.md` — 資料構成と想定読者
- **01 背景と課題**: `doc/presentation/01_background.md`
- **02 手法**: `doc/presentation/02_methodology.md` — コード→仕様→QAパイプラインの全体像
- **03 Phase 1 成果**: `doc/presentation/03_current_results.md` — 仕様抽出とQA基盤の実績
- **04 Phase 2 以降**: `doc/presentation/04_future_results.md`（枠のみ）
- **05 気付きと教訓**: `doc/presentation/05_lessons_learned.md`（随時追記中）

## カタログ（`doc/catalog/`）

移行元から取り込んだ既存ドキュメント

- **画面一覧**: `doc/catalog/screens/index.md`（59画面、[VERIFIED]）
- **テーブル定義**: `doc/catalog/database/tables.md`（37テーブル、DDL検証済み[VERIFIED]）
- **ビュー定義**: `doc/catalog/database/views.md`（16ビュー、[DRAFT]）
- **用語集**: `doc/catalog/glossary.md`（[DRAFT]）
- 信頼度ラベルの説明: `doc/catalog/README.md`
