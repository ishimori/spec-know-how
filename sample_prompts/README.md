# サンプルプロンプト集

各 Step の作業を DD（設計書）として起票するためのプロンプトサンプルです。

## 前提

dd-know-how スキルが導入済みであること。
未導入の場合は https://github.com/ishimori/dd-know-how を参照してセットアップしてください。

## DD の粒度について

各ファイルのプロンプトは「1 Gate = 1 DD」として記載していますが、**実際にはシステムのボリュームに応じてさらに小分けにすることを推奨します**。

例（Gate 2 を分割する場合）:
- DD-A: スキーマ取得（テーブル一覧の作成まで）
- DD-B: 関連テーブルの詳細確認（外部キー・ビュー・ストアドプロシージャ）
- DD-C: マスタデータの具体値確認

DD を小さく保つと、作業の進捗が見やすくなり、詰まったときに原因を特定しやすくなります。

## 使い方

1. 対応するファイルを開く
2. 「作業用 DD 起票プロンプト」をコピーして Claude に貼り付ける
3. `<コードベースのパス>` などのプレースホルダーを実際の値に置き換える
4. DD が完了したら、「Gate 宣言 DD 起票プロンプト」を使って Gate 通過を明示的に宣言する
5. 対応する `gates/` のチェックリストで Gate 通過を確認する

## Gate との対応

| サンプルプロンプト | 対応 Gate |
|-----------------|-----------|
| [01_initial_survey.md](01_initial_survey.md) | [gates/01_initial_survey.md](../gates/01_initial_survey.md) |
| [02_database.md](02_database.md) | [gates/02_database.md](../gates/02_database.md) |
| [03_screens.md](03_screens.md) | [gates/03_screens.md](../gates/03_screens.md) |
| [04_business_logic.md](04_business_logic.md) | [gates/04_business_logic.md](../gates/04_business_logic.md) |
| [05_nfr.md](05_nfr.md) | [gates/05_nfr.md](../gates/05_nfr.md) |
| [06_qa_ready.md](06_qa_ready.md) | [gates/06_qa_ready.md](../gates/06_qa_ready.md) |

## Gate 宣言 DD について

Gate 宣言 DD は「この Gate を通過した」という**人間の意思決定を記録する**ためのものです。

- 作業 DD が完了した後に別途起票する
- チェックリストの確認結果・未解決事項・判断の根拠を記録する
- 「Gate 2 通過。マスタデータは未確認だが、Step 4 で仕様書作成後に照合する方針」のような判断も記録できる

各プロンプトファイルの末尾に Gate 宣言 DD 用のプロンプトが含まれています。
