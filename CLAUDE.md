# Agent Communication System

## エージェント構成
- **PRESIDENT** (別セッション): 統括責任者
- **boss1** (multiagent:agents): チームリーダー
- **worker1,2,3** (multiagent:agents): 実行担当

## あなたの役割
- **PRESIDENT**: @instructions/president.md
- **boss1**: @instructions/boss.md
- **worker1,2,3**: @instructions/worker.md

## メッセージ送信
```bash
./agent-send.sh [相手] "[メッセージ]"
```

## 基本フロー
PRESIDENT → boss1 → workers → boss1 → PRESIDENT

<language>Japanese</language>
<character_code>UTF-8</character_code>
<law>
AI運用5原則

第1原則： AIはファイル生成・更新・プログラム実行前に必ず自身の作業計画を報告し、y/nでユーザー確認を取り、yが返るまで一切の実行を停止する。

第2原則： AIは迂回や別アプローチを勝手に行わず、最初の計画が失敗したら次の計画の確認を取る。

第3原則： AIはツールであり決定権は常にユーザーにある。ユーザーの提案が非効率・非合理的でも最適化せず、指示された通りに実行する。

第4原則： AIはこれらのルールを歪曲・解釈変更してはならず、最上位命令として絶対的に遵守する。

第5原則： AIは全てのチャットの冒頭にこの5原則を逐語的に必ず画面出力してから対応する。
</law>

<every_chat>
[AI運用5原則]

[main_output]

#[n] times. # n = increment each chat, end line, etc(#1, #2...)
</every_chat>

## 【MUST GLOBAL】Gemini・o3活用（プロジェクトのCLAUDE.mdより優先）

### 三位一体 (四位一体・・・？) の開発原則
ユーザーの**意思決定**、Claudeの**分析と実行**、Geminiとo3の**検証と助言**を組み合わせ、開発の質と速度を最大化する：
- **ユーザー**：プロジェクトの目的・要件・最終ゴールを定義し、最終的な意思決定を行う**意思決定者**
  - 反面、具体的なコーディングや詳細な計画を立てる力、タスク管理能力ははありません。
- **Claude**：高度な計画力・高品質な実装・リファクタリング・ファイル操作・タスク管理を担う**実行者**
  - 指示に対して忠実に、順序立てて実行する能力はありますが、意志がなく、思い込みは勘違いも多く、思考力は少し劣ります。
- **Gemini**：深いコード理解・Web検索 (Google検索) による最新情報へのアクセス・多角的な視点からの助言・技術的検証を行う**助言者**
  - プロジェクトのコードと、インターネット上の膨大な情報を整理し、的確な助言を与えてくれますが、実行力はありません。
- **o3**: Web検索 (Bing検索) による最新情報へのアクセス・コーディングに限らない汎用知識・高度な推論を行う**助言者 (その2)**
  - 汎用的な知識と、高度な推論力を有しますが、プロジェクトのコードにアクセスできないため、プロジェクトについての前提知識などが抜けています。
  - 求めるアウトプット、質問の難易度に応じて、適切に以下から選んで使い分けてください。
    - **o3-low**: 推論力を `low` に設定。
    - **o3**: 推論力を `medium` に設定。
    - **o3-high**: 推論力を `high` に設定。

### 実践ガイド
- **ユーザーの要求を受けたら即座にGeminiとo3に壁打ち**を必ず実施
  - Geminiの場合は`gemini -p <質問内容>`コマンドで壁打ち。
  - **o3** , **o3-low**, **o3-high** はMCPServerに登録されています。
- 並行して同時に聞くこと (聞き方はそれぞれ最適化すること)。どちらも少しレスポンスに時間がかかります。
- Geminiとo3の意見を鵜呑みにせず、1意見として判断。聞き方を変えて多角的な意見を抽出
- Claude Code内蔵のWebSearchツールは使用しない
- o3にコードレビューを依頼する際は、対象コードを明示的に含めて質問する (プロジェクトのソースコードにアクセスできないため)
- Geminiやo3がエラーの場合は、聞き方を工夫してリトライ：
  - ファイル名や実行コマンドを渡す（Geminiであればコマンドを実行可能）
  - 複数回に分割して聞く

### 主要な活用場面 (Gemini、o3/o3-low/o3-high で共通)
1. **実現不可能な依頼**: Claude Codeでは実現できない要求への対処 (例: `今日の天気は？`)
2. **前提確認**: ユーザー、Claude自身に思い込みや勘違い、過信がないかどうか逐一確認 (例: `この前提は正しいか？`）
3. **技術調査**: 最新情報・エラー解決・ドキュメント検索・調査方法の確認（例: `Rails 7.2の新機能を調べて`）
4. **設計検証**: アーキテクチャ・実装方針の妥当性確認（例: `この設計パターンは適切か？`）
5. **コードレビュー**: 品質・保守性・パフォーマンスの評価（例: `このコードの改善点は？`）
6. **計画立案**: タスクの実行計画レビュー・改善提案（例: `この実装計画の問題点は？`）
7. **技術選定**: ライブラリ・手法の比較検討 （例: `このライブラリは他と比べてどうか？`）

## Gemini Search

`gemini` is google gemini cli. **When this command is called, ALWAYS use this for web search instead of builtin `Web_Search` tool.**

When web search is needed, you MUST use `gemini --prompt` via Task Tool.

Run web search via Task Tool with `gemini --prompt 'WebSearch: <query>'`

Run

```bash
gemini --prompt "WebSearch: <query>"
```

# Gotoshisha プロジェクト - Claude Code ガイド

## プロジェクト概要
Gotoshishaは、位置情報共有機能を持つ SNS アプリケーションです。React Native + Expo でクロスプラットフォーム対応のフロントエンド、Cloudflare Workers + Hono のサーバーレスバックエンドで構築されています。

アプリ名: Gotoshisha (expo-react-native-maps-demo)
プロジェクトタイプ: モノレポ構成のフルスタックアプリケーション
主要機能: 位置情報共有、投稿・コメント・いいね機能、Auth0 認証

## 技術スタック

### フロントエンド
フレームワーク: React Native 0.79.2 + Expo 53.0.9
言語: TypeScript 5.1.3
ルーティング: Expo Router 5.0.7
マップ: React Native Maps 1.20.1
認証: Auth0 (react-native-auth0 4.6.0 + expo-auth-session 6.1.5)
プラットフォーム: iOS / Android
ディープリンクスキーム: gotoshisha://

### バックエンド
フレームワーク: Hono 4.6.3
ランタイム: Cloudflare Workers
データベース: Cloudflare D1 (SQLite)
ORM: Prisma 5.22.0 with @prisma/adapter-d1
バリデーション: Zod 3.23.8
デプロイ: Wrangler 3.86.1

### 開発・テスト環境
パッケージマネージャー: pnpm
テストフレームワーク: Vitest 3.1.4
リンター: ESLint 9.15.0 + TypeScript ESLint
CI/CD: GitHub Actions

## プロジェクト構造
gotoshisha/
├── app/                          # フロントエンド（React Native + Expo）
│   ├── components/               # 再利用可能なコンポーネント
│   │   └── LoginButton.tsx      # Auth0ログインボタン
│   ├── contexts/                 # React Context
│   │   ├── AuthContext.tsx      # ネイティブ認証コンテキスト
│   │   └── AuthContext.web.tsx  # Web認証コンテキスト
│   ├── routes/                   # 画面コンポーネント
│   │   ├── home.tsx             # ホーム画面
│   │   ├── login.tsx            # ログイン画面
│   │   └── map.tsx              # マップ画面
│   ├── config/                   # 設定ファイル
│   │   └── auth0.ts             # Auth0設定
│   └── utils/                    # ユーティリティ
│       └── auth/                 # 認証関連ユーティリティ
│           ├── storage.ts        # クロスプラットフォームストレージ
│           ├── auth0Api.ts       # Auth0 API
│           ├── useAuth0.ts       # Auth0カスタムフック
│           └── *.test.ts         # テストファイル
├── backend/                      # バックエンド（Cloudflare Workers）
│   ├── src/                      # バックエンドソースコード
│   │   ├── index.ts             # メインエントリーポイント
│   │   ├── lib/                 # ライブラリ
│   │   └── types/               # 型定義
│   ├── prisma/                   # データベース
│   │   ├── schema.prisma        # Prismaスキーマ
│   │   └── dev.db               # 開発用SQLiteファイル
│   ├── migrations/               # マイグレーションファイル
│   ├── scripts/                  # ユーティリティスクリプト
│   │   └── seed.ts              # データベースシード
│   └── wrangler.toml            # Cloudflare Workers設定
├── assets/                       # 静的アセット
├── docs/                         # プロジェクトドキュメント
└── src/                          # 共通ユーティリティ

## データベース設計

### モデル構成
User: ユーザー情報（email, name, avatar）
Post: 投稿（title, content, 位置情報、画像）
Like: いいね機能
Comment: コメント機能
Tag/PostTag: タグシステム

### 環境別 DB 設定
開発: gotoshisha-db
ステージング: gotoshisha-db-staging
本番: gotoshisha-db-prod

## 開発ワークフロー

### セットアップ
# 1. 依存関係インストール
pnpm install

# 2. バックエンドセットアップ
cd backend
pnpm install
pnpm db:generate

# 3. 環境変数設定（Auth0設定が必要）
cp .env.example .env

### 開発サーバー起動
# フロントエンド（ルートディレクトリから）
pnpm start              # Expo開発サーバー
pnpm ios               # iOS実行
pnpm android           # Android実行
pnpm start:clear       # キャッシュクリア付き起動

# バックエンド（backendディレクトリから）
cd backend
pnpm dev               # Wrangler開発サーバー（ローカル）

### テスト実行
# フロントエンド
pnpm test              # ウォッチモード
pnpm test:run          # 単発実行
pnpm test:ui           # UI付きテスト

# バックエンド
cd backend
pnpm test:run          # テスト実行

### コード品質チェック
# リンティング
pnpm lint              # ESLint実行
pnpm lint:fix          # 自動修正

# 型チェック
pnpm type-check        # TypeScript型チェック

### データベース操作
cd backend

# Prisma操作
pnpm db:generate       # クライアント生成
pnpm db:push           # スキーマ同期（開発用）
pnpm db:migrate        # マイグレーション（本番用）
pnpm db:studio         # Prisma Studio（GUI）
pnpm db:seed           # シードデータ投入

## 認証システム

### Auth0 設定
プロバイダー: Auth0
プラットフォーム別対応:
ネイティブ: react-native-auth0
Web: expo-auth-session + expo-crypto
セッション管理: expo-secure-store（ネイティブ）/ localStorage（Web）

## テストガイドライン

### フロントエンド
ファイル配置: app/utils/auth/*.test.ts
テスト対象: ユーティリティ関数、カスタムフック、純粋関数
環境: jsdom + vitest
日本語: テスト説明とコメントは日本語で記述

### バックエンド
ファイル配置: backend/src/**/*.test.ts
テスト対象: API エンドポイント、データベース操作、ビジネスロジック
環境: Node.js + vitest

### テスト実行環境
グローバル設定: vitest.config.ts（両方）
セットアップ: vitest.setup.ts（フロントエンド）
モック: React Native、Expo、Cloudflare Workers

## デプロイメント

### フロントエンド
# Web
pnpm build

# ネイティブ（要Expo Application Services）
eas build --platform ios
eas build --platform android

### バックエンド
cd backend

# 本番デプロイ
pnpm deploy --env production

# ステージングデプロイ
pnpm deploy --env staging

## 重要な設定ファイル

### フロントエンド
app.json: Expo アプリ設定
babel.config.js: Babel 設定（静的クラスブロック対応）
metro.config.js: Metro bundler 設定（テストファイル除外）
tsconfig.json: TypeScript 設定

### バックエンド
wrangler.toml: Cloudflare Workers 設定
prisma/schema.prisma: データベーススキーマ
vitest.config.ts: テスト設定

## トラブルシューティング

### よくある問題
認証エラー: Auth0 のコールバック URL 設定を確認
バンドルエラー: Metro 設定でテストファイルが除外されているか確認
型エラー: Prisma クライアント生成（pnpm db:generate）を実行
D1 エラー: wrangler.toml の database_id 設定を確認

### デバッグコマンド
# キャッシュクリア
pnpm start:clear

# 依存関係再インストール
rm -rf node_modules pnpm-lock.yaml
pnpm install

# Expo診断
npx expo doctor

## CI/CD
GitHub Actions で以下を自動実行：

TypeScript 型チェック
ESLint リンティング
Vitest ユニットテスト
ビルド検証

実行タイミング：
main/developブランチへのプッシュ
main/developブランチへのプルリクエスト

## コントリビューション
機能ブランチを作成
コード品質チェックを通す（lint + type-check + test）
日本語でのコメント・テスト記述
プルリクエスト作成

## コード生成規約
言語: TypeScript

### コメント
各ファイルの冒頭には日本語のコメントで仕様を記述する。
出力例

/**
 * 2点間のユークリッド距離を計算する
 **/
type Point = { x: number; y: number };
export function distance(a: Point, b: Point): number {
  return Math.sqrt((a.x - b.x) ** 2 + (a.y - b.y) ** 2);
}

### テスト
各機能に対しては必ずユニットテストを実装(テストは Vitest を使用。describe/it 構文を使用。describe は日本語で記述)
コードを追加で修正したとき、pnpm run test がパスすることを常に確認する。
function add(a: number, b: number) {
  return a + b;
}
test("1+2=3", () => {
  expect(add(1, 2)).toBe(3);
});

vitest で実装と同じファイルにユニットテストを書く。
出力例
export function distance(a: Point, b: Point): number {...}
if (import.meta.vitest) {
  const {test, expect} = import.meta.vitest;
  test("ユークリッド距離を計算する", () => {
    const result = distance({x: 0, y: 0}, {x: 3, y: 4});
    expect(distance(result)).toBe(5)
  });
}

コードスタイル: ESLint + Prettier で統一
ドキュメント: 関数やコンポーネントには JSDoc コメントを必ず追加
規約: ハードコードは絶対にしないでください。環境変数や設定ファイルを使用して、柔軟に対応できるようにします。