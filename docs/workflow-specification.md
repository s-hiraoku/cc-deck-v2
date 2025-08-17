# CC-Deck v2 Workflow Specification

## 概要

CC-Deck v2は、**Spec（仕様）→ Impl（実装）→ Valid（検証）**のサイクルを繰り返すイテレーティブな開発ワークフローを提供します。各フェーズは明確な入出力と承認ゲートを持ち、AIとヒューマンの協調により高品質なソフトウェア開発を実現します。

## ワークフローアーキテクチャ

### 三層循環アーキテクチャ

```mermaid
graph TB
    Start([開始]) --> Spec[Spec: 仕様フェーズ]
    
    Spec --> SpecApproval{承認?}
    SpecApproval -->|Yes| Impl[Impl: 実装フェーズ]
    SpecApproval -->|No| Spec
    
    Impl --> ImplComplete{実装完了?}
    ImplComplete -->|Yes| Valid[Valid: 検証フェーズ]
    ImplComplete -->|No| Impl
    
    Valid --> ValidPass{検証合格?}
    ValidPass -->|Yes| Complete{開発完了?}
    ValidPass -->|No| Impl
    
    Complete -->|Next Iteration| Spec
    Complete -->|Complete| End([完了])
```

### フェーズ遷移と状態管理

```yaml
workflow_states:
  spec:
    phases:
      - init
      - requirements
      - design
      - tasks
      - approved
    transitions:
      - from: init
        to: requirements
        type: automatic
      - from: requirements
        to: design
        type: approval_required
      - from: design
        to: tasks
        type: approval_required
      - from: tasks
        to: approved
        type: approval_required
  
  impl:
    phases:
      - setup
      - tdd_cycles
      - integration
      - documentation
    transitions:
      - from: setup
        to: tdd_cycles
        type: automatic
      - from: tdd_cycles
        to: integration
        type: quality_gate
        conditions:
          coverage: ">= 95"
          lint_errors: "== 0"
          type_errors: "== 0"
      - from: integration
        to: documentation
        type: automatic
  
  valid:
    phases:
      - unit_tests
      - integration_tests
      - e2e_tests
      - performance
    transitions:
      - from: unit_tests
        to: integration_tests
        type: coverage_gate
        condition:
          coverage: ">= 95"
      - from: integration_tests
        to: e2e_tests
        type: automatic
      - from: e2e_tests
        to: performance
        type: automatic
```

## Specフェーズ（仕様）

### 目的
ユーザー要求を明確で実装可能な仕様に変換する

### サブフェーズ

#### 1. 初期化（spec-init）
- **入力**: プロジェクト名、機能名
- **処理**: ディレクトリ構造作成、初期ファイル生成
- **出力**: `specs/{feature-name}/`ディレクトリ構造

#### 2. 要求分析（spec-requirements）
- **入力**: ユーザー要求、既存コードベース参照
- **処理**: EARS+記法での要求定義、コンテキスト分析
- **出力**: `requirements.md`（構造化された要求仕様）
- **承認**: 必須（ヒューマンレビュー）

#### 3. 技術設計（spec-design）
- **入力**: 承認済み要求仕様
- **処理**: アーキテクチャ設計、技術選定、インターフェース定義
- **出力**: `design.md`（技術設計書）
- **承認**: 必須（ヒューマンレビュー）

#### 4. タスク分解（spec-tasks）
- **入力**: 承認済み設計書
- **処理**: 実装タスクの詳細分解、TDDシナリオ作成
- **出力**: `tasks.md`（実装タスクリスト）
- **承認**: 必須（ヒューマンレビュー）

### 品質基準
- 要求の明確性と検証可能性
- 設計の実現可能性と拡張性
- タスクの粒度と依存関係の明確化

## Implフェーズ（実装）

### 目的
承認済み仕様に基づく高品質な実装の作成

### サブフェーズ

#### 1. プロジェクトセットアップ
- **入力**: 承認済み仕様一式
- **処理**: プロジェクト構造生成、依存関係設定
- **出力**: 初期プロジェクト構造

#### 2. TDD実装サイクル
- **入力**: タスクリスト、テストシナリオ
- **処理**: Red→Green→Refactorサイクルの反復
- **出力**: テスト駆動による実装コード
- **品質ゲート**: 
  - テストカバレッジ95%以上
  - Lintエラー0
  - 型チェック合格

#### 3. 統合
- **入力**: 個別実装済みコンポーネント
- **処理**: コンポーネント統合、API接続
- **出力**: 統合済みアプリケーション

#### 4. ドキュメント生成
- **入力**: 実装コード、仕様書
- **処理**: APIドキュメント、使用ガイド生成
- **出力**: 完全なドキュメント

### 品質ゲート
- コードカバレッジ: 95%以上
- セキュリティスキャン: 脆弱性0
- パフォーマンス: 定義済み閾値以内
- アクセシビリティ: WCAG 2.1 AA準拠

## Validフェーズ（検証）

### 目的
実装の品質と仕様適合性の検証

### サブフェーズ

#### 1. ユニットテスト検証
- **入力**: 実装コード、テストスイート
- **処理**: 全ユニットテストの実行と検証
- **出力**: テストレポート
- **合格基準**: 全テスト合格、カバレッジ95%以上

#### 2. 統合テスト実行
- **入力**: 統合済みシステム
- **処理**: APIエンドポイント、データフロー検証
- **出力**: 統合テストレポート
- **合格基準**: 全エンドポイント正常動作

#### 3. E2Eテスト実施
- **入力**: 完全なアプリケーション
- **処理**: ユーザージャーニーの自動検証
- **出力**: E2Eテストレポート
- **合格基準**: クリティカルパス100%合格

#### 4. パフォーマンス検証
- **入力**: 本番相当環境
- **処理**: 負荷テスト、レスポンス測定
- **出力**: パフォーマンスレポート
- **合格基準**: SLA達成

### 検証失敗時の処理
- 失敗箇所の特定と原因分析
- Implフェーズへの自動フィードバック
- 修正タスクの自動生成

## イテレーション制御

### 反復条件
1. **機能追加**: 新規要求がある場合、Specフェーズから再開
2. **不具合修正**: Validフェーズで問題発見時、Implフェーズへ戻る
3. **改善**: パフォーマンスや品質改善のため、任意のフェーズから再開

### イテレーション管理
```yaml
iteration:
  number: 1
  type: feature  # feature | bugfix | improvement
  start_phase: spec  # spec | impl | valid
  context_inheritance:
    previous_decisions: true
    technical_constraints: true
    quality_metrics: true
```

## 状態管理とコンテキスト永続化

### ワークフロー状態ファイル
```
projects/{project-name}/
├── .workflow/
│   ├── state.yaml                    # 現在のワークフロー状態
│   ├── iteration-history.jsonl       # イテレーション履歴
│   └── context/
│       ├── decisions.yaml            # 重要な決定事項
│       ├── constraints.yaml          # 技術的制約
│       ├── metrics.yaml              # 品質メトリクス
│       ├── spec-context.poml         # Specフェーズコンテキスト
│       ├── impl-context.poml         # Implフェーズコンテキスト
│       ├── valid-context.poml        # Validフェーズコンテキスト
│       └── bridge/
│           ├── spec-to-impl.poml     # Spec→Implコンテキスト変換
│           ├── impl-to-valid.poml    # Impl→Validコンテキスト変換
│           └── valid-to-spec.poml    # Valid→Specコンテキスト変換
```

### POMLによるプロンプト構造化

#### POMLの役割
POMLは**個別エージェントのプロンプト定義**に使用し、プロンプトの品質と保守性を向上させます。エージェント間の調整はClaude Code Sub-Agent仕様で実装します。

#### 1. プロンプト構造化
各エージェントのプロンプトをPOML形式で構造化し、明確で保守しやすい指示を作成：

```xml
<!-- spec-context.poml -->
<poml>
  <role>You are a specification context manager for the current project feature</role>
  
  <task>
    Maintain and transfer specification context including requirements, design decisions, and constraints.
    Ensure all context is preserved and accessible for implementation phase.
  </task>
  
  <document src="specs/requirements.md" />
  <document src="specs/design.md" />
  <document src="context/constraints.yaml" />
  
  <output-format>
    Structure: {
      "requirements": {
        "functional": [...],
        "non_functional": [...],
        "acceptance_criteria": [...]
      },
      "design_decisions": {
        "architecture": "...",
        "tech_stack": [...],
        "patterns": [...]
      },
      "constraints": {
        "technical": [...],
        "business": [...],
        "timeline": "..."
      }
    }
  </output-format>
</poml>
```

#### 2. エージェント間連携
エージェント間の調整はClaude Code Sub-Agent仕様を使用し、POMLは各エージェントの個別プロンプトに集中：

```xml
<!-- spec-to-impl.poml -->
<poml>
  
  <role>You are a context bridge agent transforming specification context into implementation-ready context</role>
  
  <task>
    Transform the approved specification context into actionable implementation context.
    Map requirements to user stories, design to project structure, and constraints to development guidelines.
    Enrich with coding standards and testing frameworks.
  </task>
  
  <document src="context/spec-context.json" />
  <document src="steering/coding-standards.md" />
  <document src="steering/architecture.md" />
  
  <example>
    Input: {
      "requirements": {"functional": ["User login", "Data validation"]},
      "design": {"architecture": "MVC", "tech_stack": ["React", "Node.js"]}
    }
    Output: {
      "user_stories": ["As a user, I want to login securely"],
      "test_scenarios": ["Test successful login", "Test invalid credentials"],
      "project_structure": ["src/controllers/", "src/models/", "src/views/"],
      "dependencies": ["react@18", "express@4"]
    }
  </example>
  
  <output-format>
    Provide JSON with mapped implementation context:
    - Map requirements to user_stories and test_scenarios
    - Map design to project_structure and dependencies
    - Filter constraints to implementation-relevant items
    - Add coding_standards and testing_framework from project steering
  </output-format>
</poml>
```

#### 3. フィードバック分析
イテレーション間の学習を構造化されたプロンプトで管理：

```xml
<!-- valid-to-spec.poml (next iteration) -->
<poml>
  
  <role>You are an iteration feedback analyzer responsible for transferring validation insights to the next specification cycle</role>
  
  <task>
    Analyze validation results and provide actionable feedback for the next iteration.
    Focus on successful patterns, identify anti-patterns to avoid, and suggest concrete improvements.
    Ensure learning from current iteration informs better specifications.
  </task>
  
  <document src="context/valid-context.json" />
  <document src="context/metrics.yaml" />
  <document src="iteration-history.jsonl" />
  
  
  <example>
    Input: {
      "test_results": {"coverage": "98%", "performance": "slow"},
      "user_feedback": "UI responsive but data loading slow",
      "technical_debt": "Database queries not optimized"
    }
    Output: {
      "learned_patterns": ["High test coverage achieved with TDD"],
      "anti_patterns": ["Avoid N+1 database queries"],
      "recommendations": ["Add database indexing requirements", "Specify performance criteria"]
    }
  </example>
  
  <output-format>
    Provide structured feedback for the next iteration:
    {
      "learned_patterns": ["Successful approaches to replicate"],
      "anti_patterns": ["Approaches that failed or caused issues"],
      "performance_insights": ["Key performance learnings"],
      "quality_metrics": {"coverage": "X%", "defect_rate": "X"},
      "recommendations": ["Specific improvements for next spec"],
      "risk_mitigation": ["Identified risks and prevention strategies"]
    }
  </output-format>
</poml>
```

### エージェント調整戦略

#### Claude Code Sub-Agent仕様による調整
1. **メインオーケストレーター**: `Task`ツールでサブエージェント呼び出し
2. **状態管理**: YAML形式でワークフロー状態を永続化
3. **コンテキスト受け渡し**: ファイルベースのコンテキスト共有
4. **品質ゲート**: 各フェーズでの品質基準チェック

#### POMLとSub-Agentの使い分け
- **POML**: 個別エージェントのプロンプト品質向上
- **Sub-Agent**: エージェント間のワークフロー制御
- **YAML**: 設定と状態管理
- **ファイルシステム**: コンテキスト永続化

### 状態永続化メカニズム
```yaml
workflow_context:
  current_phase: impl
  current_subphase: tdd_cycles
  completed_tasks:
    - task-1
    - task-2
  pending_tasks:
    - task-3
    - task-4
  quality_metrics:
    coverage: 96.5
    lint_errors: 0
    type_errors: 0
  decisions:
    - phase: spec
      type: architecture
      decision: "Use Next.js App Router"
      rationale: "Better performance and DX"
```

## ワークフロー定義

### マスターオーケストレーター
```yaml
name: cc-deck-master
context:
  project: "${project_name}"
  feature: "${feature_name}"
  iteration: "${iteration_number}"

phases:
  - name: spec
    condition: "${phase == 'spec'}"
    agent: spec-generator
    approval:
      required: true
      type: human
    on_complete:
      next: impl
  
  - name: impl
    condition: "${phase == 'impl'}"
    agent: implementation
    quality_gate:
      coverage:
        minimum: 95
      lint_errors:
        maximum: 0
      type_errors:
        maximum: 0
    on_complete:
      next: valid
  
  - name: valid
    condition: "${phase == 'valid'}"
    agent: validation
    on_success:
      next: complete
    on_failure:
      next: impl

iteration_control:
  continue:
    condition: "${has_new_requirements}"
    reset_to: spec
    preserve_context: true
```

### エージェント定義（POML準拠）

エージェントへのプロンプト指示は**Microsoft POML仕様に準拠**した形式で記述し、Claude Codeが直接理解・実行できる形式とします：

#### POML準拠エージェント
- **spec-generator.poml**: 仕様生成エージェント
- **implementation.poml**: TDD実装エージェント  
- **validation.poml**: 検証エージェント
- **context-bridge.poml**: コンテキスト管理エージェント

#### 標準POML構造
```xml
<poml>
  <role>エージェントの役割とペルソナ</role>
  <task>具体的なタスクと要件</task>
  
  <!-- 外部データ参照 -->
  <document src="specs/requirements.md" />
  <document src="context/constraints.yaml" />
  
  <!-- 実例による指導 -->
  <example>
    Input: ユーザー認証機能の要求
    Output: EARS+記法による構造化要求仕様
  </example>
  
  <output-format>
    構造化された仕様ドキュメント（Markdown形式）
    - 明確な受け入れ基準
    - テスト可能な要求定義
    - 実装ガイドライン
  </output-format>
</poml>
```

#### POMLアプローチの利点
- **プロンプト品質**: 構造化された明確な指示
- **保守性**: モジュラーで再利用可能なプロンプト
- **一貫性**: 標準化されたフォーマット
- **データ統合**: 外部ファイルの自然な参照
- **Claude Code互換**: 直接実行可能な形式

#### 実装における制約
- POMLにはサブエージェント機能はない
- エージェント調整はClaude Code仕様で実装
- 複雑な変数展開は避け、シンプルな構造を維持
- ファイル参照は相対パスを使用

## 承認メカニズム

### 自動承認条件
- 軽微な変更（typo修正、ドキュメント更新）
- 95%以上のテストカバレッジを維持する変更
- セキュリティリスクがない変更

### ヒューマン承認必須条件
- アーキテクチャ変更
- 外部API統合
- セキュリティ関連実装
- パフォーマンスクリティカルな変更

## 成功指標

### 効率性メトリクス
- **サイクル時間**: Spec開始から Valid完了まで
- **イテレーション回数**: 品質達成までの反復回数
- **自動化率**: ヒューマン介入なしで完了したタスクの割合

### 品質メトリクス
- **欠陥密度**: 実装1000行あたりのバグ数
- **テストカバレッジ**: 全フェーズの平均カバレッジ
- **技術的負債**: 継続的な測定とトラッキング

## エラーハンドリング

### フェーズ失敗時の対応
1. **自動リトライ**: 一時的エラーの場合、3回まで自動再試行
2. **ロールバック**: 重大エラー時は前の安定状態へ
3. **エスカレーション**: 解決不能な問題はヒューマンへ

### リカバリーメカニズム
- チェックポイント自動保存（各サブフェーズ完了時）
- 部分的な成功の保持（完了したタスクは保存）
- コンテキスト完全復元（中断からの再開可能）

## 拡張ポイント

### カスタムフェーズの追加
- プラグインアーキテクチャによる新フェーズ追加
- 既存フェーズのカスタマイズ
- 業界固有のワークフロー対応

### 外部ツール統合
- CI/CDパイプライン連携
- プロジェクト管理ツール統合
- モニタリング・アラート連携

## まとめ

CC-Deck v2のワークフローは、Spec→Impl→Validの循環により、高品質なソフトウェアを効率的に開発します。各フェーズは明確な責任範囲を持ち、AIとヒューマンの最適な協調により、「時間のかかるワークフローを単一コマンドに」という目標を実現します。