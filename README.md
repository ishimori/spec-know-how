# spec-know-how

**レガシーコードベースから仕様を抽出・検証し、信頼度付きの知識基盤を構築するオーケストレーションスキル**

Claude Code のスキルとして動作し、既存システムのソースコードから業務仕様を体系的に抽出します。

---

## 使い方

### Step 1: プロジェクトを作成し、スキルを導入する

```bash
mkdir my-new-project && cd my-new-project
git init
claude
```

```
https://github.com/ishimori/spec-know-how から spec-know-how を導入して
```

Claude が spec-know-how と dd-know-how を自動的にセットアップします。

### Step 2: 仕様抽出を開始する

```
/path/to/legacy-system の仕様を抽出して
```

spec-know-how が **12 DD を一括作成** し、Stage 1（定量棚卸し）を即座に開始します。
以降は DD 内のタスクに従って自動的に進行します。

### Step 3: 結果を確認する

抽出された仕様は `doc/spec/` に、進捗はポートフォリオインデックス DD で確認できます。

```
doc/spec/
├── business-logic/   # 業務ロジック仕様
├── nfr/              # 非機能要件仕様
├── uxm/              # UI操作モデル仕様
└── machine-facts/    # 機械抽出結果
```

---

## 解決する問題

| 問題 | 結果 |
|------|------|
| **仕様書がない** | コードにしか存在しない業務ルールを構造化して文書化する |
| **LLM の解釈頼み** | 機械抽出ツールと突合し、根拠のない仕様を排除する |
| **信頼度が不明** | 全ての仕様項目に 4 段階の信頼度（High/Medium/Low/Conflicting）を付与する |
| **NFR が後回し** | FR/NFR を独立トラックで管理し、非機能要件の抜けを防ぐ |
| **QA で根拠が出ない** | 回答には必ず根拠（コード行/仕様書）と信頼度を付記する |

## 仕組み（v0.2）

起動すると **12 DD を一括作成** し、構造の中で判断する。

```
Stage 1: 定量棚卸し     コードベースの全体像を定量把握
Stage 2: 分析スコープ   広さと深さを決定、スキップ判定
Stage 3: 仕様抽出       FR・データフロー・状態管理・エラー処理（並列可）
Stage 4: 機械ツール設計 自動抽出ツールの設計・作成
Stage 5: 最終仕様       Stage 3 + 4 を突合、信頼度付与
Post:    移行先設計 / NFR総合レビュー / UI要件
```

不要な DD は作成はされますが、DD 内でスキップ判定と理由が記録されます。

## 前提条件

**dd-know-how** が必須です。spec-know-how は DD（Design Document）を起票・管理するオーケストレーターであり、DD の実体管理は dd-know-how が担います。導入時に自動的にセットアップされます。

## 導入方法の詳細

[IMPORT.md](IMPORT.md) を参照してください。3 つの方法があります:

1. **ブートストラップ（推奨）** — GitHub URL を伝えるだけ
2. **/setup コマンド** — spec-know-how がローカルにある場合
3. **手動セットアップ** — ファイルを自分でコピー

## 実績

`housing-e-kintai-next`（Streamlit → React+FastAPI 移行プロジェクト）で実践:

- **12 仕様書** / 約 4,300 行のビジネスロジックを抽出
- **195+ 項目**を機械検証で突合、全 VERIFIED に昇格
- **44 DD 以上**の経験から方法論を抽出・体系化

詳細は [references/examples/](references/examples/) と [references/methodology/](references/methodology/) を参照。

## フォルダ構成

```
spec-know-how/
├── SKILL.md              # スキル定義（メイン）
├── CLAUDE.md             # AI 向け設定ファイル
├── IMPORT.md             # 導入ガイド
├── CHANGELOG.md          # バージョン履歴
├── templates/
│   └── spec-know-how/    # DD テンプレート群（12種）
├── references/
│   ├── design/           # 設計書（ベース思想、ワークフロー、チェックリスト）
│   ├── examples/         # 実行例（DD-002, DD-003）
│   ├── methodology/      # 方法論・教訓
│   ├── skills/           # 実装済みスキル例（verify, qa 等）
│   └── background/       # 背景・思想（意思決定記録）
```

## 関連プロジェクト

- [dd-know-how](https://github.com/ishimori/dd-know-how) — DD 設計書管理・DA 批判レビュー・開発フロー（**必須依存・自動導入**）

## ライセンス

MIT License
