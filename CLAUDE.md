# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with the Origami Lite plugin.

## 概要

Origami Liteは、AI駆動ブラックボックステストドキュメント生成フレームワークである。仕様書からテストドキュメントを自動生成し、信号機システム（🟢🟡🔴）でAI推論の確信度を可視化する。

## プラグイン構造

```
origami/
├── commands/origami/         # コマンドファイル
│   ├── extract-features.md   # Phase 1: 機能抽出
│   ├── behavior-checklist.md # Phase 1.5: 動作仕様整理
│   ├── boundary-analysis.md  # Phase 2: 境界値分析
│   └── generate-cases.md     # Phase 3: テストケース生成
├── .claude-plugin/           # プラグイン設定
│   ├── plugin.json
│   └── marketplace.json
├── README.md
├── LICENSE
└── CHANGELOG.md
```

## コマンド一覧

| コマンド | フェーズ | 入力 | 出力 |
|---------|---------|------|------|
| `/origami:extract-features` | Phase 1 | 仕様書 | 01_機能一覧.md |
| `/origami:behavior-checklist` | Phase 1.5 | 機能一覧 | 02_動作仕様一覧.md |
| `/origami:boundary-analysis` | Phase 2 | 動作仕様一覧 | 03_境界値分析表.md |
| `/origami:generate-cases` | Phase 3 | 全ドキュメント | 04_テストケース一覧.md |

## 信号機システム

AI推論の確信度を3段階で可視化する：

| 信号 | 意味 | 判断基準 |
|------|------|----------|
| 🟢 | 高確信度 | 入力ドキュメントに明示的記載あり |
| 🟡 | 中確信度 | テスト技法・業界標準・Web常識から推測 |
| 🔴 | 要確認 | 根拠の薄い推測（必ずレビュー） |

## 出力先

すべての出力ファイルは `docs/origami/` ディレクトリに生成される：

```
docs/origami/
├── 01_機能一覧.md
├── 02_動作仕様一覧.md
├── 03_境界値分析表.md
└── 04_テストケース一覧.md
```

## ワークフロー

### 推奨フロー（順次実行）

```
/origami:extract-features [仕様書]
    ↓
/origami:behavior-checklist
    ↓
/origami:boundary-analysis
    ↓
/origami:generate-cases
```

### 単独実行

各コマンドは単独でも実行可能。仕様書を直接指定する：

```
/origami:generate-cases [仕様書]
```

## コマンドファイルの構造

各コマンドファイルはMarkdown形式のプロンプトテンプレート：

```markdown
---
description: コマンドの簡潔な説明
---

# コマンド名

## 目的
## 事前準備
## 実行内容
## 出力フォーマット
## 実行後の確認
```

## 開発・カスタマイズ時の注意

1. **信号機システムの一貫性**: 全コマンドで同じ判断基準を維持
2. **出力形式の統一**: Markdown形式、Mermaid図表記法を使用
3. **ID体系の維持**: F-XXX（機能）、TC-XXX-YY（テストケース）形式

## 謝辞

Origami Liteは [Tsumiki](https://github.com/classmethod/tsumiki) にインスパイアされて開発された。
