# spec-know-how

**レガシーコードベースから仕様を抽出するための参考書**

自動化スキルではなく、「こう進めれば仕様を引き出せる」という手順と判断基準を示します。

---

## この参考書でできること

| コンテンツ | 内容 |
|-----------|------|
| [GUIDE.md](GUIDE.md) | 仕様抽出の手順（Step 1〜6）。どこから始めて何を把握すればいいかを示す |
| [gates/](gates/) | 各ステップの通過基準チェックリスト。「次に進んでいいか」を人間が判断するために使う |
| [how-to/qa-skill.md](how-to/qa-skill.md) | プロジェクト固有の QA スキルの作り方 |
| [references/](references/) | 実績例・教訓・スキル実装例（参考資料） |

---

## 読者別の使い方

### 開発者（コードを読む人）

1. [GUIDE.md](GUIDE.md) を通読して全体像を把握する
2. Step ごとに作業を進め、終わったら該当の Gate でチェックする
3. 情報が揃ったら [how-to/qa-skill.md](how-to/qa-skill.md) を参考に QA スキルを作る

### プロジェクトリーダー・SA

1. [GUIDE.md](GUIDE.md) で何をするプロセスかを把握する
2. 各 Gate チェックリストを使って「本当に情報が揃っているか」を確認する
3. Gate が通過できていない場合は追加調査を指示する

---

## 構成

```
spec-know-how/
├── GUIDE.md                      # 手順書の本体（Step 1〜6）
├── gates/                        # ゲートチェックリスト（6種）
│   ├── README.md                 # ゲートの使い方
│   ├── 01_initial_survey.md      # Gate 1: 初回調査
│   ├── 02_database.md            # Gate 2: DB・データモデル
│   ├── 03_screens.md             # Gate 3: 画面・UI
│   ├── 04_business_logic.md      # Gate 4: 業務ロジック
│   ├── 05_nfr.md                 # Gate 5: 非機能要件
│   └── 06_qa_ready.md            # Gate 6: QA 準備完了
├── how-to/
│   └── qa-skill.md               # QA スキルの作り方
└── references/
    ├── design/                   # 概念資料（FR/NFR/信頼度）
    ├── examples/                 # 実行例（実際の DD）
    ├── methodology/              # 教訓（44DD以上の経験）
    └── skills/                   # スキル実装例（qa, verify）
```

---

## 実績

`housing-e-kintai-next`（Streamlit → React+FastAPI 移行）で実践:

- 約 4,300 行のビジネスロジックを仕様書化
- 195+ 項目をコードと突合して検証
- 44 DD 以上の経験から得た教訓を [references/methodology/](references/methodology/) にまとめ

詳細は [references/examples/](references/examples/) を参照。

---

## ライセンス

MIT License
