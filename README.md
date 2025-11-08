# GitHub CLI × Astro TODO 練習帳

このリポジトリは、GitHub CLI (`gh`) を使って Issue / Project を回しながら、Astro 製 TODO アプリを育てるための練習場です。進め方は以下のとおりです。

1. `gh project` / `gh issue` を使って Project (project_id 3, owner `weeyble`) のタスクを確認する。
2. Astro アプリで該当タスクを実装する。
3. 進捗は CLI から Issue/PR を更新して報告する。

README では手順の要点を、詳細なコマンド解説は [`docs/gh-cli.md`](docs/gh-cli.md) にまとめています。

## 1. GitHub CLI の準備

```bash
gh auth login --git-protocol https --web
export GH_REPO="weeyble/ghtest"
gh project view 3 --owner weeyble
gh issue list -R $GH_REPO --label todo
```

Issues の説明に従って作業内容を把握し、完了後は `gh issue close <number>` や `gh project item-update` でステータスを更新します。

## 2. Astro TODO アプリの実行

```bash
npm install
npm run dev
```

- 開発サーバー: http://localhost:4321
- 本番ビルド: `npm run build`
- ビルド確認: `npm run preview`

## 3. `gh pr create` 前のチェックリスト

| コマンド | 目的 |
| --- | --- |
| `npm run lint` | `.astro` / `.ts` / `.js` の ESLint チェック |
| `npm run format` | Prettier の整形確認 |
| `npm run format:write` | Prettier で自動整形 |

## 4. リポジトリ構成

```
├── docs/gh-cli.md  # GitHub CLI 練習用ドキュメント
├── public          # 静的アセット
├── src/pages       # Astro ルート
├── astro.config.mjs
├── tsconfig.json
└── package.json
```

詳細チュートリアルは `docs/gh-cli.md` を参照しつつ、README をクイックスタートのチェックリストとして活用してください。
