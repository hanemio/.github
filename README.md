# hanemio/.github

Organization-level GitHub configurations for the hanemio org.

## Copilot Auto Fix ワークフロー

CI が失敗した PR に対して、自動的に `@copilot` へ修正を依頼する reusable workflow です。

### 各リポジトリへの導入方法

1. リポジトリの **Actions → New workflow → "By hanemio"** からテンプレートを選択
2. または、以下のファイルを `.github/workflows/copilot-autofix.yml` として追加:

```yaml
name: Copilot Auto Fix

on:
  workflow_run:
    workflows:
      - "CI"          # 監視対象のワークフロー名に変更
    types: [completed]

jobs:
  copilot-autofix:
    uses: hanemio/.github/.github/workflows/copilot-autofix.yml@main
    with:
      run_id: ${{ github.event.workflow_run.id }}
      run_conclusion: ${{ github.event.workflow_run.conclusion }}
      run_event: ${{ github.event.workflow_run.event }}
      run_html_url: ${{ github.event.workflow_run.html_url }}
      head_sha: ${{ github.event.workflow_run.head_sha }}
    permissions:
      pull-requests: write
      actions: read
```

### 安全機能

- **無限ループ防止**: 最後のコミットが `copilot[bot]` や `github-actions[bot]` の場合はスキップ
- **重複防止**: 1時間以内に同じ autofix コメントがある場合はスキップ
- **ノイズ除外**: CodeQL, Copilot, Dependabot 関連のジョブ失敗は無視
