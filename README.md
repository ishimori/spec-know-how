# spec-know-how

**レガシーコードベースから仕様を抽出・検証し、信頼度付きの知識基盤を構築するオーケストレーションスキル**

Claude Code のスキルとして動作し、既存システムのソースコードから業務仕様を体系的に抽出します。

---

## 解決する問題

| 問題 | 結果 |
|------|------|
| **仕様書がない** | コードにしか存在しない業務ルールを構造化して文書化する |
| **LLM の解釈頼み** | 機械抽出ツールと突合し、根拠のない仕様を排除する |
| **信頼度が不明** | 全ての仕様項目に 4 段階の信頼度（High/Medium/Low/Conflicting）を付与する |
| **NFR が後回し** | FR/NFR を独立トラックで管理し、非機能要件の抜けを防ぐ |
| **QA で根拠が出ない** | 回答には必ず根拠（コード行/仕様書）と信頼度を付記する |

## 仕組み

```
Phase 0: ヒアリング     対象システムの前提確定
Phase 1: 抽出計画       FR/NFR/UXM を分離して DD 起票
Phase 2: 抽出戦略       技術スタックに応じた抽出器選定
Phase 2.5: 抽出器検証   抽出器自体の品質を先に確認
Phase 3: 機械抽出       コードから事実データを自動抽出
Phase 4: 仕様生成+突合  仕様を生成し機械事実と突合、信頼度付与
Phase 5: QA インデックス 仕様問い合わせに即答できる状態を構築
Phase 6: 継続改善       問題発見→外部AI評価→解決条件ループ
```

## 前提条件

**dd-know-how** が必須です。spec-know-how は DD（Design Document）を起票・管理するオーケストレーターであり、DD の実体管理は dd-know-how が担います。

## 導入方法

[IMPORT.md](IMPORT.md) を参照してください。

```bash
cd your-project
claude
> https://github.com/ishimori/spec-know-how から spec-know-how を導入して
```

dd-know-how も自動的に GitHub から取得・導入されます。

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
