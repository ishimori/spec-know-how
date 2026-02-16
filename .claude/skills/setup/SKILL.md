---
name: setup
description: 対象プロジェクトに spec-know-how + dd-know-how を導入する
---

# spec-know-how セットアップコマンド

外部プロジェクトに spec-know-how（+ 必須依存の dd-know-how）を導入するコマンドです。

## GitHub リポジトリ URL（設定）

```
SPEC_KNOW_HOW_REPO=https://github.com/ishimori/spec-know-how.git
DD_KNOW_HOW_REPO=https://github.com/ishimori/dd-know-how.git
```

> URL を変更する場合はここを編集してください。

## 使い方

### パターン A: spec-know-how リポジトリ内から実行

```
/setup {対象プロジェクトのパス}
```

**例:**
- `/setup /path/to/my-project`
- `/setup ../my-other-project`

### パターン B: 対象プロジェクト内から実行（ブートストラップ）

対象プロジェクト内で Claude Code を起動し、以下を依頼:

```
spec-know-how を導入して。
GitHub: https://github.com/ishimori/spec-know-how.git
```

この場合、Claude が以下のブートストラップ手順を実行します:

1. `tmp/_spec-know-how` に clone
2. `tmp/_dd-know-how` に clone
3. 必要ファイルをコピー
4. `tmp/_spec-know-how`, `tmp/_dd-know-how` を削除

> ブートストラップは spec-know-how スキルがなくても実行可能です。
> IMPORT.md の「ブートストラップ（ワンライナー）」セクションに手順があります。

## セットアップ手順

### 1. 実行元の判定

**spec-know-how リポジトリ内から実行している場合:**
- ソースは自身のリポジトリ（ローカルファイル）
- 対象プロジェクトは引数で指定

**対象プロジェクト内から実行している場合（ブートストラップ）:**
- ソースは GitHub から clone
- 対象プロジェクトはカレントディレクトリ

### 2. ソースの取得

#### ローカルにある場合（パターン A）

spec-know-how = カレントディレクトリ
dd-know-how = 以下の優先順で探索:

1. `{対象プロジェクト}/.claude/skills/dd/SKILL.md` が存在 → 導入済み（スキップ）
2. `../dd-know-how/` が存在 → ローカルから使用
3. 見つからない → GitHub から clone

#### GitHub から取得する場合（パターン B、またはパターン A で dd-know-how 未発見時）

```bash
# 対象プロジェクト内に一時ディレクトリを作成
mkdir -p {対象プロジェクト}/tmp

# shallow clone（高速化）
git clone --depth 1 {SPEC_KNOW_HOW_REPO} {対象プロジェクト}/tmp/_spec-know-how
git clone --depth 1 {DD_KNOW_HOW_REPO}   {対象プロジェクト}/tmp/_dd-know-how
```

### 3. dd-know-how の導入チェック（CRITICAL）

対象プロジェクトに dd-know-how が導入済みか確認:

```bash
ls {対象プロジェクト}/.claude/skills/dd/SKILL.md
ls {対象プロジェクト}/.claude/skills/workflow/SKILL.md
```

#### 導入済みの場合

→ Step 4 へ

#### 未導入の場合

dd-know-how ソース（ローカルまたは `tmp/_dd-know-how`）から Level 2 を導入:

```bash
# フォルダ作成
mkdir -p {対象}/doc/DD {対象}/doc/templates {対象}/doc/archived/DD

# テンプレート・ルール
cp {dd-src}/templates/dd_template.md  {対象}/doc/templates/
cp {dd-src}/rules/dd-basic-rules.md   {対象}/doc/templates/

# スキル
mkdir -p {対象}/.claude/skills/dd {対象}/.claude/skills/workflow
cp {dd-src}/.claude/skills/dd/SKILL.md       {対象}/.claude/skills/dd/
cp {dd-src}/.claude/skills/workflow/SKILL.md  {対象}/.claude/skills/workflow/
```

dd-know-how のバージョン（コミット SHA）を記録。

### 4. 導入レベルの選択

ユーザーに導入レベルを確認:

| レベル | 内容 | 推奨ケース |
|--------|------|------------|
| Level 1（最小） | SKILL.md のみ | まず試したい |
| **Level 2（標準）** | + 設計書 + 実行例 | **通常利用（推奨）** |
| Level 3（フル） | + 方法論・教訓 + スキル例 + 背景資料 | 深い理解が必要 |

### 5. ファイルのコピー

`{spec-src}` = spec-know-how のソース（ローカル or `tmp/_spec-know-how`）

#### Level 1（最小構成）

```bash
mkdir -p {対象}/.claude/skills/spec-know-how
cp {spec-src}/SKILL.md {対象}/.claude/skills/spec-know-how/
```

#### Level 2（標準構成）— Level 1 に加えて

```bash
mkdir -p {対象}/doc/spec-know-how/references
cp -r {spec-src}/references/design   {対象}/doc/spec-know-how/references/
cp -r {spec-src}/references/examples {対象}/doc/spec-know-how/references/
```

#### Level 3（フル構成）— Level 2 に加えて

```bash
cp -r {spec-src}/references/methodology {対象}/doc/spec-know-how/references/
cp -r {spec-src}/references/skills      {対象}/doc/spec-know-how/references/
cp -r {spec-src}/references/background  {対象}/doc/spec-know-how/references/
```

### 6. 仕様抽出の成果物フォルダ作成

```bash
mkdir -p {対象}/doc/spec/business-logic  # FR（機能要件）
mkdir -p {対象}/doc/spec/nfr             # NFR（非機能要件）
mkdir -p {対象}/doc/spec/uxm             # UI操作モデル
```

### 7. CLAUDE.md の更新

対象プロジェクトの CLAUDE.md に以下を追記（`spec-know-how` の文字列で重複チェック）:

```markdown
## 仕様抽出（spec-know-how）

- **依存**: dd-know-how（Level 2 以上）
- **バージョン**: spec-know-how {SHA 短縮7桁}, dd-know-how {SHA 短縮7桁}
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

### 8. tmp の削除

GitHub から clone した場合、一時ディレクトリを削除:

```bash
rm -rf {対象}/tmp/_spec-know-how {対象}/tmp/_dd-know-how
# tmp/ が空なら削除
rmdir {対象}/tmp 2>/dev/null
```

### 9. .gitignore の更新

対象プロジェクトの `.gitignore` に `tmp/` が含まれていなければ追加:

```bash
grep -q '^tmp/' {対象}/.gitignore 2>/dev/null || echo 'tmp/' >> {対象}/.gitignore
```

### 10. 完了報告

```
✓ spec-know-how + dd-know-how のセットアップが完了しました

【ソース】
- spec-know-how: {ローカル or GitHub} ({SHA})
- dd-know-how:   {ローカル or GitHub or 導入済み} ({SHA})

【配置されたファイル】
- dd スキル:         .claude/skills/dd/SKILL.md
- workflow スキル:   .claude/skills/workflow/SKILL.md
- spec-know-how:    .claude/skills/spec-know-how/SKILL.md
- 参照資料:          doc/spec-know-how/references/（Level 2 以上）
- 成果物フォルダ:     doc/spec/（business-logic, nfr, uxm）
- DD フォルダ:       doc/DD/
- DD テンプレート:    doc/templates/dd_template.md

【次のステップ】
1. SKILL.md の Phase 0（ヒアリング）を開始してください
2. 移行元システムの情報を準備してください:
   - 言語/フレームワーク/DB/構成
   - 主対象ドメイン
   - 重要NFR
   - 既存ドキュメントの所在
```

## 注意事項

- 既存ファイルがある場合は上書き前にユーザーに確認
- CLAUDE.md への追記は重複チェック（`spec-know-how` の文字列検索）を行う
- dd-know-how の自動導入は Level 2 固定（Level 3 は dd-know-how 側で手動昇格）
- `--depth 1` で shallow clone するため、clone 元の履歴は取得しない
- clone 後の tmp は必ず削除する（`tmp/` を .gitignore に追加済み）
