# Changelog

spec-know-how のバージョン履歴。[Semantic Versioning](https://semver.org/) に準拠。

## [v0.2.0] — 2026-02-16

### DD ポートフォリオ一括作成モデルへの転換

v0.1 の「会話ベース Phase 0〜6 進行」から、**起動時に 12 DD を一括作成し、DD の中で判断する** モデルに転換。

#### 追加

- **DD テンプレート群**: `templates/spec-know-how/`（12 種）
  - `portfolio_index.md` — 全 DD の進捗・依存関係ダッシュボード
  - `stage1_quantitative_inventory.md` — コードベースの定量棚卸し
  - `stage2_analysis_scope.md` — 広さ・深さ・抽出戦略の決定
  - `stage3_fr_extraction.md` — FR（業務ロジック）仕様抽出
  - `stage3_dataflow_extraction.md` — データフロー抽出
  - `stage3_state_management.md` — 状態管理（セッション・権限）抽出
  - `stage3_error_handling.md` — エラー処理抽出
  - `stage4_machine_tools.md` — 機械抽出ツール設計・作成
  - `stage5_final_spec.md` — 最終仕様（Stage 3 + 4 突合）
  - `post_migration_design.md` — 移行先アーキテクチャ設計
  - `post_nfr_review.md` — NFR 総合レビュー
  - `post_ui_requirements.md` — 業務アプリ UI 要件
- **references/design/02_workflow-v0.2.md** — DD ポートフォリオワークフロー設計書

#### 変更

- **SKILL.md**: Phase 0〜6 モデルから DD ポートフォリオモデルに全面書き換え
  - 起動プロトコル（パス受領 → 全 DD 一括作成 → Stage 1 即時開始）
  - 依存関係ベースの実行順序（Gate 方式廃止）
  - スキップ判定の 2 パス方式（作成時 + 実行時）
  - Stage 4 スキップ時の信頼度上限制約（Medium）
- **IMPORT.md**: テンプレートコピー手順を追加、フォルダ構造に `doc/templates/spec-know-how/` を追加
- **setup/SKILL.md**: テンプレートコピー手順を追加、完了報告を更新

#### 設計根拠

1. 会話ベースの Phase 進行は LLM の回答が不安定で確実性が低い
2. DD を先に全部作ることで全体像が最初から見える
3. 各 DD 内でスキップ判断を記録することで判断の痕跡が残る
4. 依存関係ベースにすることで並列実行が自然に可能

### 動作確認済み dd-know-how バージョン

- コミット: 56a51bf

---

## [v0.1.0] — 2026-02-16

### 初期リリース

`housing-e-kintai-next` プロジェクトで実践した仕様抽出ワークフローを汎用スキルとして抽象化。

#### 追加

- **SKILL.md**: Phase 0〜6 のオーケストレーション定義
  - 信頼度 4 段階（High/Medium/Low/Conflicting）
  - QA 信頼度 ≦ 仕様信頼度ルール
  - stale フラグ方式による鮮度管理
  - Gate 0〜5 のフェーズ完了判定
  - Definition of Done（6 項目）
- **IMPORT.md**: 別プロジェクトへの導入手順（dd-know-how 自動検出含む）
- **CLAUDE.md**: AI 向けプロジェクト設定ファイル
- **README.md**: プロジェクト概要・導入方法
- **references/**: 参照資料（5 カテゴリ, 19 ファイル）
  - `design/` — 設計書 5 ファイル
  - `examples/` — DD-002, DD-003（実行例）
  - `methodology/` — 方法論・教訓
  - `skills/` — 実装済みスキル 6 種
  - `background/` — 背景・思想・テンプレート

#### 設計書に反映済みの重要決定事項

1. 信頼度は 4 段階（Conflicting はブロッキング）
2. QA 信頼度 ≦ 仕様信頼度
3. 信頼度の降格パス（stale マーク → 再評価）
4. NFR 論理幻覚ゲート（Phase 2.5）
5. stale フラグ方式（即時再生成ではなくマーキング優先）
6. Phase 6 ポートフォリオ完了基準
