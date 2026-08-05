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

`config:best-practices` + major 以外 automerge

| 更新の種類 | automerge | 補足 |
| :-- | :-- | :-- |
| `major` | ❌ | 破壊的変更を含みうるため人間が判断する |
| `minor` / `patch` | ✅ | CI が緑であることが条件 |
| `pin` / `pinDigest` / `digest` | ✅ | `config:best-practices` による GitHub Actions の digest 固定を含む |
| `lockFileMaintenance` | ✅ | lockfile 全体の再生成 |

## 公開直後の版を避ける

npm の更新は **公開から 3 日**待ってから PR を出す（`minimumReleaseAge: "3 days"`）。マルウェア検知の猶予を作り、直後に unpublish された版を掴まないため。

これは `config:best-practices` が extends する `security:minimumReleaseAgeNpm` と同じ値だが、上流が変わっても各リポジトリの `.npmrc` とずれないよう `default.json` に明示している。**`.npmrc` の `min-release-age=3` と対で運用する**（npm 側も日数指定。→ [orchestration - npm の設定](https://github.com/usagicollective/orchestration/blob/main/docs/ops/npm.md)）。

`internalChecksFilter: "strict"` のため、待機中はブランチ自体が作られない。3 日を過ぎてから PR が現れる。

以下の更新種別は上流プリセットが対象外にしており、ここでも解除しない。**解除すると PR が永久に作られなくなる**（Renovate 側が release timestamp を渡していないため）:

| 更新種別 | 理由 |
| :-- | :-- |
| `lockFileMaintenance` | パッケージマネージャ側の処理。npm の `min-release-age` が効く |
| `pin` / `replacement` | Renovate が release timestamp を渡していない（[#40288](https://github.com/renovatebot/renovate/issues/40288) / [#39400](https://github.com/renovatebot/renovate/issues/39400)） |

その他の設定:

- `dependencyDashboard: false` — Issue でのダッシュボードを作らない
- `timezone: Asia/Tokyo`
- 全 PR に `dependencies` ラベル。eslint 関連には `dependencies:lint`、prettier 関連には `dependencies:format` を追加
- `cloudflare` - wrangler と workers-types は peer 依存で結合しているためまとめて更新する

## 参照

- [Shareable Config Presets - Renovate Docs](https://docs.renovatebot.com/config-presets/#github)
- [Full Config Presets - Renovate Docs](https://docs.renovatebot.com/presets-config/#configbest-practices)
- [orchestration - renovate 運用](https://github.com/usagicollective/orchestration/blob/main/docs/ops/renovate.md)
