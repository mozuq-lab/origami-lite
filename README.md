# Origami Lite

AI 駆動ブラックボックステストドキュメント生成フレームワーク

## 概要

Origami Lite は、仕様書からテストドキュメントを自動生成する Claude Code Plugin です。信号機システム（🟢🟡🔴）により、AI 推論の確信度を可視化し、効率的なレビューを支援します。

## 特徴

- **5 段階のワークフロー**: 機能抽出 → 動作仕様整理 → 境界値分析 → テストケース生成 → 観点レビュー（オプション）
- **観点レビュー**: 生成済みテストケースを 203 項目のテスト観点カタログと照合し、網羅性をレビュー
- **信号機システム**: AI 推論の確信度を 3 段階で可視化
  - 🟢 高確信度: 仕様書に明示的記載あり
  - 🟡 中確信度: 業界標準・テスト技法から推測
  - 🔴 要確認: 根拠の薄い推測（必ずレビュー）
- **Given/When/Then 形式**: 実行可能なテストケースを自動生成
- **柔軟な入力**: Markdown、テキスト、URL に対応
- **追加ルール対応**: プロジェクト固有のルールを適用可能

## 追加ルール

プロジェクト固有のルールを追加することで、テストドキュメント生成をカスタマイズできます。

### ルールの配置

```text
docs/
├── rule/                    # 汎用ルール（プロジェクト共通）
│   └── *.md
└── rule/origami/            # Origami 固有ルール
    └── *.md
```

### 読み込み順序

1. `docs/rule` ディレクトリ内のすべてのファイル
2. `docs/rule/origami` ディレクトリ内のすべてのファイル

各ディレクトリが存在する場合のみ読み込まれます。

### ルールの例

```markdown
# プロジェクト固有ルール

## テスト観点の追加
- セキュリティテストを必ず含める
- パフォーマンステストの閾値は 3 秒以内

## 用語の統一
- 「ユーザー」ではなく「会員」を使用
- 「購入」ではなく「注文」を使用
```

## インストール

```bash
# 1. Marketplace を追加
/plugin marketplace add https://github.com/mozuq-lab/origami-lite

# 2. プラグインをインストール
/plugin install origami@origami-lite
```

## コマンド一覧

### 基本コマンド

| コマンド                      | 説明                      | 出力ファイル            |
| ----------------------------- | ------------------------- | ----------------------- |
| `/origami:extract-features`   | 仕様書から機能一覧を抽出  | 01\_機能一覧.md         |
| `/origami:generate-checklist` | Must/Never 動作仕様を整理 | 02\_動作仕様一覧.md     |
| `/origami:analyze-boundaries` | 境界値分析表を作成        | 03\_境界値分析表.md     |
| `/origami:generate-cases`     | テストケース一覧を生成    | 04\_テストケース一覧.md |
| `/origami:review-viewpoints`  | テスト観点の網羅性をレビュー（オプション） | 05\_観点レビュー一覧.md |

### タスク分割コマンド

大規模仕様書のコンテキスト肥大化を防ぐため、機能単位でタスクを分割して順次実行できます。
**機能別ディレクトリ出力**: 各機能を独立したディレクトリに出力し、コンテキストサイズを一定に保ちます。

| コマンド                | 説明                                   | 出力                                   |
| ----------------------- | -------------------------------------- | -------------------------------------- |
| `/origami:split-spec`   | 仕様書から機能を抽出しタスク一覧を生成 | {仕様書名}/tasks/task-list.md          |
| `/origami:run-task`     | 指定タスクを実行（`--phase` でフェーズ指定可） | {仕様書名}/F-XXX_{機能名}/ に4-5ファイル |
| `/origami:verify-tasks` | フェーズ単位の進捗状況を表示       | 進捗レポート                           |

## 使い方

### 推奨ワークフロー

```bash
# Step 1: 仕様書から機能を抽出
/origami:extract-features docs/spec.md

# Step 2: Must/Never動作仕様を整理
/origami:generate-checklist

# Step 3: 境界値を分析
/origami:analyze-boundaries

# Step 4: テストケースを生成
/origami:generate-cases

# Step 5 (オプション): テスト観点の網羅性をレビュー
/origami:review-viewpoints
```

### タスク分割ワークフロー（大規模仕様書向け）

```bash
# Step 1: 仕様書からタスク一覧を生成
# → docs/origami/large-spec/tasks/task-list.md が生成される
/origami:split-spec docs/large-spec.md

# Step 2: 各タスクを順次実行
# → docs/origami/large-spec/F-001_{機能名}/ に4ファイル生成
/origami:run-task TASK-001
# → docs/origami/large-spec/F-002_{機能名}/ に4ファイル生成
/origami:run-task TASK-002
...

# Step 3: 進捗を確認（仕様書名を指定）
/origami:verify-tasks large-spec
```

### フェーズ単位実行（v3.1.0〜）

コンテキスト分離を最大化するため、フェーズ単位で実行できます：

```bash
# Phase 1: 機能抽出のみ実行
/origami:run-task TASK-001 --phase 1

# Phase 2: 動作仕様整理のみ実行
/origami:run-task TASK-001 --phase 2

# Phase 3: 境界値分析のみ実行
/origami:run-task TASK-001 --phase 3

# Phase 4: テストケース生成のみ実行
/origami:run-task TASK-001 --phase 4

# Phase 5: テスト観点レビュー（オプション）
/origami:run-task TASK-001 --phase 5

# 全フェーズ実行（--phase 省略時、Phase 1-4のみ）
/origami:run-task TASK-001
```

**フェーズ間入力制限**:
- 各フェーズは前フェーズの出力のみを参照
- 仕様書全体を毎回参照しないため、コンテキストサイズが一定

| フェーズ | 入力 | 出力 |
|---------|------|------|
| Phase 1 | 仕様書（対象機能部分） | `01_機能詳細.md` |
| Phase 2 | `01_機能詳細.md` のみ | `02_動作仕様.md` |
| Phase 3 | `02_動作仕様.md` のみ | `03_境界値分析.md` |
| Phase 4 | `01` + `02` + `03` | `04_テストケース.md` |
| Phase 5 (任意) | `04` + `01` + カタログ | `05_観点レビュー.md` |

### 単独実行

各コマンドは単独でも実行可能です：

```bash
# 仕様書から直接テストケースを生成
/origami:generate-cases docs/spec.md
```

## 出力例

### 機能一覧（01\_機能一覧.md）

```markdown
## 🟢 高確信度（入力ドキュメントに明記）

| #     | 機能名       | 機能概要                 | 根拠                                    |
| ----- | ------------ | ------------------------ | --------------------------------------- |
| F-001 | ユーザー登録 | 新規アカウントを登録する | 「ユーザーは登録できる」(仕様書 1.1 節) |
```

### テストケース（04\_テストケース一覧.md）

```markdown
#### TC-001-01: 正常なユーザー登録

**Given（前提条件）:**

- 未登録のメールアドレスを使用
- ユーザー登録画面が表示されている

**When（実行条件）:**

1. メールアドレスを入力
2. パスワードを入力
3. 登録ボタンをクリック

**Then（期待結果）:**

- [ ] 登録成功メッセージが表示される
- [ ] ユーザーアカウントが作成される
```

## 出力先

すべての出力ファイルは `docs/origami/{仕様書名}/` ディレクトリに生成されます（仕様書ごとに独立）：

### 単独実行時（従来形式）

```text
docs/origami/ecommerce-spec/
├── 01_機能一覧.md
├── 02_動作仕様一覧.md
├── 03_境界値分析表.md
├── 04_テストケース一覧.md
└── 05_観点レビュー一覧.md   # オプション（Phase 5）
```

### タスク分割経由時（機能別ディレクトリ）

```text
docs/origami/ecommerce-spec/
├── tasks/
│   └── task-list.md
├── F-001_ユーザー登録/
│   ├── 01_機能詳細.md
│   ├── 02_動作仕様.md
│   ├── 03_境界値分析.md
│   ├── 04_テストケース.md
│   └── 05_観点レビュー.md   # オプション（Phase 5）
├── F-002_ログイン/
│   └── ...
```

**機能別ディレクトリのメリット**:

- 各タスク実行時のコンテキストサイズが一定に保たれる
- 機能間の出力が混在しない
- 並列実行時のコンフリクト防止

## 信号機システムの活用

### レビュー優先度

| 信号 | レビュー優先度 | 推奨アクション             |
| ---- | -------------- | -------------------------- |
| 🟢   | 低             | 確認程度で OK              |
| 🟡   | 中             | 内容を確認                 |
| 🔴   | 高             | 必ずステークホルダーに確認 |

### 🔴 項目の対応

🔴 項目は仕様書に明記されていない推測項目です。テスト実行前に必ずステークホルダーに確認してください。

## 要件

- Claude Code CLI
- 入力ファイル（Markdown、テキスト、または URL）

## 謝辞

Origami Lite は [Tsumiki](https://github.com/classmethod/tsumiki) にインスパイアされて開発されました。

Tsumiki は、AI 駆動開発のための Claude Code Plugin フレームワークであり、以下の点で Origami Lite の設計に大きな影響を与えています：

- **コマンドテンプレート構造**: Tsumiki のプロンプトテンプレート設計パターン
- **信号機システム**: AI 推論の確信度を可視化するアプローチ
- **ワークフロー設計**: 段階的なドキュメント生成フロー

## ライセンス

MIT License - 詳細は [LICENSE](LICENSE) を参照してください。

### テスト観点カタログのライセンス

`data/viewpoint-catalog.md` に含まれるテスト観点データは、TIS株式会社の「テスト観点カタログ v1.6」から抽出・変換したものです。

- **原典**: テスト観点カタログ v1.6（TIS株式会社）
- **ライセンス**: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) (Creative Commons Attribution-ShareAlike 4.0 International)

## 変更履歴

[CHANGELOG.md](CHANGELOG.md) を参照してください。

## 貢献

Issue、Pull Request を歓迎します。

## 関連リンク

- [Claude Code](https://claude.ai/code)
- [Claude Code Plugins](https://docs.anthropic.com/claude-code/plugins)
