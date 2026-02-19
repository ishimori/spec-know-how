# spec-know-how プロジェクト設定

## 概要

このリポジトリは **spec-know-how** の参考書コンテンツを管理しています。
レガシーコードベースから仕様を抽出する手順・ゲートチェックリスト・QAスキルの作り方を提供します。

自動化スキルではなく、人間が判断・実行するための参考資料です。

## リポジトリ構造

| パス | 用途 |
|------|------|
| `GUIDE.md` | 仕様抽出の手順書（Step 1〜6） |
| `CHANGELOG.md` | バージョン履歴 |
| `gates/` | ゲートチェックリスト（6種） |
| `how-to/` | QAスキルの作り方など |
| `references/design/` | 基本概念（FR/NFR/信頼度）、チェックリスト素材 |
| `references/examples/` | 実行例（DD-002: ドキュメント棚卸し、DD-003: 業務ロジック仕様抽出） |
| `references/methodology/` | 教訓（44DD以上の経験） |
| `references/skills/` | スキル実装例（qa, verify） |

## 信頼度の 4 段階

| 信頼度 | 意味 |
|--------|------|
| `High` | コードと照合済み、根拠明確 |
| `Medium` | 一部照合済み、一部推測 |
| `Low` | 根拠不足、未検証 |
| `Conflicting` | コードとドキュメントで矛盾（解消が必要） |

## メンテナンス方針

- バージョンアップ時は `CHANGELOG.md` に記録する
- ゲートや手順の改善は実績から得た教訓を `references/methodology/05_lessons_learned.md` に追記し、`GUIDE.md` や `gates/` に反映する
