---
name: web-research
description: 企業・テーマについてWeb検索で情報収集し、調査レポートをGoogleDriveに保存するスキル。「〜を調べて」「〜をリサーチして」「〜について情報収集して」「〜をWeb検索して」「〜の情報をまとめて」などのキーワードに反応すること。PRTimesやスプレッドシート記録が目的でない、単純なWeb調査・レポート作成の場合はこのスキルを使うこと。
---

# Web調査レポートスキル

指定の企業・テーマをWeb検索で調査し、Googleドライブに調査レポートを保存します。

## 対応タスク

- 企業・サービスの基本情報調査
- 特定テーマの情報収集・要約
- 業界動向のリサーチ

---

## 作業手順

### Step 1: 調査対象を特定

ユーザーの指示から以下を確認する。

- **調査対象**（企業名 or テーマ名）
- **調査の目的・観点**（なければ「全般的な情報収集」とする）

フォルダ名として使う **保存先名** を決定する（例: 企業名なら`トヨタ自動車`、テーマなら`AI市場動向`）。

### Step 2: Web検索（WebSearchツール使用）

以下の観点で **3〜5回** 検索を実施する。

```
[調査対象] 概要 事業内容
[調査対象] 最新ニュース 2026
[調査対象] [目的に応じた追加キーワード]
```

各検索結果から以下を抽出する：
- 重要な事実・数値・データ
- 最新の動向・トピック
- 参考URL（信頼性の高いもの）

### Step 3: レポート作成

以下のフォーマットで調査レポートを作成する。

```markdown
# [調査対象] - Web調査レポート

**調査日:** YYYY-MM-DD  
**調査キーワード:** [使用したキーワード]

---

## 概要
（3〜5行で調査対象の要点をまとめる）

## 主要情報
（箇条書きで重要な情報を列挙）

## 詳細
（セクションを分けて詳しく記述）

## 参考URL
- [サイト名](URL)
```

### Step 4: Google Driveへ保存

Google Drive MCPを使ってレポートを保存する。

**フォルダ構成:**
```
情報収集/
└── [保存先名]/
    └── web_research_YYYY-MM-DD.md
```

1. `search_files` で情報収集フォルダ内に `[保存先名]` のサブフォルダが存在するか確認する
   - クエリ: `parentId = '1wwaCVR5eXBx9HXIWPoXSYwfYNkoN4PUc' and title = '[保存先名]' and mimeType = 'application/vnd.google-apps.folder'`

2. フォルダが存在しない場合は `create_file` で作成する
   - `title`: [保存先名]
   - `parentId`: `1wwaCVR5eXBx9HXIWPoXSYwfYNkoN4PUc`
   - `contentMimeType`: `application/vnd.google-apps.folder`

3. `create_file` でレポートを保存する
   - `title`: `web_research_YYYY-MM-DD.md`
   - `parentId`: 上記フォルダのID
   - `textContent`: 作成したレポート本文
   - `contentMimeType`: `text/plain`
   - `disableConversionToGoogleType`: true

### Step 5: 完了報告

以下を報告する：
- 保存したレポートのDriveリンク（`viewUrl`）
- 調査結果の要点（2〜3行）
- 追加調査が必要な場合はその提案

---

## ツール要件

- **WebSearch**: 情報収集に使用
- **Google Drive MCP**: レポートの保存に使用

---

## 保存先情報

- **情報収集フォルダID**: `1wwaCVR5eXBx9HXIWPoXSYwfYNkoN4PUc`
- **Drive URL**: https://drive.google.com/drive/folders/1wwaCVR5eXBx9HXIWPoXSYwfYNkoN4PUc
