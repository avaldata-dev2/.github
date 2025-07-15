# SBOM & Grype セキュリティスキャンの運用方法

このリポジトリでは、Pull Request に対して SBOM 生成および Grype による脆弱性スキャンを自動実行する GitHub Actions を設定しています。

---

## 対象ブランチ

- `develop` ブランチへの Pull Request 時に自動実行されます。

---

## 使用ワークフロー

- `.github/workflows/sbom-grype.yml`

---

## 処理内容

1. **SBOMの生成**
   - `anchore/sbom-action` を用いて SPDX JSON形式で `bom.json` を生成
   - `jq` で整形した読みやすい `bom.txt` を生成
   - GitHub UI で要約表示

2. **Grype による脆弱性スキャン**
   - `bom.json` を使って `grype-results.sarif`（GitHubセキュリティ対応形式）を生成
   - CLIで人間向けに整形した `grype-results.txt` も出力

3. **成果物のアップロード**
   - `bom.txt`（SBOM整形版）
   - `grype-results.sarif`（SARIF形式）
   - `grype-results.txt`（テキスト形式）

---

## 成果物の確認方法

PRページの **Actionsタブ → 該当ジョブ** にアクセスし、以下を確認できます：

- GitHub Actions UI 上に 30行のサマリが表示されます
- 成果物（Artifacts）として以下ファイルをダウンロード可能：
  - `sbom-table`（bom.txt）
  - `grype-sarif`（SARIF形式）
  - `grype-table`（grype結果テキスト）

---

## 注意事項

- スキャンで脆弱性が検出されても **CIは失敗しません**（`fail-build: false`）
- 脆弱性の深刻度などは `grype-results.txt` を確認してください

---

## 導入方法（自分のリポジトリで使うには）

このワークフローを再利用するには、対象リポジトリの `.github/workflows/` に以下のようなワークフローファイルを作成してください：

```yaml
# .github/workflows/sbom.yml

name: SBOM Scan

on:
  pull_request:
    branches:
      - develop

jobs:
  sbom-grype:
    uses: avaldata-dev2/.github/.github/workflows/sbom-grype.yml@main
    secrets: inherit
```

### 注意点：

- 既に他の `pull_request` ワークフローがある場合は、併用に注意してください

---

## 参考リンク

- [Anchore SBOM Action](https://github.com/anchore/sbom-action)
- [Grype](https://github.com/anchore/grype)
