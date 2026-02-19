# Changelog

spec-know-how のバージョン履歴。[Semantic Versioning](https://semver.org/) に準拠。

## [v0.4.0] — 2026-02-19

### 実践マニュアル追加 — 仕様抽出から実装までの包括ガイド

#### 追加

- **manuals/01_仕様書作成マニュアル.md**: コードを書く前の全工程ガイド
  - 5ステップ構成（移行判断 → ドキュメント棚卸し → 業務ロジック抽出 → 設計 → 横断検証）
  - 各ステップにゲート定義・よくある落とし穴を記載
  - アンチパターン6件（AP-1〜AP-6）+ 統合チェックリスト
- **manuals/02_実装マニュアル.md**: コードを書く全工程ガイド
  - 5ステップ構成（垂直スライス → 核機能 → 放射的実装 → 品質ゲート → デプロイ）
  - DA批判レビュー運用ガイド（視点ローテーション・多ラウンドDAパターン）
  - アンチパターン8件（AP-1〜AP-8）+ 統合チェックリスト

#### 変更

- **README.md**: スコープを「仕様抽出」から「仕様抽出 → 実装」全体に拡大
  - manuals/ のナビゲーション追加
  - GUIDE.md と manuals/ の役割分担を明示

#### GUIDE.md と manuals/ の役割分担

- `GUIDE.md` = クイックリファレンス（「何をするか」のチェックリスト、仕様抽出6ステップ）
- `manuals/` = 包括マニュアル（「なぜ・どうやるか」の詳細 + ゲート + アンチパターン、仕様抽出+実装）

---

## [v0.3.0] — 2026-02-19

### スキルから参考書へ転換

自動化オーケストレーションスキルとしての方向性を廃止し、人間が使う参考書・手順書として再構成。

#### 追加

- **GUIDE.md**: 仕様抽出の手順書（Step 1〜6、各ゲートへのリンク付き）
- **gates/**: ゲートチェックリスト 6 種
  - `01_initial_survey.md` — 初回調査
  - `02_database.md` — DB・データモデル
  - `03_screens.md` — 画面・UI
  - `04_business_logic.md` — 業務ロジック
  - `05_nfr.md` — 非機能要件
  - `06_qa_ready.md` — QA 準備完了
- **how-to/qa-skill.md**: プロジェクト固有の QA スキルの作り方（4ステップ）

#### 削除

- **SKILL.md**: 自動化スキル定義（廃止）
- **IMPORT.md**: スキル導入手順（廃止）
- **.claude/**: セットアップスキル（廃止）
- **templates/**: DD テンプレート 12 種（廃止）
- **references/design/**: ワークフロー設計書・DD連携詳細（廃止）
- **references/background/**: 内部経緯・意思決定記録（廃止）
- **references/skills/**: dd/workflow/review/review-spec スキル例（廃止）
- **references/methodology/02_methodology.md**: 自動化前提の方法論（廃止）

#### 変更

- **README.md**: 参考書としての概要・使い方に全面改訂
- **CLAUDE.md**: スキルリポジトリから参考書リポジトリへ変更を反映

#### 転換の背景

複数プロジェクトでの試みにより、レガシーコードベースの多様性（構造・言語・規模）に対して汎用スキルで自動化することは限界があると判断。
自動化ではなく「この手順で進めればいい」を示す参考書・ゲートによる人間の判断支援へ転換。

---

## [v0.2.1] — 2026-02-17

### セットアップドキュメント改善

#### 変更

- **IMPORT.md**:
  - **Step 5**: CLAUDE.md への記載内容を拡充
    - 前提条件（dd-know-how Level 2 以上）の明記
    - 成果物フォルダの詳細説明（business-logic, nfr, uxm, machine-facts）
    - 導入済みスキル一覧表を追加（セットアップ完了の確認用）
  - **検証チェックリスト**: 検査項目を詳細化
    - workflow スキルの確認
    - 成果物フォルダの個別確認
    - Claude Code の再起動確認
  - **トラブルシューティング**: 解決手順を強化
    - スキルが認識されない: Claude Code 完全再起動が必須であることを明記
    - 各問題別の診断コマンド例を追加
    - 成果物フォルダ確認手順を追加

#### 設計根拠

1. 実運用で導入後に CLAUDE.md の更新漏れが発生した
2. セットアップ完了時の確認項目が不足していた
3. トラブルシューティング時の診断手順が曖昧だった

---

## [v0.2.0] — 2026-02-16

### DD ポートフォリオ一括作成モデルへの転換

v0.1 の「会話ベース Phase 0〜6 進行」から、**起動時に 12 DD を一括作成し、DD の中で判断する** モデルに転換。

#### 追加

- **DD テンプレート群**: `templates/spec-know-how/`（12 種）
  - `05_portfolio_index.md` — 全 DD の進捗・依存関係ダッシュボード
  - `10_stage1_quantitative_inventory.md` — コードベースの定量棚卸し
  - `15_stage2_analysis_scope.md` — 広さ・深さ・抽出戦略の決定
  - `20_stage3_fr_extraction.md` — FR（業務ロジック）仕様抽出
  - `25_stage3_dataflow_extraction.md` — データフロー抽出
  - `30_stage3_state_management.md` — 状態管理（セッション・権限）抽出
  - `35_stage3_error_handling.md` — エラー処理抽出
  - `40_stage4_machine_tools.md` — 機械抽出ツール設計・作成
  - `45_stage5_final_spec.md` — 最終仕様（Stage 3 + 4 突合）
  - `50_post_migration_design.md` — 移行先アーキテクチャ設計
  - `55_post_nfr_review.md` — NFR 総合レビュー
  - `60_post_ui_requirements.md` — 業務アプリ UI 要件
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
