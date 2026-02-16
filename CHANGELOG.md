# Changelog

spec-know-how のバージョン履歴。[Semantic Versioning](https://semver.org/) に準拠。

## [v0.2.0] — 2026-02-16

### 用語日本語化 + 規模別段階化戦略の追加

#### 改善内容

1. **用語の日本語化（表示レベル）**
   - `FR` → `機能要件（FR）`
   - `NFR` → `非機能要件（NFR）`
   - `UXM` → `UI操作モデル（UXM）`
   - 信頼度ラベルも日本語併記化（検討中）

2. **プロジェクト規模による自動段階化**
   - Phase 0 に「コード規模」「ドメイン複雑性」の判定を追加
   - 規模別進め方の選択（即座生成 / 標準進行 / 段階的実行 / 全体DD+分割）
   - Gate 0・1 の判定基準を規模別に拡張

3. **生成物の出力先ルール明示**
   - 分析対象プロジェクト配下ではなく、実行元プロジェクト配下に生成物を作成
   - 複数プロジェクトの仕様を一元管理可能に

#### ファイル変更

- **references/design/01_foundation.md**: 用語定義を日本語併記化
- **references/design/02_workflow-v0.1.md**: Phase 0・1 に規模判定と段階的DD戦略を追加
- **references/design/04_alignment-checklist.md**: 出力先ルールを合意チェックリストに追加
- **SKILL.md**: 上記を全て反映

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
