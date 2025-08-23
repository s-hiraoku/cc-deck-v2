# ハイブリッドエージェントアーキテクチャ設計書

## 概要

CC-Deck v2のエージェントシステムにおいて、**静的な構造定義**と**動的なプロンプト生成**を分離することで、効率的で保守性の高いワークフロー制御を実現する。

### 基本原則
- **静的部分（.md）**: エージェントの基本構造、ロール、ツール定義
- **動的部分（.poml）**: プロジェクト固有のプロンプトテンプレート、条件分岐、変数展開

## アーキテクチャ構成

### 1. ファイル構成

```
cc-deck-v2/
├── .claude/
│   └── agents/
│       ├── spec-agent.md          # 静的: エージェント定義
│       └── impl-agent.md          # 静的: エージェント定義
├── poml/
│   ├── commands/
│   │   └── orchestrator.poml      # 動的: 統一オーケストレーター
│   └── agents/
│       ├── spec-generator.poml    # 動的: 仕様生成エージェント挙動
│       └── implementation.poml    # 動的: 実装エージェント挙動
└── projects/
    └── {project-name}/
        └── .workflow/
            ├── context.poml        # 動的: プロジェクト固有コンテキスト
            └── variables.json      # 動的: 実行時変数
```

### 2. 静的部分：エージェント定義（.md）

```markdown
---
name: spec-agent
description: Specification generation agent
model: sonnet
tools: [Read, Write, Edit, LS]
---

# Specification Agent

## Core Responsibilities
[不変のエージェントロール定義]

## Execution Pattern
[標準的な実行パターン]

## Context Loading
Load dynamic prompt from: `.workflow/context.poml`
```

**特徴**:
- エージェントの基本的な振る舞いを定義
- プロジェクトに依存しない共通ロジック
- ツールセットと基本設定

### 3. 動的部分：プロンプトテンプレート（.poml）

```xml
<!-- spec-prompt.poml -->
<poml>
  <let name="project" value="{{PROJECT_NAME}}" />
  <let name="language" value="{{LANGUAGE}}" />
  <let name="framework" value="{{FRAMEWORK}}" />
  
  <role>
    You are working on {{project}} using {{language}} and {{framework}}.
  </role>
  
  <task>
    <p if="phase == 'spec'">Create comprehensive specifications.</p>
    <p if="phase == 'impl'">Implement based on specifications.</p>
  </task>
  
  <context>
    <document src="projects/{{project}}/README.md" optional="true" />
    <document src=".workflow/context/previous-result.json" optional="true" />
  </context>
  
  <output-format>
    Generate structured output with status, files created, and next steps.
  </output-format>
</poml>
```

**特徴**:
- プロジェクト固有の設定値
- 実行時に変更可能な変数
- ワークフロー状態の保持

## 統合メカニズム

### 1. オーケストレーターによる統一制御

CC-Deck v2では、現在の統一orchestratorアーキテクチャに基づいて、POML behavior.pomlファイルを活用した動的挙動制御を実装します：

#### 統一Orchestrator実行フロー
```bash
# 自然言語コマンド例
/orchestrator create specification for fintech-app user-authentication with enterprise compliance

# 実行フロー:
1. プロンプト解析 → project: fintech-app, feature: user-authentication, behavior: enterprise-compliance
2. behavior.poml読み込み → poml/agents/development-styles/enterprise-development.poml
3. サブエージェント呼び出し → spec-generator with enterprise-compliance behavior
4. 結果統合と状態更新 → spec.json更新、次アクション提案
```

#### POML Behavior制御の実装
現在のアーキテクチャでは、個別エージェントがPOMLファイルを直接読み込み、挙動を動的に変更します：

```markdown
# .claude/agents/spec-generator.md実行例

## Process
1. Always load POML behavior template if provided: poml/agents/{behavior}.poml
2. Apply behavior modifications from POML
3. Execute tasks based on POML priorities and constraints
4. Generate output in POML-specified format

## Enterprise Compliance Behavior Example
When behavior=enterprise-development:
- Load: poml/agents/development-styles/enterprise-development.poml
- Apply: Enterprise quality standards, comprehensive documentation
- Output: Detailed specifications with compliance mapping
```

### 2. POML Behavior Template Integration

#### Enterprise Development Behavior (enterprise-development.poml)
```xml
<poml>
  <role>Enterprise-grade specification generator with strict compliance requirements</role>
  
  <task>
    Generate comprehensive specifications that meet enterprise standards including:
    - Detailed security requirements and threat modeling
    - Comprehensive compliance documentation (SOX, GDPR, HIPAA as applicable)
    - Enterprise architecture alignment
    - Risk assessment and mitigation strategies
  </task>
  
  <constraints>
    <quality-gates>
      <security>Complete security analysis required</security>
      <compliance>Regulatory compliance mapping mandatory</compliance>
      <architecture>Enterprise architecture review required</architecture>
    </quality-gates>
  </constraints>
  
  <output-format>
    Generate enterprise-grade specification with:
    - Executive summary with business impact
    - Detailed security analysis and threat model
    - Compliance requirements mapping
    - Risk assessment matrix
    - Enterprise architecture integration plan
  </output-format>
</poml>
```

### 3. 現在アーキテクチャとの整合

#### Orchestrator-Mediated Workflow
```yaml
# 現在の実行フロー
execution_flow:
  1. unified_orchestrator: "/orchestrator [natural language]"
  2. prompt_analysis: "Extract project, command, behavior parameters"
  3. behavior_loading: "Load specified behavior.poml if provided"
  4. subagent_invocation: "Call appropriate subagent with behavior instructions"
  5. result_integration: "Integrate results and update state"
```

#### Static vs Dynamic Separation (Current Implementation)
- **Static (.claude/agents/*.md)**: Basic agent definitions and Claude Code compliance
- **Dynamic (poml/agents/*.poml)**: Behavior modification templates (existing files)
- **Orchestrator (poml/commands/orchestrator.poml)**: Unified command processing and routing

## 実装例

### 1. 静的エージェント定義

```markdown
# .claude/agents/spec-agent.md
---
name: spec-agent
model: sonnet
tools: [Read, Write, Edit]
---

# Specification Agent

## Context Integration
Read project-specific prompt from `.workflow/context.poml`:
- Project name, language, framework
- Current feature being developed
- Quality requirements

## Execution
1. Load TOML configuration
2. Apply project-specific rules
3. Generate specifications based on context
```

### 2. 動的コンテキスト

```xml
<!-- projects/test-project/.workflow/context.poml -->
<poml>
  <workflow>
    <phase>spec</phase>
    <feature>user-authentication</feature>
    <iteration>1</iteration>
  </workflow>
  
  <requirements>
    <format>EARS+</format>
    <include-diagrams>true</include-diagrams>
    <detail-level>comprehensive</detail-level>
  </requirements>
  
  <tech-stack>
    <language>python</language>
    <framework>fastapi</framework>
    <database>postgresql</database>
    <testing>pytest</testing>
  </tech-stack>
</poml>
```

### 3. 状態管理

```json
// config/workflow-state.json
{
  "execution": {
    "last_run": "2025-01-17T12:00:00Z",
    "last_agent": "spec-agent",
    "status": "success"
  },
  "phases": {
    "spec": "completed",
    "impl": "in_progress",
    "valid": "pending"
  },
  "context_files": {
    "spec_result": ".workflow/context/spec-result.json",
    "impl_result": ".workflow/context/impl-result.json"
  }
}
```

## 利点

### 1. 明確な責任分離
- **静的（.md）**: "何をするか"を定義
- **動的（.poml）**: "どのようにするか"をテンプレート化

### 2. 再利用性
- エージェント定義は複数プロジェクトで共有可能
- プロジェクト固有の設定は独立管理

### 3. 保守性
- エージェントロジックの変更は.mdファイルのみ
- プロジェクト設定の変更は.tomlファイルのみ

### 4. 可読性
- POMLのセマンティックタグでプロンプト構造を明確化
- Markdownで人間が理解しやすいエージェント定義

## 実装ガイドライン

### 1. POML仕様準拠の実装

CC-Deck v2では、Microsoft POML仕様に準拠したテンプレートシステムを採用：

```xml
<!-- Microsoft POML仕様準拠のテンプレート例 -->
<poml>
  <role>Specification generator with project-specific behavior</role>
  
  <task>
    Generate high-quality specifications based on project requirements.
    Apply behavior-specific modifications as needed.
  </task>
  
  <document src="projects/{{project}}/project.json" />
  <document src="specs/{{feature}}/requirements.md" optional="true" />
  
  <output-format>
    Generate structured specification documents with:
    - Clear requirements in EARS+ notation
    - Technical design with architecture diagrams
    - Implementation tasks with TDD approach
  </output-format>
</poml>
```

### 2. Claude Code環境での動作

現在のアーキテクチャでは、Claude Codeが直接POMLファイルを読み込み、解釈します：

```bash
# Claude Codeでの実行例
# 1. Orchestratorがbehavior指定を解析
/orchestrator review fintech-app user-auth with strict security

# 2. 該当するPOMLファイルを特定
behavior_file: poml/agents/review-styles/strict-security-review.poml

# 3. Sub-agentに挙動指示を渡す
Task(subagent_type="code-reviewer", 
     prompt="Review code with strict-security behavior applied...",
     behavior_template="strict-security-review.poml")
```

### 3. pomljsライブラリの役割と制限

#### 実際の機能範囲
- ✅ **POML → テキスト変換**: `read()` + `write()` または `poml()`で可能
- ❌ **テキスト → POML変換**: サポートなし（テキストはプレーンテキストとして処理）
- ✅ **POML構文解析**: POMLマークアップの構造化処理

#### CC-Deck v2での実装方針
```javascript
// POMLファイルからプロンプトテキスト生成のみ使用
import { poml } from 'pomljs';

// behavior.pomlを読み込み、プロンプトテキストに変換
const behaviorText = await poml(behaviorPomlContent);
// Claude Codeがこのテキストをsub-agentに提供
```

### 4. 実装時の考慮事項

#### POML処理の制限事項
- **単方向変換のみ**: POML → テキストのみサポート
- **変数展開制限**: シンプルな変数置換のみ
- **条件分岐簡素化**: 基本的なif条件のみサポート
- **ファイル参照**: 相対パスでの外部ファイル参照

### 5. 現在の実装方針との整合

CC-Deck v2では、以下の方針で統合アーキテクチャを実装：

#### 責任分離の明確化
```yaml
# 責任分離
responsibilities:
  orchestrator: "プロンプト解析、ルーティング、状態管理"
  sub_agent: "専門領域タスク実行、品質管理"
  poml_behavior: "挙動制御、出力フォーマット調整"
  
# 現在の実装
current_architecture:
  static_agents: ".claude/agents/*.md (Claude Code準拠)"
  behavior_templates: "poml/agents/*.poml (既存ファイル)"
  unified_orchestrator: "poml/commands/orchestrator.poml"
  state_management: "spec.json based project state"
```

## ベストプラクティス

### 1. 現在アーキテクチャでの実装指針
- **Orchestrator統一**: 全コマンドを`/orchestrator`経由で実行
- **自然言語解析**: パラメータベースから自然言語プロンプト解析への移行
- **POML制限遵守**: Microsoft POML仕様に厳格準拠
- **Claude Code準拠**: Task()ツール制限、独立性確保

### 2. POML使用ガイドライン
- **セマンティックタグのみ**: `<role>`, `<task>`, `<output-format>`等
- **シンプルな変数展開**: `{{project}}`, `{{feature}}`レベル
- **外部ファイル参照**: `<document src="..."/>`による構造化データ取得

### 3. エージェント統合時の注意点
- **Behavior読み込み確認**: POMLファイル存在チェック必須
- **デフォルト挙動保証**: Behaviorなしでも正常動作
- **エラーハンドリング**: POML解析失敗時の適切な処理

## 現在の実装状況と今後の計画

### 現在の状況
- **Orchestrator統一**: 基本アーキテクチャ設計完了
- **4コアSub-agent**: spec-generator, code-reviewer, implementation, validation
- **POML behavior**: 既存テンプレートファイル群
- **状態管理**: spec.jsonベースの進捗管理

### 実装優先度
```yaml
priority_1:
  - orchestrator.pomlの完成
  - 4コアsub-agentのPOML behavior統合
  - 基本的な自然言語コマンド処理

priority_2:
  - より多くのbehavior.pomlテンプレート
  - エラーハンドリング強化
  - プロジェクトコンテキスト管理

priority_3:
  - 高度なプロンプト解析
  - 複数プロジェクト並列サポート
  - パフォーマンス最適化
```

## 人間承認との統合

### POML Behaviorにおける承認対応
```xml
<poml>
  <role>Specification generator with approval-aware output formatting</role>
  
  <task>
    Generate specifications that require human approval before proceeding.
    Clearly mark approval requirements and provide approval commands.
  </task>
  
  <constraints>
    <approval-required>
      <deliverable>requirements.md</deliverable>
      <deliverable>design.md</deliverable>
      <deliverable>tasks.md</deliverable>
    </approval-required>
  </constraints>
  
  <output-format>
    End all specification deliverables with:
    
    ## 🔍 Human Approval Required
    
    This deliverable requires human approval before proceeding to the next phase.
    
    **Approval Command**:
    ```bash
    /orchestrator approve {{deliverable_type}} for {{project}} {{feature}}
    ```
    
    **Next Steps**: Implementation phase will be available after approval.
  </output-format>
</poml>
```

### Behavior Template統合での承認制御
現在のアーキテクチャでは、各sub-agentがbehavior.pomlを読み込み時に、承認要件も同時に適用：

```yaml
approval_integration:
  spec_generator:
    - 成果物生成時に承認要件を自動付与
    - spec.json承認状況の確認と更新
    - 承認コマンドの具体的提示
  
  code_reviewer:
    - 重大問題発見時の承認保留推奨
    - セキュリティ問題の承認前修正要求
  
  implementation:
    - 品質ゲート未通過時の承認ブロック
    - テストカバレッジ不足時の実装承認延期
  
  validation:
    - 最終承認のための検証結果整理
    - ステークホルダー承認準備
```

## まとめ

CC-Deck v2のハイブリッドアーキテクチャは、現在の統一orchestrator設計と完全に整合し：

- **統一インターフェース**: `/orchestrator`による自然言語コマンド処理
- **動的挙動制御**: 既存behavior.pomlテンプレートによる柔軟な実行制御
- **明確な責任分離**: orchestrator（調整）+ sub-agent（実行）+ POML（挙動制御）
- **人間承認統合**: 成果物生成時の自動承認要件適用
- **拡張性**: 新しいbehaviorテンプレートの追加による機能拡張

このアプローチにより「時間のかかるワークフローを単一コマンドに」という目標を実現し、人間承認による品質制御を維持しながら、AI-native開発プラットフォームとして高い価値を提供します。

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-01-17  
**ステータス**: 設計承認待ち