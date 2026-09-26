# renovate-config

Org 共通の Renovate 設定プリセット

## 使い方

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>usagicollective/renovate-config"]
}
```

## プリセット

| プリセット | extends の書き方 | 更新の出し方 |
| :-- | :-- | :-- |
| `default` | `github>usagicollective/renovate-config` | 週次（土曜・JST）。major 以外を 1 本の PR にまとめる |
| `active-update` | `github>usagicollective/renovate-config:active-update` | 随時。更新ごと（またはグループごと）に PR を出す |

`default` は `active-update` の設定をすべて引き継ぎ、スケジュールとグループ化だけを足す。

### `default`（週次）

- PR を作るのは土曜のみ（`schedule: ["* * * * 6"]`、`timezone: Asia/Tokyo`）。作成済みの PR の rebase と automerge は曜日を問わず続く
- `minor` / `patch` / `pin` / `pinDigest` / `digest` を `all non-major dependencies` の 1 本にまとめる
- `major` はまとめない。automerge しない更新が入るとグループ全体が automerge されなくなるため、従来どおり個別（または下記のグループ単位）の PR になる
- `lockFileMaintenance` も土曜に出す。Renovate は lockfile の再生成を他の更新と同じ PR にまとめられないため、別の PR になる
- 脆弱性の更新は `vulnerabilityAlerts` の既定のまま。スケジュールに関係なく、別の PR ですぐに出る

## 方針（両プリセット共通）

`config:best-practices` + major 以外 automerge

| 更新の種類 | automerge | 補足 |
| :-- | :-- | :-- |
| `major` | ❌ | 破壊的変更を含みうるため人間が判断する |
| `minor` / `patch` | ✅ | CI が緑であることが条件 |
| `pin` / `pinDigest` / `digest` | ✅ | `config:best-practices` による GitHub Actions の digest 固定を含む |
| `lockFileMaintenance` | ✅ | lockfile 全体の再生成 |

**自 org の action だけは digest を固定しない**（`usagicollective/actions`）。同じ org で trust boundary が同じため pin の防御効果が無く、main が動くたびに全リポジトリへ更新 PR が出るため。**外部 action の digest 固定は続ける。**

その他の設定（`default` ではグループ化により major 以外は `all non-major dependencies` にまとまるため、以下のグループは主に major で効く）:

- 全 PR に `dependencies` ラベル。eslint 関連には `dependencies:lint`、prettier 関連には `dependencies:format` を追加
- `cloudflare` - wrangler と workers-types は peer 依存で結合しているためまとめて更新する
- `astro` - astro と `@astrojs/*` は peer で結合しているためまとめて更新する
- `lucide` - lucide のアイコンパッケージ（`lucide` / `lucide-react` / `@lucide/astro` など 11 個）は同一モノレポで同時にリリースされるためまとめて更新する。`@lucide/lab` と内部ツールは別のリリース列なので含めない

## 参照

- [Shareable Config Presets - Renovate Docs](https://docs.renovatebot.com/config-presets/#github)
- [Full Config Presets - Renovate Docs](https://docs.renovatebot.com/presets-config/#configbest-practices)
- [orchestration - renovate 運用](https://github.com/usagicollective/orchestration/blob/main/docs/ops/renovate.md)
