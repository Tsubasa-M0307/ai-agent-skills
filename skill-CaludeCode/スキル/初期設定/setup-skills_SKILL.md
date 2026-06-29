---
name: setup-skills
description: >
  全スキルの初期設定を行うセットアップスキル。初めてCoworkを使うときや設定をリセット・確認したいときに使う。
  「初期設定をして」「セットアップして」「スキルの設定をして」「DriveやSlackを連携して」
  「設定を確認して」「スキルが動かない」「フォルダを作ってほしい」「メールテンプレートを設定して」
  などのキーワードに反応すること。
  MCP接続確認・Driveフォルダ作成・メールテンプレート確認・Slack疎通確認・未導入スキルの提案を一括で行い、
  全スキルが使える状態にする。初回利用時は必ずこのスキルを実行すること。
---

# スキル初期設定（setup-skills）

インストール済みスキルが動作するために必要な **MCP接続・Driveフォルダ・テンプレート・Slack** を一括で確認・作成する。

---

## 確認・設定する項目の一覧

| # | 項目 | 対象スキル |
|---|------|-----------|
| 1 | Google Drive MCP 接続確認 | 全スキル |
| 2 | Gmail MCP 接続確認 | email-draft-writer |
| 3 | Google Calendar MCP 接続確認 | google-calendar / calendar-slot-finder / calendar-task-prioritizer |
| 4 | Slack MCP 接続確認 | meeting-minutes-with-slack / calendar-task-prioritizer / slack-summarizer |
| 5 | Drive フォルダ確認・作成 | 市場調査 / 競合調査 / ニュース / 議事録 / 情報収集 |
| 6 | メールテンプレート Doc 確認 | email-draft-writer |
| 7 | 送信先スプレッドシート確認 | email-draft-writer |
| 8 | 情報収集スプレッドシート確認 | joho-shushu |
| 9 | Slack DM 疎通テスト | calendar-task-prioritizer |
| 10 | 設定サマリーをSlackへ送信 | — |
| 11 | 未導入スキルの提案・インストール | 全スキル（業務カバレッジ向上） |

---

## Step 0 — 開始メッセージ

以下を出力してからセットアップを開始する。

```
🚀 スキル初期設定を開始します。
各ステップで確認結果を報告しながら進めます。
問題が発生した場合は、対処方法も案内します。
```

---

## Step 1 — MCP接続確認

4つのMCPをそれぞれ確認する。接続確認は軽量なリスト系ツールで行う。

### 1-A Google Drive MCP

`list_recent_files` または `search_files`（`query: "mimeType = 'application/vnd.google-apps.folder'"`, `pageSize: 1`）を呼び出す。

- 成功 → `✅ Google Drive MCP: 接続OK`
- 失敗 → `❌ Google Drive MCP: 未接続`
  → 案内: 「Coworkの設定 > コネクタ から Google Drive を追加してください。」
  → **Drive未接続の場合、項目5〜8（フォルダ作成・テンプレート確認）はスキップ。項目10（サマリー送信）はDrive関連を❌のまま続行**

### 1-B Gmail MCP

`list_drafts`（`maxResults: 1`）を呼び出す。

- 成功 → `✅ Gmail MCP: 接続OK`
- 失敗 → `❌ Gmail MCP: 未接続`
  → 案内: 「email-draft-writer スキルを使うには Gmail を追加してください。」

### 1-C Google Calendar MCP

`list_calendars` を呼び出す。

- 成功 → `✅ Google Calendar MCP: 接続OK`（プライマリカレンダー名も表示）
- 失敗 → `❌ Google Calendar MCP: 未接続`
  → 案内: 「google-calendar / calendar-slot-finder / calendar-task-prioritizer スキルを使うには Google Calendar を追加してください。」

### 1-D Slack MCP

`slack_search_users`（`query: "info@next-group-hd.co.jp"`）を呼び出す。

- 成功（ユーザー取得できた） → `✅ Slack MCP: 接続OK`（ユーザー表示名も表示）
- 失敗 → `❌ Slack MCP: 未接続`
  → 案内: 「meeting-minutes-with-slack / calendar-task-prioritizer / slack-summarizer スキルを使うには Slack を追加してください。」

---

## Step 2 — Drive フォルダ確認・作成

Drive MCP が接続されている場合のみ実行。

以下のフォルダを順番に確認し、存在しない場合は作成する。

| フォルダ名 | 親フォルダID | 用途スキル |
|-----------|-------------|-----------|
| 市場調査レポート | ※自動判定（下記参照） | market-research |
| 競合調査レポート | ※自動判定 | competitor-research |
| ニュースまとめ | ※自動判定 | news-curation |
| 議事録 | ※自動判定 | meeting-minutes |
| 情報収集 | ※自動判定 | web-research / industry-news / franchise-research |
| 音声ファイル | 議事録フォルダ内 | meeting-minutes（音声入力） |

### フォルダ確認の手順

各フォルダについて以下を実行する：

1. `search_files` でフォルダ名を検索する
   ```
   query: title = '[フォルダ名]' and mimeType = 'application/vnd.google-apps.folder'
   ```

2. 検索結果の解釈：
   - **1件以上ヒット** → `✅ [フォルダ名]: 確認OK（ID: [folderID]）`
     → そのフォルダIDをユーザーに確認し、スキルで使うIDとして記録する
   - **0件** → フォルダを新規作成する
     ```json
     { "title": "[フォルダ名]", "contentMimeType": "application/vnd.google-apps.folder" }
     ```
     → `✅ [フォルダ名]: 新規作成しました（ID: [folderID]）`

3. 確認・作成した全フォルダのIDを以下の形式でまとめる：

```
📁 Driveフォルダ一覧
---
市場調査レポート  : [ID]  → market-research スキル
競合調査レポート  : [ID]  → competitor-research スキル
ニュースまとめ    : [ID]  → news-curation スキル
議事録           : [ID]  → meeting-minutes スキル
　└ 音声ファイル  : [ID]  → meeting-minutes（音声入力）
情報収集         : [ID]  → web-research / industry-news / franchise-research
```

### ⚠️ フォルダIDがスキルと異なる場合

スキルに埋め込まれているデフォルトのフォルダIDと、実際に確認・作成したIDが異なる場合は以下を案内する：

> 「スキルのフォルダIDを更新する必要があります。`skill-creator` スキルを使って各スキルのSKILL.mdの `parentId` を上記IDに変更することをお勧めします。」

---

## Step 3 — メールテンプレート確認（email-draft-writer）

Drive MCP と Gmail MCP が両方接続されている場合のみ実行。

### 3-A メールテンプレート Doc

`get_file_metadata`（`fileId: "1v2rnvuPQ9vxuR5snnoklJ4DnR1oVMA6rwa5oSiFnn1o"`）を呼び出す。

- 成功 → `✅ メールテンプレートDoc: 確認OK（[タイトル]）`
- 失敗（404/アクセス不可） →
  ```
  ❌ メールテンプレートDoc: 見つかりません
  → 対処方法:
    1. 新しいGoogle Docを作成してメールテンプレートを記入してください
    2. テンプレートDocのURLをお知らせいただければ設定を更新します
  ```
  ユーザーにテンプレートDocのURLを確認する。

### 3-B 送信先リストスプレッドシート

`get_file_metadata`（`fileId: "1MAikKhzILsMcyioyAzAOoDduwZ12HuviFy_JuAvnk_4"`）を呼び出す。

- 成功 → `✅ 送信先リストシート: 確認OK（[タイトル]）`
- 失敗 →
  ```
  ❌ 送信先リストシート: 見つかりません
  → 対処方法: 送信先を管理するスプレッドシートを作成し、URLをお知らせください
  ```

---

## Step 4 — 情報収集スプレッドシート確認（joho-shushu）

Drive MCPが接続されている場合のみ実行。

`get_file_metadata` または `read_file_content`（`fileId: "1JQ4RxnWQfYclBmvmVTrUDG67fHEITj6pOhJB1Ij_E3k"`）を呼び出す。

- 成功 → `✅ 情報収集スプレッドシート: 確認OK`
- 失敗 →
  ```
  ❌ 情報収集スプレッドシート: 見つかりません
  → 対処方法: 新しいスプレッドシートを作成し、「PRTimes」「フランチャイズ」シートを追加してください
  ```

---

## Step 5 — Slack 疎通テスト（オプション）

Slack MCPが接続されている場合のみ実行。ユーザーに確認を取ってから実施する。

```
Slack MCPが接続されています。
テストメッセージを自分のDMに送信してSlack連携を確認しますか？
（「はい」/「スキップ」でお答えください）
```

ユーザーが「はい」と答えた場合：

1. `slack_search_users`（`query: "info@next-group-hd.co.jp"`）でユーザーIDを取得
2. `slack_send_message` で以下を送信：
   ```
   🤖 Cowork スキル設定テスト
   Slack連携が正常に動作しています。
   このメッセージはセットアップスキルからの疎通確認です。
   ```
3. 送信成功 → `✅ Slack DM: 疎通確認OK（@[表示名] に送信しました）`
4. 送信失敗 → `❌ Slack DM: 送信失敗 — チャンネルへの投稿権限を確認してください`

---

## Step 6 — 設定サマリーをSlackへ送信

Slack MCPが接続されている場合のみ実行。**未接続の場合はこのStepをスキップし、サマリーをチャット上にそのまま出力する。**

以下のフォーマットでサマリーを作成し、`slack_send_message` で送信する。

```
🛠️ Cowork スキル初期設定サマリー
設定日: YYYY年MM月DD日

■ MCP接続状況
  Google Drive  : ✅ / ❌
  Gmail         : ✅ / ❌
  Google Calendar: ✅ / ❌
  Slack         : ✅ / ❌

■ Driveフォルダ設定
  市場調査レポート  : [ID] （新規作成 / 既存確認）
  競合調査レポート  : [ID] （新規作成 / 既存確認）
  ニュースまとめ    : [ID] （新規作成 / 既存確認）
  議事録           : [ID] （新規作成 / 既存確認）
  　└ 音声ファイル  : [ID] （新規作成 / 既存確認）
  情報収集         : [ID] （新規作成 / 既存確認）

■ テンプレート確認
  メールテンプレートDoc  : ✅ / ❌
  送信先リストシート     : ✅ / ❌
  情報収集スプレッドシート: ✅ / ❌

■ 対応が必要な項目
  （問題があった項目と対処方法をリストアップ）

■ 次のステップ
  ・フォルダIDに変更があった場合は skill-creator で各スキルを更新してください
  ・❌の項目を解消後、再度このスキルを実行して確認してください
```

### 送信先の決定

1. Step 5でSlack DM疎通テストを実施済みの場合は、同じ宛先（`info@next-group-hd.co.jp` のDM）にそのまま送信する。
2. Step 5を未実施・スキップした場合は、送信先を確認する：
   ```
   設定サマリーをSlackに送りたいのですが、送信先は自分のDMでよいですか？
   別のチャンネルがあればチャンネル名を教えてください。
   ```
3. DM宛先は `slack_search_users`（`query: "info@next-group-hd.co.jp"`）でユーザーIDを取得してから `slack_send_message` を呼ぶ。チャンネル指定がある場合は `slack_search_channels` でチャンネルIDを特定してから送信する。

- 送信成功 → `✅ 設定サマリー: Slackへ送信しました（[宛先]）`
- 送信失敗 → `❌ 設定サマリー: Slack送信失敗 — チャンネルへの投稿権限を確認してください`（このサマリーはチャット上にも表示する）

---

## Step 7 — 未導入スキルの提案・インストール

既存スキルが連携している業務領域（調査・議事録・カレンダー・Slack・メール等）に対して、まだ導入されていない便利なスキルやプラグインがないかを確認し、ユーザーに提案する。

1. `list_skills` を呼び出し、現在インストール済みのスキル一覧を取得する。
2. `suggest_skills` を呼び出し、ユーザーの業務内容（調査・営業・議事録・スケジュール管理など）に関連する未インストールのスキルがあるか確認する。
3. 既存スキルが参照している連携領域（Slack・Gmail・Calendar・スプレッドシート管理など）をカバーするプラグインが未導入の場合は `search_plugins` で検索する。
4. 提案できるスキル・プラグインが見つかった場合、一覧を提示してユーザーに確認を取る：
   ```
   📦 追加導入をおすすめするスキル
   ・[スキル名] — [説明を1行]
   ・[スキル名] — [説明を1行]

   インストールしますか？（導入したい項目を選んでください／スキップ可）
   ```
5. ユーザーが選択した項目について：
   - プラグイン内のスキルの場合 → `suggest_plugin_install` を呼び出してインストールを案内する
   - 単体スキルの場合 → インストール手順（Cowork設定 > スキル から追加 / `.skill` ファイルを保存）を案内する
6. 提案できるスキルが見つからない場合は `おすすめできる追加スキルは見つかりませんでした。` と報告してStep 8へ進む。

**範囲外:** 既存スキルの新規作成・改修はこのStepの対象外（`skill-creator` の領域）。ここで扱うのは公開済みスキル・プラグインの導入提案のみ。

---

## Step 8 — 完了メッセージ

```
✅ セットアップが完了しました！

[問題なし の場合]
全ての設定が完了し、スキルを使う準備ができています。

[問題あり の場合]
一部の設定に問題があります。上記の「対処方法」を参照して解消してください。
解消後にもう一度「初期設定をして」と言えば再確認できます。

設定サマリーをSlackに送信しました。（[宛先]）
[Slack未接続・送信失敗の場合は上記サマリーをこのまま参照してください]

[Step7で導入したスキルがあれば]
追加導入: [スキル名1] / [スキル名2]
```

---

## 共通ルール

- 各Stepは失敗しても次Stepへ進む（MCPが未接続の場合は関連Stepをスキップ）
- エラーが出た場合は必ず対処方法をユーザーに案内する
- フォルダIDの確認や作成は必ず実際のMCPツールで行い、推測で埋めない
- ユーザーの確認が必要な場合は必ず先に聞いてから実行する（特にSlackへの送信）
