# spec-know-how

**レガシーシステムのリエンジニアリング（仕様抽出 → 実装）のための実践ガイド集**

自動化スキルではなく、「こう進めれば仕様を引き出し、高品質に作り直せる」という手順と判断基準を示します。

---

## この参考書でできること

| コンテンツ | 内容 |
|-----------|------|
| [GUIDE.md](GUIDE.md) | 仕様抽出のクイックリファレンス（Step 1〜6）。どこから始めて何を把握すればいいかを示す |
| [manuals/](manuals/) | **実践マニュアル**（包括ガイド）。「なぜ・どうやるか」の詳細 + ゲート定義 + アンチパターン |
| [gates/](gates/) | 各ステップの通過基準チェックリスト。「次に進んでいいか」を人間が判断するために使う |
| [how-to/qa-skill.md](how-to/qa-skill.md) | プロジェクト固有の QA スキルの作り方 |
| [references/](references/) | 実績例・教訓・スキル実装例（参考資料） |

### GUIDE.md と manuals/ の役割分担

- **GUIDE.md** = クイックリファレンス（「何をするか」のチェックリスト形式、6ステップ）
- **manuals/** = 包括マニュアル（「なぜ・どうやるか」の詳細 + 移行判断 + DA義務化 + ゲート定義）
  - [01_仕様書作成マニュアル.md](manuals/01_仕様書作成マニュアル.md) — コードを書く前の全工程（移行判断 → 仕様抽出 → 設計 → 横断検証）
  - [02_実装マニュアル.md](manuals/02_実装マニュアル.md) — コードを書く全工程（垂直スライス → 核機能 → 放射的実装 → 品質ゲート → デプロイ）

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
├── GUIDE.md                      # クイックリファレンス（Step 1〜6）
├── manuals/                      # 実践マニュアル（包括ガイド）
│   ├── 01_仕様書作成マニュアル.md # 仕様抽出の詳細手順・ゲート・アンチパターン
│   └── 02_実装マニュアル.md       # 実装の詳細手順・ゲート・アンチパターン
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
