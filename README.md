# renovate-config

usagicollective org 共通の Renovate 設定プリセット。

## 使い方

各リポジトリの `renovate.json` は、原則これを extends するだけにする。

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>usagicollective/renovate-config"]
}
```

リポジトリ固有の事情でプリセットから外れる場合は、`renovate.json` にコメントを書くのではなく [orchestration の docs/ops/renovate.md](https://github.com/usagicollective/orchestration/blob/main/docs/ops/renovate.md) に理由を記録する。

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

## 注意

**このリポジトリは public にしている。** private な preset リポジトリを参照するには、preset リポジトリ自体への Renovate App のインストールと `local>` 構文が必要で、さらに参照側が public だと解決できない。プリセットに秘匿する内容はないため、制約の少ない public を選んでいる。

## 参照

- [Shareable Config Presets - Renovate Docs](https://docs.renovatebot.com/config-presets/#github)
- [Full Config Presets - Renovate Docs](https://docs.renovatebot.com/presets-config/#configbest-practices)
- [orchestration - renovate 運用](https://github.com/usagicollective/orchestration/blob/main/docs/ops/renovate.md)
