# Origami Lite

AI 駆動ブラックボックステストドキュメント生成フレームワーク

## 概要

Origami Lite は、仕様書からテストドキュメントを自動生成する Claude Code Plugin です。信号機システム（🟢🟡🔴）により、AI 推論の確信度を可視化し、効率的なレビューを支援します。

## 特徴

- **4 段階のワークフロー**: 機能抽出 → 動作仕様整理 → 境界値分析 → テストケース生成
- **信号機システム**: AI 推論の確信度を 3 段階で可視化
  - 🟢 高確信度: 仕様書に明示的記載あり
  - 🟡 中確信度: 業界標準・テスト技法から推測
  - 🔴 要確認: 根拠の薄い推測（必ずレビュー）
- **Given/When/Then 形式**: 実行可能なテストケースを自動生成
- **柔軟な入力**: Markdown、テキスト、URL に対応

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

### タスク分割コマンド

大規模仕様書のコンテキスト肥大化を防ぐため、機能単位でタスクを分割して順次実行できます。
**出力先分離**: 各仕様書ごとに独立したディレクトリに出力を分離し、複数仕様書の管理を容易にします。

| コマンド                | 説明                                   | 出力                          |
| ----------------------- | -------------------------------------- | ----------------------------- |
| `/origami:split-spec`   | 仕様書から機能を抽出しタスク一覧を生成 | {仕様書名}/tasks/task-list.md |
| `/origami:run-task`     | 指定タスクを実行（4コマンド順次呼出）  | {仕様書名}/に4出力ファイル追記 |
| `/origami:verify-tasks` | 指定仕様書のタスク完了状況を表示       | 進捗レポート                  |

## 使い方

### 推奨ワークフロー

```
# Step 1: 仕様書から機能を抽出
/origami:extract-features docs/spec.md

# Step 2: Must/Never動作仕様を整理
/origami:generate-checklist

# Step 3: 境界値を分析
/origami:analyze-boundaries

# Step 4: テストケースを生成
/origami:generate-cases
```

### タスク分割ワークフロー（大規模仕様書向け）

```
# Step 1: 仕様書からタスク一覧を生成
# → docs/origami/large-spec/tasks/task-list.md が生成される
/origami:split-spec docs/large-spec.md

# Step 2: 各タスクを順次実行
# → docs/origami/large-spec/ に出力ファイルが追記される
/origami:run-task TASK-001
/origami:run-task TASK-002
...

# Step 3: 進捗を確認（仕様書名を指定）
/origami:verify-tasks large-spec
```

### 単独実行

各コマンドは単独でも実行可能です：

```
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

```
docs/origami/
├── ecommerce-spec/           # 仕様書ごとに独立したディレクトリ
│   ├── 01_機能一覧.md
│   ├── 02_動作仕様一覧.md
│   ├── 03_境界値分析表.md
│   ├── 04_テストケース一覧.md
│   └── tasks/
│       └── task-list.md
├── auth-system/              # 別の仕様書は別ディレクトリ
│   └── ...
```

**出力先分離のメリット**:
- 複数仕様書の出力が混在しない
- 仕様書の再実行時に既存出力の上書き確認
- 仕様書単位での進捗管理が容易

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

## 変更履歴

[CHANGELOG.md](CHANGELOG.md) を参照してください。

## 貢献

Issue、Pull Request を歓迎します。

## 関連リンク

- [Claude Code](https://claude.ai/code)
- [Claude Code Plugins](https://docs.anthropic.com/claude-code/plugins)
