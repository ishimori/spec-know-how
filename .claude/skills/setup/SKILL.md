---
name: setup
description: 対象プロジェクトに spec-know-how + dd-know-how を導入する
---

# spec-know-how セットアップコマンド

外部プロジェクトに spec-know-how（+ 必須依存の dd-know-how）を導入するコマンドです。

## 使い方

```
/setup {対象プロジェクトのパス}
```

**例:**
- `/setup /path/to/my-project`
- `/setup ../my-other-project`
- `/setup` （対話でパスを指定）

## 前提条件

このコマンドは **spec-know-how リポジトリ内** で実行します。

## セットアップ手順

### 1. 対象プロジェクトの確認

引数でパスが指定された場合:
- そのパスが存在し、ディレクトリであることを確認
- 確認できない場合はエラーメッセージを表示

引数がない場合:
- ユーザーに対象プロジェクトのパスを質問

### 2. dd-know-how の依存チェック（CRITICAL）

対象プロジェクトに dd-know-how が導入済みか確認:

```bash
ls {対象プロジェクト}/.claude/skills/dd/SKILL.md
ls {対象プロジェクト}/.claude/skills/workflow/SKILL.md
```

#### 導入済みの場合

→ Step 3 へ進む

#### 未導入の場合

dd-know-how リポジトリを探索:

```bash
# 同じ親ディレクトリに dd-know-how があるか探す
ls ../dd-know-how/ 2>/dev/null
# よくあるパスを確認
ls ~/repos/dd-know-how/ 2>/dev/null
ls ~/projects/dd-know-how/ 2>/dev/null
```

**見つかった場合:**

ユーザーに確認:

```
⚠️ dd-know-how が対象プロジェクトに未導入です。
spec-know-how は dd-know-how（Level 2 以上）が必須です。

dd-know-how が {見つかったパス} にあります。
先に dd-know-how を導入しますか？

1. はい — dd-know-how の Level 2 セットアップを実行してから spec-know-how を導入
2. いいえ — spec-know-how のみ導入（DD起票機能は動作しません）
```

「はい」の場合:
1. dd-know-how の IMPORT.md に記載の Level 2 手順を実行:
   - `doc/DD/`, `doc/templates/`, `doc/archived/DD/` フォルダ作成
   - `dd-know-how/templates/dd_template.md` → `{対象}/doc/templates/`
   - `dd-know-how/rules/dd-basic-rules.md` → `{対象}/doc/templates/`
   - `dd-know-how/.claude/skills/dd/SKILL.md` → `{対象}/.claude/skills/dd/`
   - `dd-know-how/.claude/skills/workflow/SKILL.md` → `{対象}/.claude/skills/workflow/`
2. dd-know-how セットアップ完了を確認してから Step 3 へ

**見つからない場合:**

```
⚠️ dd-know-how が対象プロジェクトに未導入です。
spec-know-how は dd-know-how（Level 2 以上）が必須です。

dd-know-how リポジトリが見つかりません。
以下のいずれかを行ってください:

1. dd-know-how リポジトリを取得:
   git clone https://github.com/xxx/dd-know-how.git

2. dd-know-how 内で対象プロジェクトへセットアップ:
   cd dd-know-how && claude
   > /setup {対象プロジェクトのパス}

3. その後、再度 spec-know-how の /setup を実行してください。
```

### 3. 導入レベルの選択

ユーザーに導入レベルを確認:

| レベル | 内容 | 推奨ケース |
|--------|------|------------|
| Level 1（最小） | SKILL.md のみ | まず試したい |
| **Level 2（標準）** | + 設計書 + 実行例 | **通常利用（推奨）** |
| Level 3（フル） | + 方法論・教訓 + スキル例 + 背景資料 | 深い理解が必要 |

### 4. ファイルのコピー

#### Level 1（最小構成）

```
{対象プロジェクト}/
└── .claude/
    └── skills/
        └── spec-know-how/
            └── SKILL.md          # ← spec-know-how/SKILL.md
```

#### Level 2（標準構成）— Level 1 に加えて

```
{対象プロジェクト}/
├── .claude/
│   └── skills/
│       └── spec-know-how/
│           └── SKILL.md
└── doc/
    └── spec-know-how/
        └── references/
            ├── design/           # ← spec-know-how/references/design/
            └── examples/         # ← spec-know-how/references/examples/
```

#### Level 3（フル構成）— Level 2 に加えて

```
{対象プロジェクト}/
└── doc/
    └── spec-know-how/
        └── references/
            ├── design/           # （Level 2 で配置済み）
            ├── examples/         # （Level 2 で配置済み）
            ├── methodology/      # ← spec-know-how/references/methodology/
            ├── skills/           # ← spec-know-how/references/skills/
            └── background/       # ← spec-know-how/references/background/
```

### 5. 仕様抽出の成果物フォルダ作成

```bash
mkdir -p {対象プロジェクト}/doc/spec/business-logic  # FR（機能要件）
mkdir -p {対象プロジェクト}/doc/spec/nfr              # NFR（非機能要件）
mkdir -p {対象プロジェクト}/doc/spec/uxm              # UI操作モデル
```

### 6. CLAUDE.md の更新

対象プロジェクトの CLAUDE.md に以下を追記（重複チェック実施）:

```markdown
## 仕様抽出（spec-know-how）

- **依存**: dd-know-how（Level 2 以上）
- **バージョン**: {spec-know-how の最新コミット SHA 短縮7桁}
- **参照資料**: `doc/spec-know-how/references/`（Level 2 以上で配置）
- **成果物**: `doc/spec/`（business-logic, nfr, uxm）

### 信頼度ルール

| 信頼度 | 定義 | アクション |
|--------|------|-----------|
| High | 機械裏付けあり | そのまま利用可 |
| Medium | 一部推論 | 注意付きで利用可 |
| Low | 根拠不足 | 調査が必要 |
| Conflicting | 矛盾あり | 解消するまで利用不可 |

### QA 応答要件

回答 + 信頼度 + 根拠行 + コミットSHA + 生成日時
```

### 7. バージョン固定

spec-know-how の現在のコミット SHA を取得して記録:

```bash
cd {spec-know-how のパス}
git log --oneline -1
# → 例: 1c7a3a6 v0.1.0: spec-know-how 初期構築
```

CLAUDE.md のバージョン欄に記録する。

### 8. 完了報告

```
✓ spec-know-how のセットアップが完了しました

【依存関係】
- dd-know-how: ✅ 導入済み
- spec-know-how: ✅ 導入済み（{バージョン}）

【配置されたファイル】
- スキル: .claude/skills/spec-know-how/SKILL.md
- 参照資料: doc/spec-know-how/references/（Level 2 以上）
- 成果物フォルダ: doc/spec/（business-logic, nfr, uxm）

【次のステップ】
1. SKILL.md の Phase 0（ヒアリング）を開始してください
2. 移行元システムの情報を準備してください:
   - 言語/フレームワーク/DB/構成
   - 主対象ドメイン
   - 重要NFR
   - 既存ドキュメントの所在
```

## 注意事項

- 既存ファイルがある場合は上書き前に確認
- CLAUDE.md への追記は重複チェック（`spec-know-how` の文字列検索）を行う
- dd-know-how の自動導入は Level 2 固定（Level 3 は dd-know-how 側で手動昇格）
