# Orchestrator 実行フロー詳細

## 1. 概要

このドキュメントは、CC-Deck v2 の `/orchestrator` コマンドの実行フローを段階的に説明します。orchestrator は自然言語入力を解析し、適切なサブエージェントにタスクを委譲する統一インターフェースです。

## 2. 実行フロー全体像

### 2.1 フロー図

```
[User Input]
    ↓
[orchestrator.md 読み込み]
    ↓
[orchestrator.poml 実行]
    ↓
[プロジェクト確認] ← 対話的（必要時）
    ↓
[パラメータ抽出]
    ↓
[spec.json 確認]
    ↓
[フェーズ検証]
    ↓
[コマンドルーティング]
    ↓
[挙動テンプレート適用]
    ↓
[サブエージェント呼び出し]
    ↓
[タスク実行]
    ↓
[結果出力・状態更新]
```

### 2.2 主要コンポーネント

| コンポーネント    | ファイルパス                                   | 役割                                                                     |
| ----------------- | ---------------------------------------------- | ------------------------------------------------------------------------ |
| orchestrator.md   | `.claude/commands/orchestrator.md`             | エントリーポイント、初期処理                                             |
| orchestrator.poml | `poml/commands/orchestrator.poml`              | メインロジック、パラメータ解析                                           |
| spec.json         | `projects/{project}/specs/{feature}/spec.json` | プロジェクト状態管理 (Orchestrator が指定し、サブエージェントで読み込む) |
| behavior POMLs    | `poml/agents/{category}/{behavior}.poml`       | 挙動修正テンプレート                                                     |
| サブエージェント  | `.claude/agents/{agent}.md`                    | 実際のタスク実行                                                         |

## 3. 詳細ステップ

### Step 1: コマンド起動

```bash
User: /orchestrator review authentication module with strict security analysis
```

**処理内容:**

- ユーザーが `/orchestrator` コマンドを実行
- システムが `.claude/commands/orchestrator.md` を読み込み
- 自然言語指示を `user_prompt` として受け取る

### Step 2: POML テンプレート読み込み

**orchestrator.md の処理:**

```markdown
1. **Always load and follow**: poml/commands/orchestrator.poml
```

**重要:** POML テンプレートが最優先で実行される

### Step 3: 変数初期化

**orchestrator.poml での初期化:**

```xml
<let name="user_prompt" value="review authentication module with strict security analysis" />
<let name="command" value="" />
<let name="target" value="" />
<let name="behavior" value="" />
<let name="project" value="" />
<let name="feature" value="" />
```

### Step 4: プロンプト解析

#### 4.1 プロジェクト特定（最重要）

```xml
<prompt-analysis>
  **CRITICAL - Project Identification**
  - プロンプトから抽出: 見つからない
  - 対話的確認が必要
</prompt-analysis>
```

#### 4.2 コマンド識別

- "review" → `command = "review"`

#### 4.3 ターゲット抽出

- "authentication module" → `target = "authentication module"`

#### 4.4 挙動テンプレート識別

- "strict security" → `behavior = "strict-security-review"`

#### 4.5 フィーチャー推測

- "authentication" → `feature = "user-authentication"`

### Step 5: プロジェクト確認ダイアログ

**プロジェクト不明時の対話:**

```
System: "どのプロジェクトで作業しますか？
         利用可能なプロジェクト:
         1. fintech-app
         2. e-commerce-platform
         3. blog-system"

User: "fintech-app"
```

**結果:** `project = "fintech-app"`

### Step 6: プロジェクトコンテキスト読み込み

**spec.json の読み込み:**

```json
// projects/fintech-app/specs/user-authentication/spec.json
{
  "project": "fintech-app",
  "feature": "user-authentication",
  "phase": "design-approved",
  "approvals": {
    "requirements": {
      "generated": true,
      "approved": true
    },
    "design": {
      "generated": true,
      "approved": true
    },
    "tasks": {
      "generated": false,
      "approved": false
    }
  },
  "progress": {
    "requirements": 100,
    "design": 100,
    "tasks": 0
  }
}
```

### Step 7: フェーズ検証

**検証処理:**

```xml
<project-context-check>
  - Current phase: "design-approved"
  - Requested command: "review"
  - Validation: ✓ レビューは任意のフェーズで実行可能
</project-context-check>
```

**フェーズ制約の例:**

- `implement` → 設計承認済みが必要
- `approve` → 対象が生成済みが必要
- `continue` → 現在フェーズから次を判断

### Step 8: コマンドルーティング

```xml
<command-routing>
  <p if="command == 'review'">
    Route to code-reviewer sub-agent for code analysis.
  </p>
</command-routing>
```

**ルーティング決定:** `code-reviewer` サブエージェント

### Step 9: 挙動テンプレート適用

**POML 挙動テンプレート読み込み:**

```xml
<behavior-integration>
  Load: poml/agents/review-styles/strict-security-review.poml
</behavior-integration>
```

**適用される挙動修正:**

- 優先順位: セキュリティ脆弱性検出
- 制約: ゼロトレランス
- アプローチ: 脅威モデリングベース
- 出力: セキュリティ監査レポート形式

### Step 10: サブエージェント実行

**サブエージェント呼び出し:**

```markdown
> Use the code-reviewer subagent to review authentication module
> for fintech-app project with strict-security-review behavior applied
```

**code-reviewer.md の処理:**

1. POML 挙動テンプレート適用
2. プロジェクトコンテキスト考慮
3. セキュリティ重視のレビュー実行

### Step 11: 結果生成と状態更新

**生成される出力:**

```markdown
## セキュリティ監査レポート

プロジェクト: fintech-app
モジュール: authentication
フェーズ: design-approved
挙動: strict-security-review

### 検出された問題

🔴 Critical (2 件):

- SQL injection vulnerability (auth.js:45)
- Weak password hashing algorithm

🟡 High (3 件):

- Missing rate limiting
- Session token issues
- CSRF protection absent

### 推奨事項

1. bcrypt ラウンド数を 12 以上に
2. Rate limiting 実装
3. CSRF token 実装

### 次のアクション

タスク生成が必要です: /orchestrator generate tasks for fintech-app user-auth
```

**状態更新（必要に応じて）:**

```json
// spec.jsonへの記録
{
  "lastReview": {
    "date": "2024-01-20",
    "type": "security",
    "issues": 5
  }
}
```

## 4. 実行例

### 例 1: セキュリティレビュー（プロジェクト確認あり）

```bash
# 入力
User: /orchestrator review auth module with security focus

# プロジェクト確認
System: どのプロジェクトで作業しますか？
User: fintech-app

# 実行
System: [セキュリティレビュー実行]

# 結果
System: セキュリティ監査完了。5件の問題を検出。
```

### 例 2: 継続コマンド

```bash
# 入力
User: /orchestrator continue fintech-app

# フェーズ確認
System: fintech-app は design-approved フェーズです。

# 次アクション提案
System: 次のアクション:
        1. タスク生成 (/orchestrator generate tasks)
        2. 実装開始 (/orchestrator implement)
```

### 例 3: 承認コマンド

```bash
# 入力
User: /orchestrator approve design for fintech-app user-auth

# 承認処理
System: 設計を承認中...

# spec.json更新
System: 設計が承認されました。実装フェーズに進めます。
```

### 例 4: フェーズ制約エラー

```bash
# 入力
User: /orchestrator implement fintech-app user-auth

# フェーズチェック
System: [spec.json確認: design.approved = false]

# エラー
System: エラー: 設計が未承認です。
        先に '/orchestrator approve design for fintech-app user-auth' を実行してください。
```

## 5. エラーハンドリング

### 5.1 プロジェクト不明

```
入力: /orchestrator review something
処理: プロジェクト抽出失敗
出力: "どのプロジェクトで作業しますか？ [プロジェクト一覧]"
```

### 5.2 フェーズ不適合

```
入力: /orchestrator implement [未承認プロジェクト]
処理: 承認チェック失敗
出力: "設計承認が必要です。承認コマンドを実行してください。"
```

### 5.3 挙動テンプレート不存在

```
入力: /orchestrator review with unknown-behavior
処理: テンプレート読み込み失敗
出力: "挙動テンプレート 'unknown-behavior' が見つかりません。
       デフォルト挙動で実行します。"
```

### 5.4 サブエージェント不明

```
入力: /orchestrator unknown-command
処理: コマンド識別失敗
出力: "不明なコマンドです。利用可能: review, implement, spec, continue, approve"
```

## 6. POML チェーン

### 6.1 チェーン構造

```
orchestrator.poml
    ↓ (変数引き継ぎ)
behavior.poml (optional)
    ↓ (挙動修正)
sub-agent
```

### 6.2 変数の流れ

```xml
<!-- orchestrator.poml -->
<let name="project" value="fintech-app" />
<let name="behavior" value="strict-security-review" />
    ↓
<!-- strict-security-review.poml -->
<priorities>security-focused</priorities>
    ↓
<!-- code-reviewer.md -->
Apply POML-defined priorities and constraints
```

### 6.3 コンテキスト管理

各段階で以下が引き継がれる：

- プロジェクト情報
- フィーチャー情報
- 現在フェーズ
- 承認状況
- 挙動設定

## 7. 状態管理

### 7.1 spec.json の役割

```json
{
  "phase": "現在のワークフローフェーズ",
  "approvals": "各フェーズの承認状況",
  "progress": "各フェーズの進捗率",
  "context": "プロジェクト固有のコンテキスト",
  "history": "過去の作業履歴"
}
```

### 7.2 フェーズ遷移

```
requirements-generated
    ↓ (approve)
requirements-approved
    ↓ (generate)
design-generated
    ↓ (approve)
design-approved
    ↓ (generate)
tasks-generated
    ↓ (approve)
tasks-approved
    ↓ (implement)
implementation-in-progress
    ↓ (test)
testing-complete
    ↓ (deploy)
deployed
```

### 7.3 承認ゲート

各フェーズで承認が必要：

- 要件承認 → 設計可能
- 設計承認 → タスク生成可能
- タスク承認 → 実装可能

## 8. 重要な設計原則

### 8.1 プロジェクト必須

- すべての操作にプロジェクトコンテキストが必要
- プロジェクト不明時は必ず確認
- プロジェクトごとに独立した状態管理

### 8.2 フェーズ駆動

- 現在フェーズが次のアクションを決定
- フェーズスキップ防止
- 順序立てた開発プロセス

### 8.3 承認ゲート準拠

- 重要な決定には人間の承認が必要
- 自動承認なし（セキュリティ）
- 承認履歴の記録

### 8.4 挙動の動的変更

- POML テンプレートで実行時に挙動変更
- 同じコマンドでも異なる実行方法
- プロジェクトやフェーズに応じた最適化

## 9. トラブルシューティング

### 9.1 POML が読み込まれない

**原因:** ファイルパスの誤り
**解決:** poml/commands/ と poml/agents/ の構造確認

### 9.2 パラメータ抽出失敗

**原因:** キーワード不足
**解決:** より明確な指示、または対話的確認

### 9.3 サブエージェント実行エラー

**原因:** 必要なツール不足
**解決:** サブエージェントの tools 設定確認

### 9.4 状態不整合

**原因:** spec.json の破損
**解決:** spec.json の手動修復または再生成

## 10. behavior.poml管理戦略

### 10.1 既存ファイルアプローチ

**設計方針**: behavior.pomlファイルは予め用意された既存のテンプレートファイルを使用（将来的には動的生成も検討）

```
poml/agents/
├── review-styles/
│   ├── strict-security-review.poml    # セキュリティ重視レビュー
│   ├── performance-focused.poml       # パフォーマンス重視
│   └── accessibility-audit.poml       # アクセシビリティ監査
├── development-styles/
│   ├── rapid-prototyping.poml         # 高速プロトタイピング
│   ├── enterprise-development.poml    # エンタープライズ開発
│   └── test-driven-development.poml   # TDD重視開発
└── output-formats/
    ├── technical-report.poml          # 技術レポート形式
    ├── security-audit-report.poml     # セキュリティ監査レポート
    └── implementation-guide.poml      # 実装ガイド形式
```

### 10.2 責任分離

#### Orchestratorの責任
1. **識別**: ユーザープロンプトからbehavior名を抽出
2. **ファイル指定**: 適切なbehavior.pomlファイルパスを決定
3. **指示**: サブエージェントに読み込むべきファイルを指示

#### サブエージェントの責任
1. **読み込み**: Orchestratorから指示されたbehavior.pomlファイルを読み込み
2. **適用**: POMLで定義された挙動修正を実際に適用
3. **実行**: 修正された挙動でタスクを実行

### 10.3 実行フロー例

```xml
<!-- orchestrator.poml内での処理 -->
<behavior-integration>
  <p if="behavior == 'strict-security-review'">
    Load behavior template: poml/agents/review-styles/strict-security-review.poml
    Apply behavioral modifications to the selected sub-agent
  </p>
</behavior-integration>
```

```markdown
<!-- orchestratorからcode-reviewerへの指示 -->
> Use the code-reviewer subagent to review authentication module
> for fintech-app project with strict-security-review behavior applied
> Load: poml/agents/review-styles/strict-security-review.poml
```

```xml
<!-- strict-security-review.poml (サブエージェントが読み込む) -->
<poml>
  <priorities>
    1. Security vulnerability detection (highest priority)
    2. Authentication/authorization flaws
    3. Input validation issues
    4. Cryptographic implementation review
  </priorities>
  
  <constraints>
    - Zero tolerance for security issues
    - OWASP Top 10 compliance required
    - Threat modeling approach mandatory
  </constraints>
  
  <output-format>
    Generate security audit report with:
    - Risk level classification (Critical/High/Medium/Low)
    - CVSS scoring where applicable
    - Concrete remediation steps
    - Code examples for fixes
  </output-format>
</poml>
```

### 10.4 設計合理性

#### 既存ファイル使用の理由
1. **安定性**: 実行時エラーの削減
2. **パフォーマンス**: ファイル生成時間の不要
3. **品質保証**: 事前検証済みテンプレート
4. **再利用性**: 複数プロジェクトでの共通挙動
5. **メンテナンス性**: 一箇所での挙動定義管理

#### Dynamic生成との比較
| 項目 | 既存ファイル | Dynamic生成 |
|------|-------------|-------------|
| 実行速度 | ⚡ 高速 | 🐌 生成時間必要 |
| 安定性 | ✅ 安定 | ⚠️ 生成エラーリスク |
| カスタマイズ | 🔧 事前定義 | 🎨 無制限 |
| 品質保証 | ✅ 検証済み | ❓ 実行時検証 |

### 10.5 将来の拡張計画

#### Phase 1: MVP (現在)
- 事前定義されたbehavior.pomlファイルのみ
- 基本的な挙動パターン（5-10種類）
- 静的ファイル参照システム

#### Phase 2: カスタマイズ
- プロジェクト固有のbehavior.poml作成機能
- 既存テンプレートからの派生とカスタマイズ
- バリデーション機能付きエディタ

#### Phase 3: Dynamic生成
- リアルタイムでのbehavior.poml生成
- コンテキスト適応型挙動作成
- 機械学習による最適挙動提案

### 10.6 behavior.poml管理のベストプラクティス

#### ファイル命名規則
```
{category}-{specific-behavior}.poml

例:
- review-styles/strict-security-review.poml
- development-styles/rapid-prototyping.poml
- output-formats/technical-report.poml
```

#### 品質保証
1. **構文検証**: POML仕様準拠チェック
2. **論理検証**: 矛盾する指示の検出
3. **実行テスト**: 各behaviorの動作確認
4. **文書化**: 各behaviorの目的と使用場面の明記

#### バージョン管理
- 全behaviorファイルをGit管理
- セマンティックバージョニング
- 変更履歴の追跡
- 後方互換性の保証

## 11. まとめ

orchestrator は以下の流れで動作します：

1. **自然言語入力** を受け取る
2. **プロジェクト特定** を最優先で行う
3. **パラメータ抽出** で意図を理解
4. **フェーズ検証** で実行可能性確認
5. **適切なサブエージェント** にルーティング
6. **挙動テンプレート** で実行方法調整（既存ファイル使用）
7. **結果生成** と **状態更新**

この設計により、自然で柔軟、かつ堅牢なワークフロー管理が実現されています。behavior.pomlファイルの既存ファイルアプローチにより、安定性とパフォーマンスを確保しながら、将来のカスタマイズ要求にも対応可能な拡張性を持っています。
