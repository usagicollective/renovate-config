# renovate-config

usagicollective org 共通の Renovate 設定プリセット

## 使い方

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>usagicollective/renovate-config"]
}
```

## 方針

**routine な依存更新は automerge に任せ、人間も AI も merge ボタンを押す作業から降りる。**

| 更新の種類 | automerge | 補足 |
| :-- | :-- | :-- |
| `major` | ❌ | 破壊的変更を含みうるため人間が判断する |
| `minor` / `patch` | ✅ | CI が緑であることが条件 |
| `pin` / `pinDigest` / `digest` | ✅ | `config:best-practices` による GitHub Actions の digest 固定を含む |
| `lockFileMaintenance` | ✅ | lockfile 全体の再生成 |

その他の設定:

- `dependencyDashboard: false` — Issue でのダッシュボードを作らない
- `timezone: Asia/Tokyo`
- 全 PR に `dependencies` ラベル。eslint 関連には `dependencies:lint`、prettier 関連には `dependencies:format` を追加
- `cloudflare` - wrangler と workers-types は peer 依存で結合しているためまとめて更新する

## 参照

- [Shareable Config Presets - Renovate Docs](https://docs.renovatebot.com/config-presets/#github)
- [Full Config Presets - Renovate Docs](https://docs.renovatebot.com/presets-config/#configbest-practices)
- [orchestration - renovate 運用](https://github.com/usagicollective/orchestration/blob/main/docs/ops/renovate.md)
