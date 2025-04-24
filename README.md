# AWS Lambda TypeScript テンプレート

TypeScriptで書かれたAWS Lambda関数のテンプレートプロジェクトです。esbuildを使用してバンドルし、GitHub Actionsによる自動デプロイを設定しています。

## 特徴

- **TypeScript対応**: 型安全なLambda関数の開発が可能
- **esbuildによる高速バンドル**: 高速なビルドプロセス
- **AWS SDK統合**: S3, DynamoDB用のSDKが含まれています
- **GitHub Actions**: 自動デプロイワークフローが設定済み
- **最小限の設定**: すぐに使い始められるシンプルな構成

## 前提条件

- Node.js (v18以上推奨)
- AWSアカウント
- AWS CLI (設定済み)
- GitHub Actions（自動デプロイを使用する場合）

## プロジェクト構成

```
aws-lambda-typescript/
├── .github/workflows/   # GitHub Actions ワークフロー定義
│   └── deploy.yml       # デプロイワークフロー
├── lambda/              # Lambda関数のソースコード
│   └── index.ts         # メインのLambdaハンドラー
├── package.json         # プロジェクト依存関係と構成
├── tsconfig.json        # TypeScript設定
└── .gitignore           # Git除外ファイル設定
```

## セットアップ

### 1. リポジトリをクローン

```bash
git clone <リポジトリURL>
cd aws-lambda-typescript
```

### 2. 依存関係のインストール

```bash
npm install
```

### 3. 開発

`lambda/index.ts` を編集して、必要なLambda機能を実装します。

## ビルド

以下のコマンドでLambda関数をビルドします：

```bash
npm run build
```

このコマンドは以下を実行します：
1. `dist` ディレクトリをクリーンアップ
2. esbuildを使用してTypeScriptをバンドル
3. 生成されたファイルをzip形式にパッケージング (`dist/package.zip`)

## デプロイ

### 手動デプロイ

ビルド後、AWS CLIを使用して手動でデプロイできます：

```bash
aws lambda update-function-code \
  --function-name <あなたのLambda関数名> \
  --zip-file fileb://dist/package.zip
```

### GitHub Actionsによる自動デプロイ

このテンプレートには、`develop` ブランチへのプッシュ時に自動的にデプロイするGitHub Actionsワークフローが含まれています。

設定するには：

1. GitHub上でリポジトリの「Settings > Secrets」に以下のシークレットを追加：
   - `AWS_ROLE_ARN`: デプロイに使用するIAMロールのARN

2. `.github/workflows/deploy.yml` 内の `lambda-function-name` を実際のLambda関数名に変更してください。

## カスタマイズ

### 依存関係の追加

Lambda関数で追加のnpmパッケージを使用する場合：

```bash
npm install --save 使いたいパッケージ名
```

### Lambda関数の変更

`lambda/index.ts` ファイルを編集し、必要なロジックを実装します。AWS SDKモジュールは依存関係として含まれているため、以下のように簡単にインポートできます：

```typescript
import { S3Client, GetObjectCommand } from '@aws-sdk/client-s3';
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient, PutCommand } from '@aws-sdk/lib-dynamodb';
```

## 注意事項

- AWS SDKクライアントは外部依存として扱われ（`--external:@aws-sdk/*`）、Lambda実行環境で提供されるものを使用します
- Node.js 22のランタイムを対象としています
- AWSリソースに対するアクセス権限は、Lambda関数のIAMロールで適切に設定してください
