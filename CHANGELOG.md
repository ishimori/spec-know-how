# Changelog

spec-know-how のバージョン履歴。[Semantic Versioning](https://semver.org/) に準拠。

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
