# spec-know-how プロジェクト設定

## 概要

このリポジトリは **spec-know-how** スキルの定義と参照資料を管理しています。
レガシーコードベースから仕様を抽出・検証し、信頼度付きの知識基盤を構築するオーケストレーションスキルです。

## 前提条件

**dd-know-how** が必須依存です。spec-know-how は DD を起票・管理するオーケストレーターであり、DD の実体管理は dd-know-how が担います。導入先プロジェクトには dd-know-how（Level 2 以上）が先にセットアップされている必要があります。

## リポジトリ構造

| パス | 用途 |
|------|------|
| `SKILL.md` | スキル定義本体。Phase 0〜6 のワークフロー、信頼度ルール、Gate 定義 |
| `IMPORT.md` | 別プロジェクトへの導入手順 |
| `CHANGELOG.md` | バージョン履歴 |
| `references/design/` | 設計書（ベース思想、ワークフロー、DD連携、チェックリスト、実行手順） |
| `references/examples/` | 実行例（DD-002: ドキュメント棚卸し、DD-003: 業務ロジック仕様抽出） |
| `references/methodology/` | 方法論（5ステップ抽出プロセス）、教訓（44DD以上の経験） |
| `references/skills/` | 実装済みスキル例（verify, qa, workflow, dd, review, review-spec） |
| `references/background/` | 背景・思想（意思決定記録、Document-First/Verify-First の根拠） |


## 利用可能なスキル

### セットアップ
- `/setup <対象プロジェクトのパス>` — 対象プロジェクトに spec-know-how + dd-know-how を導入

## 設計判断の記録

### 信頼度は 4 段階

`High` / `Medium` / `Low` / `Conflicting`。`Conflicting` はブロッキング状態であり、解消するまで次フェーズに進めない。

### QA 信頼度 ≦ 仕様信頼度

QA 回答の信頼度は元の仕様項目の信頼度を超えない。

### stale フラグ方式

コード変更時は即時再生成ではなく stale マーク → 再評価。リリース前 Gate で stale が 0 件であることを完了条件とする。

## メンテナンス方針

- 設計書の変更は `references/design/` を更新し、`SKILL.md` に反映する
- バージョンアップ時は `CHANGELOG.md` に記録する
- 設計書の正は `references/design/`。変更時は `SKILL.md` と `CHANGELOG.md` にも反映する
