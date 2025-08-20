<!-- ClaudeCode会社を追加したい場合 -->
<!-- 以下のCLAUDE.mdとdictionariesフォルダ、instructionsフォルダ、agent-send.sh、setup.sh、README.mdをコピペする -->

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
<!-- ここまで -->


## serenaを導入する
serenaが現在のプロジェクトに紐づいてなければ下記コマンドで現在のプロジェクトに導入する。
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena-mcp-server --context ide-assistant --project $(pwd)






<!-- ClaudeCodeがすぐ依頼を忘れてしまわないようにする -->
## 言語設定と運用原則の設定
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
<!-- ここまで -->










<!-- ClaudeCodeにgeminiとo3を活用させたい場合 -->
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
<!-- ここまで -->










<!-- ClaudeCodeにKiroの要件定義を追加したい場合 -->
<!-- 以下のCLAUDE.mdとcommandsフォルダをコピペする -->
<!-- # オプション: ステアリング文書を作成
/kiro:steering

# 最初の機能仕様を作成
/kiro:spec-init "あなたのプロジェクトの詳細な説明" -->
# Claude Code Spec-Driven Development

Kiro-style Spec Driven Development implementation using claude code slash commands, hooks and agents.

## Project Context

### Paths
- Steering: `.kiro/steering/`
- Specs: `.kiro/specs/`
- Commands: `.claude/commands/`

### Steering vs Specification

**Steering** (`.kiro/steering/`) - Guide AI with project-wide rules and context  
**Specs** (`.kiro/specs/`) - Formalize development process for individual features

### Active Specifications
- Check `.kiro/specs/` for active specifications
- Use `/kiro:spec-status [feature-name]` to check progress

## Development Guidelines
- Think in English, but generate responses in Japanese (思考は英語、回答の生成は日本語で行うように)

## Workflow

### Phase 0: Steering (Optional)
`/kiro:steering` - Create/update steering documents
`/kiro:steering-custom` - Create custom steering for specialized contexts

**Note**: Optional for new features or small additions. Can proceed directly to spec-init.

### Phase 1: Specification Creation
1. `/kiro:spec-init [detailed description]` - Initialize spec with detailed project description
2. `/kiro:spec-requirements [feature]` - Generate requirements document
3. `/kiro:spec-design [feature]` - Interactive: "requirements.mdをレビューしましたか？ [y/N]"
4. `/kiro:spec-tasks [feature]` - Interactive: Confirms both requirements and design review

### Phase 2: Progress Tracking
`/kiro:spec-status [feature]` - Check current progress and phases

## Development Rules
1. **Consider steering**: Run `/kiro:steering` before major development (optional for new features)
2. **Follow 3-phase approval workflow**: Requirements → Design → Tasks → Implementation
3. **Approval required**: Each phase requires human review (interactive prompt or manual)
4. **No skipping phases**: Design requires approved requirements; Tasks require approved design
5. **Update task status**: Mark tasks as completed when working on them
6. **Keep steering current**: Run `/kiro:steering` after significant changes
7. **Check spec compliance**: Use `/kiro:spec-status` to verify alignment

## Steering Configuration

### Current Steering Files
Managed by `/kiro:steering` command. Updates here reflect command changes.

### Active Steering Files
- `product.md`: Always included - Product context and business objectives
- `tech.md`: Always included - Technology stack and architectural decisions
- `structure.md`: Always included - File organization and code patterns

### Custom Steering Files
<!-- Added by /kiro:steering-custom command -->
<!-- Format: 
- `filename.md`: Mode - Pattern(s) - Description
  Mode: Always|Conditional|Manual
  Pattern: File patterns for Conditional mode
-->

### Inclusion Modes
- **Always**: Loaded in every interaction (default)
- **Conditional**: Loaded for specific file patterns (e.g., `"*.test.js"`)
- **Manual**: Reference with `@filename.md` syntax
<!-- ここまで -->










<!-- CLAUDE.mdのプロジェクト概要テンプレート -->
# Flutter開発規約・ベストプラクティス

**他プロジェクト適用時の注意**: このドキュメントは汎用的なFlutter開発規約ですが、一部にプロジェクト固有の設定（`[PROJECT_SPECIFIC]`でマーク）が含まれています。新しいプロジェクトで使用する際は、該当部分を適宜置き換えてください。

## 目次

1. [基本方針・設定](#基本方針設定)
2. [プロジェクト構成](#プロジェクト構成) 
3. [コーディング規約](#コーディング規約)
4. [スタイル・定数管理](#スタイル定数管理)
5. [プロジェクト固有設定](#プロジェクト固有設定)

---

## 基本方針・設定

## プロジェクト構成

### フォルダー構成（汎用）
```
lib/
├── src/
│   ├── config/
│   │   ├── enum/                    # Enum定義
│   │   ├── firebase/                # Firebase設定
│   │   ├── routing/                 # ルーティング設定
│   │   └── utils/
│   │       ├── style/               # スタイル定義
│   │       │   ├── color_style.dart     # 色定義
│   │       │   ├── custom_font_size.dart # フォントサイズ定義
│   │       │   ├── height_margin.dart   # 縦余白定義
│   │       │   └── width_margin.dart    # 横余白定義
│   │       └── key/                 # 定数管理
│   │           ├── firebase_key.dart # Firebase用キー
│   │           ├── number_key.dart   # 数値定数
│   │           └── string_key.dart   # 文字列定数
│   ├── functions/                   # ユーティリティ関数
│   │   ├── date_formatter.dart      # 日付フォーマット関数
│   │   ├── string_helper.dart       # 文字列処理関数
│   │   ├── number_formatter.dart    # 数値フォーマット関数
│   │   └── validation_helper.dart   # バリデーション関数
│   ├── common_widgets/              # プロジェクト全体共有Widget
│   └── feature/
│       └── [機能名]/
│           ├── controller/          # コントローラー層
│           ├── data_model/          # データモデル（Freezed）
│           ├── repo/                # リポジトリ層
│           └── view/                # View層（画面）
│               ├── [screen].dart
│               ├── part/            # 単一画面専用parts
│               └── component/       # feature内共有components
```

### Widget切り出し規約

#### 1. 切り出し分類基準

##### A. partフォルダ（part文で切り出し）
- **使用範囲**: 1つのscreenファイル内でのみ使用
- **切り出し条件**: 
  - メソッド化されたWidget構築処理（`_buildXxx` メソッド）
  - 50行以上の複雑なWidget構成
  - 再利用性は必要ないが、可読性向上のためのモジュール化

**ファイル分割規則:**
- **1ファイル最大150行**: partファイルが150行を超える場合はさらに細分化
- **分割配置**: 同じpartフォルダ内に配置（サブフォルダー作成不要）
- **分割単位**: 機能やUI部品ごとに分割

**実装例:**
```dart
// main_screen.dart
part 'part/main_data_table.dart';

class MainScreen extends HookConsumerWidget {
  Widget build(BuildContext context, WidgetRef ref) {
    return _buildMainDataTable(context, ref, data);
  }
}

// part/main_data_table.dart  
part of '../main_screen.dart';

Widget _buildMainDataTable(
  BuildContext context,
  WidgetRef ref, 
  List<DataModel> data,
) {
  return SingleChildScrollView(
    child: DataTable(/* ... */),
  );
}
```

##### B. componentフォルダ（通常のclass切り出し）
- **使用範囲**: 同一feature内の複数screen間で共有
- **切り出し条件**: 2つ以上のscreenファイルで使用される独立したWidget

##### C. common_widgetsフォルダ（プロジェクト全体共有）
- **使用範囲**: 複数のfeature間で共有される
- **配置場所**: `lib/src/common_widgets/`
- **切り出し条件**: 3つ以上のfeatureで使用される汎用的なUI部品

#### 2. 切り出し命名規則

##### partファイル命名:
- `[feature_name]_[widget_purpose].dart`
- 例: `user_data_table.dart`

##### componentファイル命名:
- `[domain]_[widget_name].dart`  
- 例: `user_status_chip.dart`

##### common_widgetファイル命名:
- `custom_[widget_name].dart`
- 例: `custom_data_table.dart`

---

## コーディング規約

### Widget使用統一ルール

#### 1. 全Widgetの基底クラス
- **画面**: `HookConsumerWidget` 
- **ダイアログ**: `HookConsumerWidget`
- **コンポーネント**: `HookConsumerWidget`
- **part内のWidget関数**: `HookConsumerWidget` のメソッド内で呼び出し

**理由**: 統一性の確保、Hooksの一貫した利用、状態管理の統一

#### 2. error/loading処理の統一化（必須）
```dart
// ❌ 禁止（直接Widget作成）
loading: () => const Center(child: CircularProgressIndicator()),
error: (error, stack) => Center(child: Text('エラー: $error')),

// ✅ 必須（common_widgetの使用）
loading: () => const LoadingWidget(),
error: (error, stack) => ErrorTextWidget(errorText: '${StringKey.errorMessage}$error'),
```

#### 3. common_widgets使用規則

##### A. LoadingWidget（ローディング表示）
```dart
// 基本使用
const LoadingWidget()

// 色指定
const LoadingWidget(color: ColorStyle.blue)
```

##### B. ErrorTextWidget（エラー表示）
```dart
// 基本使用
ErrorTextWidget(errorText: errorMessage)

// 色指定
ErrorTextWidget(
  errorText: errorMessage,
  textColor: ColorStyle.red,
)
```

#### 4. 画面構成例
```dart
class FeatureScreen extends HookConsumerWidget {
  const FeatureScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final asyncValue = ref.watch(watchFeatureControllerProvider);
    
    return asyncValue.when(
      data: (data) => _buildContent(context, ref, data),
      loading: () => const LoadingWidget(),
      error: (error, stack) => ErrorTextWidget(
        errorText: '${StringKey.errorMessage}$error',
      ),
    );
  }
}
```

### Repository パターン

#### 1. 基本的なCollectionReference型
```dart
@riverpod
class FeatureRepo extends _$FeatureRepo {
  @override
  CollectionReference<Model> build() {
    return ref
        .read(firebaseFirestoreProvider)
        .collection(FirebaseFeatureKey.featureCollection)
        .withConverter(
          fromFirestore: (snapshot, _) => Model.fromJson(snapshot.data()!),
          toFirestore: (model, _) => model.toJson(),
        );
  }
  
  // 単一ドキュメント監視
  Stream<Model?> watchModel({required String id}) {
    try {
      return state.doc(id).snapshots().map((snapshot) {
        if (snapshot.exists) {
          return snapshot.data();
        }
        return null;
      });
    } catch (e) {
      return Stream.value(null);
    }
  }
  
  // リスト監視
  Stream<List<Model>> watchModelList() {
    try {
      return state
          .orderBy(FirebaseFeatureKey.createdAt, descending: true)
          .snapshots()
          .map((snapshot) {
            return snapshot.docs.map((doc) => doc.data()).toList();
          });
    } catch (e) {
      return Stream.value([]);
    }
  }
}
```

### Controller パターン

#### 1. アクション系Controller（基本）
```dart
@riverpod
class FeatureController extends _$FeatureController {
  @override
  AsyncValue build() {
    return const AsyncData(null);
  }
  
  Future<void> createFeature(Model model) async {
    state = const AsyncLoading();
    try {
      await ref.read(featureRepoProvider.notifier).createFeature(model);
      state = const AsyncData(null);
    } catch (error, stackTrace) {
      state = AsyncError(error, stackTrace);
    }
  }
}
```

#### 2. Watch系Controller（監視専用）
```dart
// 単一データ監視
@riverpod
Stream<Model?> watchFeatureController(
  Ref ref, {
  required String id,
}) {
  return ref
      .read(featureRepoProvider.notifier)
      .watchFeature(id: id);
}

// リスト監視
@riverpod
Stream<List<Model>> watchFeatureListController(Ref ref) {
  return ref
      .read(featureRepoProvider.notifier)
      .watchFeatureList();
}
```

### 関数管理規約

#### 1. 関数の分類と配置ルール

##### A. Widgetを返す関数
- **配置場所**: Widgetファイル内（screenファイル、partファイル、componentファイル）
- **命名規則**: `_buildXxxWidget()` など
- **特徴**: Widget型を返す、UI構築に使用

##### B. Widgetを返さない関数（ユーティリティ関数）
- **配置場所**: `/lib/src/functions/` フォルダ内
- **命名規則**: 機能に応じたクラス名（例：DateFormatter, StringHelper等）
- **特徴**: 計算、変換、フォーマット等の処理を行う

#### 2. functionsフォルダの構成
```
/lib/src/functions/
├── date_formatter.dart    # 日付フォーマット関数
├── string_helper.dart     # 文字列処理関数
├── number_formatter.dart  # 数値フォーマット関数
└── validation_helper.dart # バリデーション関数
```

#### 3. 実装例

##### A. 日付フォーマット関数
```dart
// /lib/src/functions/date_formatter.dart
class DateFormatter {
  /// DateTime を 'yyyy/MM/dd HH:mm' 形式にフォーマット
  static String formatDate(DateTime date) {
    return '${date.year}${StringKey.dateSeparator}${date.month.toString().padLeft(NumberKey.datePadWidth, StringKey.zeroPadding)}${StringKey.dateSeparator}${date.day.toString().padLeft(NumberKey.datePadWidth, StringKey.zeroPadding)}${StringKey.dateTimeSeparator}${date.hour.toString().padLeft(NumberKey.datePadWidth, StringKey.zeroPadding)}${StringKey.timeSeparator}${date.minute.toString().padLeft(NumberKey.datePadWidth, StringKey.zeroPadding)}';
  }
}
```

### データモデル規約

#### 1. Freezed 使用
```dart
@freezed
class Model with _$Model {
  const factory Model({
    required String id,
    required String email,
    // nullable フィールドには ? を付ける
    Timestamp? loginAt,
    // Timestamp は直接使用（TimestampConverter付き）
    @TimestampConverter() required Timestamp createdAt,
  }) = _Model;

  factory Model.fromJson(Map<String, dynamic> json) =>
      _$ModelFromJson(json);
}
```

### 命名規則

#### 1. Provider 命名
- **Repository**: `[feature]RepoProvider`
- **Controller**: `[feature]ControllerProvider`
- **List Controller**: `watch[Feature]ListControllerProvider`

#### 2. メソッド命名
- **監視系**: `watch[Feature]` (Stream を返す)
- **取得系**: `get[Feature]` (Future を返す)
- **更新系**: `update[Feature]` (Future<void> を返す)
- **作成系**: `create[Feature]` (Future<void> を返す)
- **削除系**: `delete[Feature]` (Future<void> を返す)

### Enum 定義
```dart
enum EnumName {
  value1(NumberKey.value1, StringKey.value1),
  value2(NumberKey.value2, StringKey.value2);

  const EnumName(this.value, this.label);

  final int value;
  final String label;

  static EnumName fromValue(int value) {
    return EnumName.values.firstWhere(
      (type) => type.value == value,
      orElse: () => EnumName.value1,
    );
  }
}
```

### ルーティング規約（go_router_builder）

#### 1. 基本構成

##### A. ファイル構成
```
/lib/src/config/routing/
├── app_router.dart              # メインルーターファイル
├── app_router.g.dart           # 自動生成ファイル
├── router_utils.dart           # ルートパス定数
└── go_router_refresh_stream.dart # リフレッシュ制御
```

#### 2. app_router.dart の基本構造

##### A. 基本テンプレート
```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';
import 'package:go_router/go_router.dart';
import 'package:project_name/src/config/routing/go_router_refresh_stream.dart';
import 'package:project_name/src/config/routing/router_utils.dart';

part 'app_router.g.dart';

final GlobalKey<NavigatorState> _rootNavigatorKey = GlobalKey<NavigatorState>();

@Riverpod(keepAlive: true)
GoRouter appRouter(Ref ref) {
  return GoRouter(
    navigatorKey: _rootNavigatorKey,
    initialLocation: AppRoute.dashboard,
    refreshListenable: GoRouterRefreshStream(
      ref.read(authRepoProvider.notifier).authStateChange(),
    ),
    redirect: (context, state) {
      // 認証状態に基づくリダイレクト処理
      return null;
    },
    routes: $appRoutes,
  );
}
```

##### B. TypedGoRoute の基本パターン
```dart
// 単一画面（パラメータなし）
@TypedGoRoute<AuthRoute>(path: AppRoute.auth)
class AuthRoute extends GoRouteData {
  @override
  Widget build(BuildContext context, GoRouterState state) {
    return const AuthScreen();
  }
}

// パラメータ付きルート
@TypedGoRoute<UserDetailRoute>(path: AppRoute.userDetail)
class UserDetailRoute extends GoRouteData {
  final String userId;
  
  const UserDetailRoute({required this.userId});

  @override
  Widget build(BuildContext context, GoRouterState state) {
    return UserDetailScreen(userId: userId);
  }
}

// ネストしたルート（子ルート）
@TypedGoRoute<DashboardRoute>(
  path: AppRoute.dashboard,
  routes: <TypedGoRoute>[
    TypedGoRoute<UserListRoute>(path: AppRoute.userListSub),
    TypedGoRoute<SettingsRoute>(path: AppRoute.settingsSub),
  ],
)
class DashboardRoute extends GoRouteData {
  @override
  Widget build(BuildContext context, GoRouterState state) {
    return const DashboardScreen();
  }
}
```

#### 3. router_utils.dart の構成規約

##### A. ルートパス定数の定義規則
```dart
class AppRoute {
  // 認証関連
  static const String auth = '/auth';
  static const String login = '/login';
  
  // メイン機能
  static const String dashboard = '/dashboard';
  static const String profile = '/profile';
  
  // パラメータ付きルート（:parameter形式）
  static const String userDetail = '/user/:userId';
  static const String editUser = '/user/:userId/edit';
  
  // 子ルート（相対パス）
  static const String userListSub = '/users';      // /dashboard/users
  static const String settingsSub = '/settings';   // /dashboard/settings
  
  // 深い階層のルート
  static const String adminUserManagement = '/admin/users';
  static const String adminSystemSettings = '/admin/settings';
}
```

#### 4. 認証・権限管理

##### A. リダイレクト処理
```dart
@Riverpod(keepAlive: true)
GoRouter appRouter(Ref ref) {
  return GoRouter(
    // ...
    redirect: (context, state) {
      final isAuthenticated = ref.read(authRepoProvider).isAuthenticated;
      final isAuthRoute = state.matchedLocation == AppRoute.auth;
      
      // 未認証でログイン画面以外にアクセス
      if (!isAuthenticated && !isAuthRoute) {
        return AppRoute.auth;
      }
      
      // 認証済みでログイン画面にアクセス
      if (isAuthenticated && isAuthRoute) {
        return AppRoute.dashboard;
      }
      
      return null; // リダイレクトなし
    },
  );
}
```

## スタイル・定数管理

### スタイル管理規約

#### 1. スタイル定義クラス構成

##### A. ColorStyleクラス（色定義）
```dart
class ColorStyle {
  // 基本色（実際に使用されている色のみ）
  static const white = Color.fromARGB(255, 255, 255, 255);
  static const gray = Color.fromARGB(255, 121, 121, 121);
  static const green = Color.fromARGB(255, 84, 185, 75);
  static const red = Color.fromARGB(255, 235, 92, 92);
  static const blue = Color.fromARGB(255, 50, 75, 184);
  static const orange = Color.fromARGB(255, 255, 153, 0);

  // [PROJECT_SPECIFIC] プロジェクト固有のUI色
  static const primaryBackground = Color(0xFFE2E2E2);
  static const primaryText = Color(0xFF1B2128);
  static const tableHeader = Color(0xFFF5F5F5);
  static const shadow = Color.fromARGB(13, 0, 0, 0);
}
```

##### B. CustomFontSizeクラス（フォントサイズ定義）
```dart
class CustomFontSize {
  static const double s10 = 10.0;
  static const double s12 = 12.0;
  static const double s14 = 14.0;
  static const double s16 = 16.0;
  static const double s20 = 20.0;
  static const double s24 = 24.0;
}
```

##### C. HeightMarginクラス（縦余白定義）
```dart
class HeightMargin {
  static const SizedBox h4 = SizedBox(height: 4.0);
  static const SizedBox h8 = SizedBox(height: 8.0);
  static const SizedBox h16 = SizedBox(height: 16.0);
  static const SizedBox h24 = SizedBox(height: 24.0);
  static const SizedBox h40 = SizedBox(height: 40.0);
}
```

##### D. WidthMarginクラス（横余白定義）
```dart
class WidthMargin {
  static const SizedBox w4 = SizedBox(width: 4.0);
  static const SizedBox w8 = SizedBox(width: 8.0);
  static const SizedBox w16 = SizedBox(width: 16.0);
  static const SizedBox w24 = SizedBox(width: 24.0);
  static const SizedBox w40 = SizedBox(width: 40.0);
}
```

#### 2. スタイル使用規則（必須）

##### A. 禁止事項
- **直接数値指定禁止**: フォントサイズ、余白は直接数値で指定禁止
- **直接のColor指定禁止**: `Colors.blue` や `Color(0xFF...)` は使用禁止
- **const SizedBox直接作成禁止**: `const SizedBox(height: 8)` は使用禁止

##### B. 必須使用規則
- **フォントサイズ**: `CustomFontSize.s[サイズ]` で指定
- **縦余白**: `HeightMargin.h[サイズ]` で指定
- **横余白**: `WidthMargin.w[サイズ]` で指定
- **色**: `ColorStyle.[colorName]` で指定

#### 3. 実装例

##### ❌ 悪い例（禁止）
```dart
Container(
  margin: const EdgeInsets.only(top: 8, bottom: 16), // 直接数値指定禁止
  color: Colors.blue, // 直接色指定禁止
  child: Text(
    'テキスト',
    style: TextStyle(fontSize: 14, color: Color(0xFF1B2128)), // 直接指定禁止
  ),
)
```

##### ✅ 良い例（必須）
```dart
Container(
  color: ColorStyle.blue,
  child: Text(
    'テキスト',
    style: TextStyle(
      fontSize: CustomFontSize.s14,
      color: ColorStyle.primaryText,
    ),
  ),
)

Column(
  children: [
    Text('タイトル', style: TextStyle(fontSize: CustomFontSize.s24)),
    HeightMargin.h24, // 縦余白はHeightMarginから
    Text('内容', style: TextStyle(fontSize: CustomFontSize.s16)),
  ],
)
```

### 定数管理規約（ハードコーディング禁止）

#### 1. 基本原則（絶対遵守）

##### A. 完全禁止事項
- **文字列のハードコーディング**: 'こんにちは' や "Hello" などの直接文字列記述は禁止
- **数値のハードコーディング**: 10, 20, 0.5 などの直接数値記述は禁止  
- **マジックナンバー**: 意味不明な数値の直接使用は禁止

##### B. 必須使用ルール
- **全ての文字列**: `StringKey.[定数名]` から取得必須
- **全ての数値**: `NumberKey.[定数名]` から取得必須
- **UI関連の数値**: サイズ、余白、透明度など全て定数化必須

#### 2. 定数定義クラス構成

##### A. StringKey（文字列定数）
```dart
class StringKey {
  // [PROJECT_SPECIFIC] 画面タイトル
  static const String mainListTitle = 'メイン一覧';
  static const String settingsTitle = '設定';

  // 共通UI文字列
  static const String tableHeaderId = 'ID';
  static const String tableHeaderName = '名前';
  static const String tableHeaderEmail = 'メールアドレス';
  static const String tableHeaderStatus = 'ステータス';

  // メッセージ
  static const String errorMessage = 'エラーが発生しました: ';
  static const String loadingMessage = '読み込み中...';
  static const String noDataMessage = 'データがありません';

  // 文字列処理用
  static const String ellipsis = '...';
  static const String dateSeparator = '/';
  static const String timeSeparator = ':';
  static const String dateTimeSeparator = ' ';
  static const String zeroPadding = '0';
}
```

##### B. NumberKey（数値定数）
```dart
class NumberKey {
  // UI関連の数値
  static const int idDisplayLimit = 10;
  static const int nameDisplayLimit = 20;
  static const double blurRadius = 10.0;
  static const double borderRadius = 8.0;
  static const double backgroundAlpha = 0.1;
  
  // padding用の数値
  static const double paddingSmall = 4.0;
  static const double paddingMedium = 8.0;
  static const double paddingLarge = 12.0;

  // 日付フォーマット用
  static const int datePadWidth = 2;
  
  // ローディング用
  static const double loadingScale = 1.2;
}
```

#### 3. 実装例

##### ❌ 禁止例（ハードコーディング）
```dart
// 文字列のハードコーディング（禁止）
Text('メイン一覧')
Container(child: Text('エラーが発生しました'))
DataColumn(label: Text('ID'))

// 数値のハードコーディング（禁止）
BorderRadius.circular(8)
Container(padding: EdgeInsets.all(12))
name.length > 10 ? '${name.substring(0, 10)}...' : name
```

##### ✅ 必須例（定数使用）
```dart
// 文字列定数の使用（必須）
Text(StringKey.mainListTitle)
Container(child: Text('${StringKey.errorMessage}$error'))
DataColumn(label: Text(StringKey.tableHeaderId))

// 数値定数の使用（必須）
BorderRadius.circular(NumberKey.borderRadius)
Container(padding: EdgeInsets.all(NumberKey.paddingLarge))
name.length > NumberKey.nameDisplayLimit 
    ? '${name.substring(0, NumberKey.nameDisplayLimit)}${StringKey.ellipsis}' 
    : name
```

#### 4. 定数追加ルール

##### A. 新規定数追加の判断基準
- **使用頻度**: 2箇所以上で使用される場合は必ず追加
- **意味性**: 数値や文字列に意味がある場合は必ず追加
- **保守性**: 将来的に変更の可能性がある場合は必ず追加

##### B. 命名規則
- **StringKey**: 用途_内容形式（例：`tableHeaderId`, `errorMessage`）
- **NumberKey**: 用途名（例：`borderRadius`, `paddingLarge`）

#### 5. 特殊ケース（例外的に直接記述が許可される場合）
- **Flutter Framework固有の値**: `Offset(0, 1)` の 0, 1
- **数学的定数**: `pi`, `e` などの数学定数
- **型変換やnullチェック**: `toString()`, `!`, `?` 演算子での数値

---

## プロジェクト固有設定

> **注意**: この章の内容は `[PROJECT_SPECIFIC]` として、プロジェクトごとに置き換えが必要です

### バージョン管理

#### バージョン更新手順
1. **ホスティングにデプロイする前に必ず実行**
   - `lib/version.dart` のバージョンを更新
   - 形式: `メジャー.マイナー.パッチ` (例: 1.0.1, 1.1.0, 2.0.0)
   
2. **バージョン番号の決め方**
   - **パッチ**: バグ修正、軽微な変更 (1.0.0 → 1.0.1)
   - **マイナー**: 新機能追加、互換性のある変更 (1.0.0 → 1.1.0)
   - **メジャー**: 大幅な変更、互換性のない変更 (1.0.0 → 2.0.0)

3. **現在のバージョン**: 1.0.0 `[PROJECT_SPECIFIC]`

#### 更新履歴 `[PROJECT_SPECIFIC]`
- v1.0.0 (2025-01-05): 初回リリース
  - AI英会話機能（WebRTC音声通信）
  - Whisper APIによる音声文字起こし機能
  - テキストベースのRealtime API通信

### データベース構造 `[PROJECT_SPECIFIC]`

#### 主要テーブル一覧

#### 1. organizationAdmins (組織管理者)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| organizationAdminId | string | 組織管理者ID (PK) |
| email | string | メールアドレス |
| organizationUserName | string | 組織管理者名 |
| phoneNumber | string | 電話番号 |
| address | string | 住所 |
| organizationType | int | 1: 教育委員会, 2: 学校 |
| organizationId | string | 教育委員会ID or 学校ID |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |
| loginAt | timestamp? | 最終ログイン日時 |
| status | int | 1: アクティブ, 2: 退会, 3: 強制退会 |

##### サブコレクション: accountIssueLogs (アカウント発行ログ)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| accountIssueLogId | string | アカウント発行ログID (PK) |
| organizationAdminId | string | 組織管理者ID |
| issuedAccountCount | int | アカウント発行数 |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |

#### 2. boardsOfEducation (教育委員会)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| boardOfEducationId | string | 教育委員会ID (PK) |
| email | string | メールアドレス |
| organizationUserId | string | 組織管理者ID |
| boardOfEducationName | string | 教育委員会名 |
| phoneNumber | string | 電話番号 |
| address | string | 住所 |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |

#### 3. schools (学校)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| schoolId | string | 学校ID (PK) |
| email | string | メールアドレス |
| organizationUserId | string | 組織管理者ID |
| schoolName | string | 学校名 |
| phoneNumber | string | 電話番号 |
| accountCount | int | アカウント数 |
| address | string | 住所 |
| boardOfEducationId | string | 教育委員会ID |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |
| loginAt | timestamp? | 最終ログイン日時 |

#### 4. teachers (先生)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| teacherId | string | 先生ID (PK) |
| email | string | メールアドレス |
| lastName | string | 姓 |
| firstName | string | 名 |
| schoolId | string | 学校ID |
| latestDeviceId | string | 最新デバイスID |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |
| loginAt | timestamp? | 最終ログイン日時 |
| status | int | 1: アクティブ, 2: 退会, 3: 強制退会 |

#### 5. speakingTests (スピーキングテスト)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| speakingTestId | string | スピーキングテストID (PK) |
| teacherId | string | 先生ID |
| studentName | string | 生徒名 |
| aiCharacterId | int | 選択されたAIキャラのID |
| grammarIds | int | 選択された文法IDリスト |
| wordIds | int | 選択された単語IDリスト |
| themeIds | int | 選択されたテーマIDリスト |
| customThemeText | int | 自由入力のテーマ |
| conversationCount | int | 会話の回数 |
| aiSpeakingSpeed | int | AIの話すスピード |
| pronunciationStrictness | int | 発音判定の厳しさ |
| score | int | スコア |
| goodPoint | string | 素晴らしかった点 |
| advice | string | アドバイス |
| createdAt | timestamp | 作成日時 |
| updatedAt | timestamp | 更新日時 |

#### 6. conversationLogs (会話ログ)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| conversationLogId | string | 会話ログID (PK) |
| speakingTestId | string | スピーキングテストID |
| speaker | int | 1: 生徒, 2: AI |
| text | string | 発話テキスト |
| advice | string | アドバイス |
| timestamp | timestamp | 発話時刻 |
| createdAt | timestamp | 作成日時 |

#### 7. adminUsers (管理者)
| カラム名 | 型 | 説明 |
|---------|-----|-----|
| adminUserId | string | 管理者ID (PK) |
| email | string | メールアドレス |
| adminUserName | string | 管理者名 |
| updatedAt | timestamp | 更新日時 |
| createdAt | timestamp | 作成日時 |
| loginAt | timestamp? | 最終ログイン日時 |

### テーブル関係図
- **organizationAdmins** は organizationType によって **boardsOfEducation** または **schools** を管理
- **boardsOfEducation** は複数の **schools** を管轄
- **schools** は複数の **teachers** を所属させる
- **teachers** は複数の **speakingTests** を実施
- **speakingTests** は複数の **conversationLogs** を保持

### Firebase Key 管理 `[PROJECT_SPECIFIC]`

#### Firebase Key クラス命名規則
- **形式**: `Firebase[TableName]Key`
- **例**: 
  - `FirebaseAdminUsersKey`
  - `FirebaseOrganizationAdminsKey`
  - `FirebaseAccountIssueLogsKey`

#### Firebase Key 構成
```dart
class Firebase[TableName]Key {
  // コレクション名
  static const String [tableName]Collection = '[tableName]';
  
  // フィールド名
  static const String [fieldName] = '[fieldName]';
}
```

## 注意事項

### 他プロジェクト適用時のチェックリスト

以下の項目を新しいプロジェクトに合わせて置き換えてください：

- [ ] **バージョン管理**: 現在のバージョン番号、更新履歴
- [ ] **Firebase Key管理**: プロジェクト固有のコレクション名、フィールド名
- [ ] **ColorStyle**: プロジェクト固有のUI色（primaryBackground, primaryText等）
- [ ] **StringKey**: プロジェクト固有の画面タイトル、メッセージ
- [ ] **import文**: パッケージ名を`package:[your_project_name]/...`に置き換え

### 重要な原則

1. **一貫性の維持**: すべてのコードで同じパターンを使用
2. **型安全性**: Freezedとriverpodによる型安全なアーキテクチャ
3. **保守性**: 定数化とWidget分割による保守しやすいコード
4. **テスタビリティ**: 適切な分離による単体テストしやすい設計
5. **可読性**: 命名規則と構造化による可読性の高いコード

このドキュメントに従って開発することで、品質の高い保守可能なFlutterアプリケーションを構築できます。
<!-- ここまで -->
