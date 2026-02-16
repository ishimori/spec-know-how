# spec-know-how インポートガイド

自分のプロジェクトに spec-know-how を導入する手順です。

## 何が手に入るか

| 機能 | 効果 |
|------|------|
| **仕様抽出ワークフロー** | Phase 0〜6 の体系的な抽出プロセス |
| **信頼度管理** | 4段階（High/Medium/Low/Conflicting）の信頼度付き仕様 |
| **QA インデックス** | 仕様問い合わせに根拠付きで即答 |
| **機械抽出戦略** | プロジェクトの技術スタックに応じた可変型抽出 |
| **Gate 判定** | 各フェーズの完了条件を明確化 |

## 前提条件

**dd-know-how（Level 2 以上）が必須です。**

spec-know-how は DD を起票・管理するオーケストレーターであり、DD の実体管理は dd-know-how が担います。下記のセットアップ方法はいずれも dd-know-how を自動的に導入します。

## セットアップ

### 初回のみ: グローバル設定

`~/.claude/CLAUDE.md` に以下を追記してください（一度だけ）:

```markdown
## Know-How スキル

「spec-know-how を導入して」と言われたら:

1. `https://github.com/ishimori/spec-know-how.git` を `tmp/_spec-know-how` に shallow clone
2. `tmp/_spec-know-how/IMPORT.md` の「ブートストラップ手順」に従って導入を実行
3. dd-know-how も同手順内で自動導入される
```

この設定により、Claude はどのプロジェクトでも URL なしで spec-know-how を導入できるようになります。

### 方法 1: ブートストラップ（推奨 — リポジトリ不要）

対象プロジェクト内で Claude Code を起動し、以下を依頼するだけで導入できます。
**spec-know-how / dd-know-how のローカル clone は不要です。**

```bash
cd your-project
claude
```

```
spec-know-how を導入して
```

Claude が以下を自動実行します:

1. `tmp/_spec-know-how` と `tmp/_dd-know-how` を GitHub から shallow clone
2. dd-know-how Level 2（DD管理 + DA批判レビュー）を導入
3. spec-know-how（SKILL.md + 参照資料）を導入
4. CLAUDE.md にバージョン・設定を追記
5. `tmp/` を削除してクリーンアップ

> **仕組み**: `~/.claude/CLAUDE.md` のグローバル設定により、Claude がこの IMPORT.md を取得・参照して実行します。
> `/setup` スキルがなくても、この手順を依頼すれば動作します。

#### ブートストラップ手順（Claude が実行する内容）

```bash
# 1. 一時ディレクトリに clone
mkdir -p tmp
git clone --depth 1 https://github.com/ishimori/spec-know-how.git tmp/_spec-know-how
git clone --depth 1 https://github.com/ishimori/dd-know-how.git   tmp/_dd-know-how

# 2. dd-know-how Level 2 を導入
mkdir -p doc/DD doc/templates doc/archived/DD
cp tmp/_dd-know-how/templates/dd_template.md       doc/templates/
cp tmp/_dd-know-how/rules/dd-basic-rules.md        doc/templates/
mkdir -p .claude/skills/dd .claude/skills/workflow
cp tmp/_dd-know-how/.claude/skills/dd/SKILL.md       .claude/skills/dd/
cp tmp/_dd-know-how/.claude/skills/workflow/SKILL.md .claude/skills/workflow/

# 3. spec-know-how を導入
mkdir -p .claude/skills/spec-know-how
cp tmp/_spec-know-how/SKILL.md .claude/skills/spec-know-how/
mkdir -p doc/spec-know-how/references
cp -r tmp/_spec-know-how/references/design   doc/spec-know-how/references/
cp -r tmp/_spec-know-how/references/examples doc/spec-know-how/references/

# 4. 成果物フォルダ
mkdir -p doc/spec/business-logic doc/spec/nfr doc/spec/uxm

# 5. クリーンアップ
rm -rf tmp/_spec-know-how tmp/_dd-know-how
rmdir tmp 2>/dev/null

# 6. .gitignore に tmp/ を追加（安全策）
grep -q '^tmp/' .gitignore 2>/dev/null || echo 'tmp/' >> .gitignore
```

### 方法 2: /setup コマンド（spec-know-how がローカルにある場合）

spec-know-how リポジトリ内で Claude Code を起動し、対象プロジェクトを指定:

```bash
cd spec-know-how
claude
> /setup /path/to/your-project
```

dd-know-how が未導入であれば、ローカル探索 → GitHub clone の順で自動導入します。

### 方法2: 手動セットアップ

以下、`spec-know-how/` は本リポジトリのパス、`your-project/` は導入先のパスです。

#### Step 1: dd-know-how の確認

```bash
# 導入先に dd-know-how が入っているか確認
ls your-project/.claude/skills/dd/SKILL.md
ls your-project/.claude/skills/workflow/SKILL.md
# → 存在しない場合は dd-know-how を先に導入
```

#### Step 2: spec-know-how スキルの配置

```bash
cd your-project

# スキルディレクトリ作成
mkdir -p .claude/skills/spec-know-how

# SKILL.md をコピー
cp spec-know-how/SKILL.md .claude/skills/spec-know-how/
```

#### Step 3: 参照資料の配置（推奨）

```bash
# 参照資料をコピー（運用チェックルール・実行例・方法論）
mkdir -p doc/spec-know-how/references
cp -r spec-know-how/references/design    doc/spec-know-how/references/
cp -r spec-know-how/references/examples  doc/spec-know-how/references/
```

必要に応じて他の参照資料もコピー:

```bash
cp -r spec-know-how/references/methodology  doc/spec-know-how/references/
cp -r spec-know-how/references/skills        doc/spec-know-how/references/
cp -r spec-know-how/references/background    doc/spec-know-how/references/
```

#### Step 4: CLAUDE.md に追記

導入先の `CLAUDE.md` に以下を追記:

```markdown
## 仕様抽出（spec-know-how）

- **スキル**: `/spec-know-how` — レガシーコードからの仕様抽出ワークフロー
- **参照資料**: `doc/spec-know-how/references/`
- **信頼度ルール**: High / Medium / Low / Conflicting（4段階）
- **QA応答要件**: 回答 + 信頼度 + 根拠行 + コミットSHA + 生成日時
```

#### Step 5: 仕様抽出の成果物フォルダ作成

```bash
mkdir -p doc/spec/business-logic  # FR（機能要件）の仕様書
mkdir -p doc/spec/nfr             # NFR（非機能要件）の仕様書
mkdir -p doc/spec/uxm             # UI操作モデルの仕様書
```

## 導入後のフォルダ構造

```
your-project/
├── .claude/
│   └── skills/
│       ├── dd/                        # dd-know-how（必須）
│       │   └── SKILL.md
│       ├── workflow/                  # dd-know-how（必須）
│       │   └── SKILL.md
│       └── spec-know-how/             # ★ 本スキル
│           └── SKILL.md
├── doc/
│   ├── DD/                            # DD設計書（dd-know-how）
│   ├── archived/DD/                   # アーカイブ済みDD
│   ├── templates/                     # DDテンプレート
│   ├── spec/                          # ★ 仕様抽出の成果物
│   │   ├── business-logic/            # FR仕様書
│   │   ├── nfr/                       # NFR仕様書
│   │   └── uxm/                       # UI操作モデル仕様書
│   └── spec-know-how/                 # ★ 参照資料
│       └── references/
└── CLAUDE.md
```

## バージョン固定

導入時に使用バージョン（タグ or コミット SHA）を記録してください。

```bash
cd spec-know-how
git log --oneline -1
# → abc1234 v0.1.0: 初期リリース

# 導入先の CLAUDE.md に記録
echo "- spec-know-how: abc1234 (v0.1.0)" >> your-project/CLAUDE.md
```

案件途中でスキルの挙動が変わることを防ぐため、バージョンを固定して運用します。

## 検証チェックリスト

- [ ] dd-know-how が導入済み（`/dd list` が動作する）
- [ ] `.claude/skills/spec-know-how/SKILL.md` が配置されている
- [ ] `CLAUDE.md` に spec-know-how の設定が記載されている
- [ ] `doc/spec/` フォルダが存在する
- [ ] 使用バージョンが `CLAUDE.md` に記録されている

## トラブルシューティング

### スキルが認識されない

- `.claude/skills/spec-know-how/SKILL.md` の配置を確認
- Claude Code を再起動

### DD 起票ができない

- dd-know-how が導入されているか確認（`/dd list` で動作確認）
- dd-know-how の Level が 2 以上であることを確認

### 機械抽出ツールがない

- Phase 2 で対象プロジェクトに適した抽出ツールを選定します
- Python AST 解析が必要な場合は `references/skills/verify_SKILL.md` を参考に構築します
