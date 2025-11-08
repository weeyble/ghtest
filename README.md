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
