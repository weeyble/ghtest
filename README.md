# ghtest

このリポジトリは GitHub CLI (`gh`) の操作を試しながら、Issue や Project を使った TODO 管理の流れを確認するための練習場です。以下では主に利用できるコマンドと、GitHub Projects / Issues を使って TODO リストを作成するまでの一連の手順をまとめています。

## 事前準備
- GitHub CLI をインストール済みであること: <https://cli.github.com/>
- `gh auth login` で GitHub アカウントにログインしておきます。
  ```bash
  gh auth login --git-protocol https --web
  ```
- 作業対象のリポジトリを環境変数に入れておくと便利です。
  ```bash
  export GH_REPO="your-org/ghtest"
  ```

## 練習できる主な操作 (`gh` コマンド)
- **リポジトリ情報**: `gh repo view $GH_REPO --web` / `gh repo clone $GH_REPO`
- **Issue 操作**: `gh issue list -R $GH_REPO`, `gh issue create -R $GH_REPO -t "title" -b "body"`, `gh issue status`
- **Pull Request**: `gh pr create -R $GH_REPO`, `gh pr checkout <number>`, `gh pr view <number> --web`
- **Project (beta)**: `gh project list`, `gh project view <number>`, `gh project create --owner your-org -t board -n "TODO Board"`
- **Project カード管理**: `gh project item-add`, `gh project item-update`, `gh project field-list`
- **リリース**: `gh release create v1.0.0 --generate-notes`, `gh release view`
- **自動化補助**: `gh alias set todo 'issue list --label todo -R $GH_REPO'`

## TODO リストを GitHub Projects / Issues に作成する手順
1. **プロジェクトの作成**
   ```bash
   gh project create --owner your-org --title "TODO Board" --template board
   # 出力される Project ID/番号を控える。例: 3
   ```
2. **Project に使用するフィールドを確認**
   ```bash
   gh project field-list 3 --owner your-org
   ```
   必要であれば `Status` や `Priority` などのフィールドを UI から追加しておきます。
3. **TODO 用の Issue を作成**
   ```bash
   gh issue create -R $GH_REPO \
     --title "TODO: <やること>" \
     --body "## 背景\n- ...\n## 完了条件\n- ..." \
     --label todo
   ```
   TODO を複数に分ける場合はこの操作を繰り返し、`--label todo` を付けます。
4. **Issue を Project のカードとして追加**
   ```bash
   gh project item-add 3 --owner your-org --url https://github.com/$GH_REPO/issues/<番号>
   ```
   追加した後は `gh project item-update` で `Status` や `Priority` を更新できます。
5. **進捗の確認**
   ```bash
   gh project view 3 --owner your-org
   gh issue list -R $GH_REPO --label todo
   ```
   必要に応じて `gh issue close <番号>` や `gh project item-update` で状態を Done に移すと、TODO リストが常に最新の状態になります。

上記をベースに、Organization/個人リポジトリに合わせて `your-org` や `GH_REPO` を書き換えて利用してください。

## Astro TODO アプリ開発 Project を作る
Astro 製 TODO アプリを実装する際に、そのまま Project に流し込めるタスクセットを以下にまとめました。`your-org` や `GH_REPO`、`PROJECT_ID` は環境に合わせて変更してください。

### 1. Project を作成し基本フィールドを準備
```bash
export PROJECT_ID=$(gh project create --owner your-org --title "Astro TODO App" --template board --format json | jq -r '.number')
# 既存 Project を使う場合は番号を直接設定
# export PROJECT_ID=3

gh project field-list $PROJECT_ID --owner your-org
# UI から Status(Backlog/In Progress/Review/Done), Priority, Target release などを用意しておく
```

### 2. タスク (Issue) を作成して Project に流し込む
| フェーズ | Issue タイトル例 | 目的/完了条件 | 代表コマンド |
| --- | --- | --- | --- |
| セットアップ | `[Astro] プロジェクトの初期セットアップ` | `npm create astro@latest` で新規アプリを作り、ESLint/Prettier を導入 | `gh issue create -R $GH_REPO -t "[Astro] プロジェクトの初期セットアップ" -b "## ToDo\n- npm create astro@latest\n- ESLint/Prettier 設定\n- 初期コミット" --label todo`
| UI 設計 | `[Astro] TODO レイアウトとUI設計` | ヘッダー/入力欄/リスト/フィルタのワイヤーフレームとスタイルを決定 | `gh issue create ... --title "[Astro] TODO レイアウトとUI設計" --body "## 完了条件\n- ベースとなるUI設計\n- コンポーネント構成方針"`
| 機能実装 | `[Astro] TODO CRUD 機能` | 追加/編集/完了/削除をハンドラで実装、状態は `src/components/TodoList.astro` で管理 | `gh issue create ... --title "[Astro] TODO CRUD 機能" --body "- アイテム追加フォーム\n- 完了トグル\n- ローカル状態管理"`
| 状態永続化 | `[Astro] localStorage 永続化` | ブラウザ localStorage へ保存・復元、初期データロードと同期処理 | `gh issue create ... --title "[Astro] localStorage 永続化" --body "- マウント時にロード\n- 変更時に保存"`
| フィルタ/UX | `[Astro] フィルタとアクセシビリティ向上` | All/Active/Completed のフィルタ、キーボード操作や ARIA | `gh issue create ... --title "[Astro] フィルタとアクセシビリティ向上"`
| テスト/CI | `[Astro] テストとCI整備` | コンポーネントテスト(Vitest)とGitHub Actions(ビルド+Lint) | `gh issue create ... --title "[Astro] テストとCI整備"`
| デプロイ | `[Astro] デプロイとREADME更新` | Vercel/Netlify へデプロイ、環境変数や usage を README に追記 | `gh issue create ... --title "[Astro] デプロイとREADME更新"`

各 Issue を Project に追加するには、作成後に URL を渡します。
```bash
ISSUE_URL=https://github.com/$GH_REPO/issues/<番号>
gh project item-add $PROJECT_ID --owner your-org --url $ISSUE_URL
```
必要に応じて `gh project item-update` でフィールド (Status/Priority/Target release など) を更新し、進捗を管理します。

### 3. 定期的な進捗レビュー
```bash
# ボードで全体確認
gh project view $PROJECT_ID --owner your-org
# 着手中/ブロッカーの洗い出し
gh issue list -R $GH_REPO --label todo --state open
```
以上を基盤に、Astro TODO アプリの要件変更や追加機能に合わせて Issue/Project を拡張してください。
