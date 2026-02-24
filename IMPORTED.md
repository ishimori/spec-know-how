# IMPORTED.md

インポートしたファイルの記録。削除時はこのリストを参照すること。

---

## dd-know-how からのインポート

- **インポート日**: 2026-02-24
- **ソース**: `C:/repo/dd-know-how`
- **対象スキル**: `/dd`（DD設計書の作成・参照・一覧・アーカイブ）

### インポートしたファイル

| ファイルパス | ソース |
|------------|--------|
| `.claude/skills/dd/SKILL.md` | `dd-know-how/.claude/skills/dd/SKILL.md` |
| `templates/dd_template.md` | `dd-know-how/templates/dd_template.md` |
| `rules/dd-basic-rules.md` | `dd-know-how/rules/dd-basic-rules.md` |

### 削除方法

上記3ファイルを削除すれば `/dd` スキルは無効になる。

```bash
rm .claude/skills/dd/SKILL.md
rm templates/dd_template.md
rm rules/dd-basic-rules.md
rmdir .claude/skills/dd
rmdir templates
rmdir rules
```

DDフォルダ（`doc/DD/`, `doc/archived/DD/`）は別途判断すること。
