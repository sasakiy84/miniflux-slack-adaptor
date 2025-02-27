# Miniflux Slack アダプター

このプロジェクトは、セルフホストの Miniflux API から RSS
フィードを取得し、言語モデル (LLM) を使用してフィルタリングおよび要約し、Slack
に通知を送信する Slack アプリです。

## プロジェクト概要

Miniflux Slack アダプターは、LLM
の力を活用してフィードの内容をフィルタリングおよび要約し、Slack
でユーザーに通知することで、さまざまな RSS
フィードからの情報を効率的に管理することを目的としています。このアプリは、タイムリーな情報を必要とするチームに特に役立ちます。

## 機能

- **RSS フィードの取得**: セルフホストの Miniflux API に接続して RSS
  フィードを取得します。
- **フィルタリングと要約**:
  言語モデルを使用してフィードの内容をフィルタリングおよび要約します。
- **Slack 通知**: 要約された内容を指定された Slack チャンネルに送信します。

具体的な処理の流れは、以下の通りです。

`FEED_FETCH_INTERVAL_SECOND`（デフォルトでは 60s）おきにフィードを確認する。
取得したフィードに対して、Slack
から取得したチャンネルの情報をもとにして、通知するチャンネルを LLM
に投げて判定する。 チャンネルは、説明欄に `for_miniflux_slack_adaptor`
という文字列があるチャンネルが通知の対象であり、
目的欄に振り分けのカテゴリが書いてある。
この一連の処理の中にプラグインの形式で回答内容を変換する処理を挟むことができ、好みに応じてフィルタリングや要約などの処理を入れることができる。

## アーキテクチャ

### 主なコンポーネント

- **SourceProvider**: フィードエントリを取得するための抽象インターフェース。
  - **MinifluxSourceProvider**: `SourceProvider` を実装し、Miniflux
    からエントリを取得するクラス。
- **Notifier**: 通知を送信するための抽象インターフェース。
  - **SlackNotifier**: `Notifier` を実装し、Slack に通知を送信するクラス。
- **Plugin**: フィードエントリを変換するためのプラグインの型。
  - 例: `SlackDataEntryTransformer` は、フィードエントリを Slack
    通知用に変換するプラグインです。

### データの流れ

1. **SourceProvider** がデータを取得し、`DataEntry`
   オブジェクトの配列として返します。
2. 取得した `DataEntry`
   オブジェクトは、必要に応じてプラグインによって変換されます。
3. 変換された `DataEntry` オブジェクトは、**Notifier** によって通知されます。

### 現在の実装クラス

- **MinifluxSourceProvider**: Miniflux API からデータを取得するクラス。
- **SlackNotifier**: Slack に通知を送信するクラス。
- **DataEntry**: フィードエントリのデータ構造を定義するインターフェース。
- **Plugin**: フィードエントリを変換するためのプラグインの型。

## 前提条件

- API アクセスが可能なセルフホストの Miniflux インスタンス。
- アプリの作成と管理が可能な Slack ワークスペース。
- コンテンツのフィルタリングと要約のための言語モデル API へのアクセス。

## セットアップ手順

```bash
npm install
cp .env.sample .env # and set environment variables
npm start
```

## 使用方法

アプリが実行されると、Miniflux から定期的に RSS フィードを取得し、LLM
を使用して処理し、要約された内容を指定された Slack チャンネルに送信します。

## ライセンス / License

このプロジェクトは MIT ライセンスの下でライセンスされています。

This project is licensed under the MIT License.
