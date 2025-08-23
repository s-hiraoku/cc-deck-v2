# POML、Orchestration、Sub-agentの関係性ドキュメント

## 1. 概要

このドキュメントでは、CC-Deck v2プラットフォームにおけるPOML（Prompt Orchestration Markup Language）、Orchestration（オーケストレーション）、Sub-agent（サブエージェント）の正確な関係性と実装方針を明確に定義します。

## 2. 各技術の正確な定義

### 2.1 POML（Prompt Orchestration Markup Language）

**POML とは：**
- **Microsoft 開発の公式仕様**: プロンプトエンジニアリング用マークアップ言語
- **用途**: 単一LLM プロンプトの構造化とテンプレート化
- **設計目的**: プロンプトの再利用性、モジュール性、可読性向上
- **重要**: **マルチエージェントシステム用ではない**

**POML の実際の機能：**
```xml
<poml>
  <!-- 変数定義 -->
  <let name="user_role" value="software engineer" />
  
  <!-- エージェントの役割定義 -->
  <role>You are a {{user_role}} specialized in {{domain}}</role>
  
  <!-- タスク仕様 -->
  <task>
    Analyze the provided code and suggest improvements
    based on best practices and performance considerations.
  </task>
  
  <!-- 条件付きコンテンツ -->
  <p if="complexity_level > 3">
    Focus on architectural patterns and maintainability.
  </p>
  
  <!-- 外部データ統合 -->
  <document src="coding-standards.md" />
  <img src="architecture-diagram.png" alt="System architecture" />
  
  <!-- 出力フォーマット指定 -->
  <output-format>
    Provide recommendations in markdown format with:
    1. Current code analysis
    2. Specific improvement suggestions
    3. Implementation priority
  </output-format>
</poml>
```

**POML の制限：**
- エージェント間通信機能なし
- ワークフローオーケストレーション機能なし  
- Claude Code サブエージェントとの直接統合なし

### 2.2 Claude Code Sub-agents（Claude Code サブエージェント）

**Sub-agent とは：**
- **Anthropic Claude Code の公式機能**: 専門化されたAIペルソナ
- **用途**: 特定タスクに特化した独立したAIエージェント
- **実装**: YAML frontmatter + Markdown 形式
- **実行**: 独立したコンテキストウィンドウで動作

**Sub-agent の公式構造：**
```yaml
---
name: code-reviewer
description: Expert code review specialist. Use proactively after writing code.
tools: Read, Grep, Glob, Bash
model: sonnet
color: blue
---

You are a senior code reviewer ensuring high standards of code quality and security.

When invoked:
1. Run git diff to see recent changes
2. Focus on modified files  
3. Begin review immediately

Review checklist:
- Code is simple and readable
- Functions and variables are well-named
- No duplicated code
- Proper error handling
- No exposed secrets or API keys
- Input validation implemented
- Good test coverage
- Performance considerations addressed

Provide feedback organized by priority:
- Critical issues (must fix)
- Warnings (should fix)  
- Suggestions (consider improving)

Include specific examples of how to fix issues.
```

**Sub-agent の重要な制約：**
- **Task() tool 使用禁止**: サブエージェントは他のエージェントを呼び出せない
- **独立実行**: 各エージェントは独立したコンテキストで動作
- **Orchestrator による管理**: オーケストレーターのみがエージェント間調整を行う

### 2.3 Orchestration（オーケストレーション）

**Orchestration とは：**
- **ワークフロー管理システム**: 複数のサブエージェントとタスクを調整
- **責任範囲**: エージェント選択、タスク分解、結果統合
- **実装**: Claude Code の主要コンテキストで実行

**Orchestration の機能：**
```
Main Claude Code Context (Orchestrator)
├── Task Analysis & Agent Selection
├── Sub-agent Invocation & Coordination
├── Result Collection & Integration
└── Workflow State Management
```

## 3. CC-Deck v2における関係性アーキテクチャ

### 3.1 ハイブリッド実装戦略

CC-Deck v2では、各技術を適切な役割で組み合わせます：

```
┌─────────────────────────────────────────────────────────────┐
│ CC-Deck v2 Architecture                                     │
├─────────────────────────────────────────────────────────────┤
│ Orchestration Layer (Claude Code Main Context)             │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Task Analysis   │ │ Agent Selection │ │ Result        │   │
│ │ & Decomposition │ │ & Invocation    │ │ Integration   │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ POML Template Layer (Prompt Engineering)                   │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Agent Prompt    │ │ Context         │ │ Output Format │   │
│ │ Templates       │ │ Management      │ │ Definitions   │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ Sub-agent Execution Layer (Claude Code Sub-agents)         │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Spec Generator  │ │ Implementation  │ │ Validation    │   │
│ │ Agent           │ │ Agent           │ │ Agent         │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 実装マッピング

#### 3.2.1 POML Templates (`poml/`)
```
poml/
├── commands/                           # オーケストレーター用POMLテンプレート
│   └── orchestrator.poml               # 統一コマンドオーケストレーター（プロンプト解析・ルーティング）
└── agents/                             # エージェント挙動制御用POMLテンプレート（既存ファイル）
    ├── development-styles/
    │   ├── rapid-prototyping.poml      # 高速プロトタイピング
    │   ├── enterprise-development.poml # エンタープライズ開発
    │   └── test-driven-development.poml # TDD重視開発
    ├── review-styles/
    │   ├── strict-security-review.poml # 厳格セキュリティレビュー
    │   ├── performance-focused.poml    # パフォーマンス重視
    │   └── code-quality-review.poml    # コード品質レビュー
    └── testing-styles/
        ├── comprehensive-testing.poml  # 包括的テスト
        └── unit-test-focused.poml      # ユニットテスト重視
```

#### 3.2.2 Claude Code Sub-agents (`~/.claude/agents/` & `.claude/agents/`)
```
.claude/agents/
├── spec-generator.md           # CES仕様生成専門エージェント
├── implementation.md           # TDD実装専門エージェント
├── code-reviewer.md           # コードレビュー専門エージェント
├── test-runner.md             # テスト実行専門エージェント
└── validation.md              # 検証専門エージェント
```

#### 3.2.3 Orchestration Logic (メインClaudeコンテキスト)
- `/orchestrator`統一コマンドによるワークフロー開始
- 自然言語プロンプト解析によるパラメータ抽出
- プロジェクトコンテキスト管理と状態確認
- 適切なサブエージェントの選択と呼び出し
- POML behavior templateの読み込みと適用指示
- 結果の統合と品質保証
- 次アクション提案と状態更新

## 4. 具体的な実装例

### 4.1 POML Template Example

**`poml/commands/orchestrator.poml`:**
```xml
<poml>
  <metadata>
    <template-name>orchestrator</template-name>
    <version>1.0.0</version>
    <description>Unified command orchestrator for CC-Deck v2 slash commands</description>
    <compatible-commands>all</compatible-commands>
  </metadata>

  <!-- プロンプト解析用変数 -->
  <let name="user_prompt" value="{{USER_PROMPT}}" />
  
  <!-- 抽出されるパラメータ -->
  <let name="command" value="" />
  <let name="target" value="" />
  <let name="behavior" value="" />
  <let name="project" value="" />
  <let name="feature" value="" />

  <role>
    You are the orchestrator for CC-Deck v2 platform commands.
    You coordinate between slash commands and specialized sub-agents
    to execute complex development workflows.
  </role>

  <task>
    1. First, analyze the user prompt: "{{user_prompt}}"
    2. Extract parameters: command, target, behavior, project, feature
    3. Route to appropriate sub-agent based on extracted command
    4. Apply behavior template if identified
    5. Execute the workflow with extracted parameters
  </task>

  <prompt-analysis>
    **CRITICAL - Project Identification** (MUST be identified):
    - Look for project names in the prompt
    - If NO project found → ASK USER: "どのプロジェクトで作業しますか？"
    
    **Command Identification**:
    - If contains "review", "check", "analyze", "audit" → command = "review"
    - If contains "implement", "create", "build", "develop" → command = "implement"
    - If contains "specification", "spec", "requirement", "design" → command = "spec"
    - If contains "continue", "next", "proceed" → check spec.json for next action
    - If contains "approve" → command = "approve"
    
    **Behavior Identification**:
    - If contains "security", "strict", "audit" → behavior = "strict-security-review"
    - If contains "rapid", "prototype", "quick", "fast" → behavior = "rapid-prototyping"
    - If contains "enterprise", "production", "robust" → behavior = "enterprise-development"
  </prompt-analysis>

  <command-routing>
    <p if="command == 'review'">
      Route to code-reviewer sub-agent for code analysis.
    </p>
    
    <p if="command == 'implement'">
      Check if design is approved in spec.json
      If approved → Route to implementation sub-agent
    </p>
    
    <p if="command == 'spec'">
      Route to spec-generator sub-agent for specification.
    </p>
  </command-routing>

  <behavior-integration>
    <p if="behavior">
      Load behavior template: poml/agents/{{behavior}}.poml
      Apply behavioral modifications to the selected sub-agent
    </p>
  </behavior-integration>
</poml>
```

### 4.2 Claude Code Sub-agent Example

**`.claude/agents/spec-generator.md`:**
```yaml
---
name: spec-generator
description: CES specification generation specialist. Use proactively for requirements analysis and technical design.
tools: Read, Write, Edit, Grep, Glob, WebSearch, mcp__context7, mcp__deepwiki
model: sonnet
color: green
---

You are a specification generation expert for CC-Deck v2 platform specializing in Claude Code Enhanced Specification (CES) methodology.

Core Responsibilities:
1. EARS+ Requirements Generation
2. Technical Design Documentation  
3. Implementation Task Planning
4. Context-Aware Specification Creation

When invoked:
1. Analyze project structure and existing documentation
2. Research relevant technologies using MCP tools
3. Generate comprehensive CES specifications
4. Ensure consistency with project steering documents
5. Create clear acceptance criteria and success metrics

CES Specification Format:
- requirements.md: EARS+ syntax with file references #[[file:path]]
- design.md: Technical architecture with context integration
- tasks.md: TDD implementation breakdown with quality gates
- context.md: Feature-specific constraints and assumptions

Quality Standards:
- All requirements must be testable and verifiable
- Technical design must align with project architecture
- Implementation tasks must follow TDD methodology
- Specifications must reference existing codebase patterns

Context Integration:
- Use #codebase for project-wide understanding
- Reference #[[file:path/to/file]] for specific file linkage
- Integrate MCP research for technology best practices
- Maintain consistency with steering documents

Always ensure specifications are implementable, testable, and aligned with CC-Deck v2 quality standards.
```

### 4.3 Orchestration Flow Example

**Main Claude Code Context での実行例:**
```
User: /orchestrator create specification for my-project user-authentication

Claude (Orchestrator):
1. Natural Language Analysis (orchestrator.poml):
   - Extract project: "my-project"
   - Extract command: "spec" (create specification)
   - Extract feature: "user-authentication"
   - Extract behavior: none (default behavior)

2. Project Context Loading:
   - Load projects/my-project/project.json
   - Load projects/my-project/specs/user-authentication/spec.json (if exists)
   - Validate current phase and constraints

3. Sub-agent Selection & Routing:
   - Select spec-generator sub-agent based on "spec" command
   - Prepare agent invocation with extracted parameters

4. Sub-agent Invocation:
   > Use the spec-generator subagent to create comprehensive specifications
   > for user-authentication feature in my-project
   > No behavior template specified - use default CES methodology

5. Result Integration:
   - Validate generated specifications against CES standards
   - Update project state: spec.json (phase = "requirements-generated")
   - Record completion status

6. Next Steps Suggestion:
   - Review generated requirements.md for accuracy
   - Use /orchestrator approve requirements for my-project user-authentication when ready
```

## 5. 重要な実装原則

### 5.1 責任分離

1. **POML**: プロンプトテンプレート化のみ
2. **Sub-agents**: 専門タスク実行のみ  
3. **Orchestrator**: ワークフロー管理とエージェント調整のみ

### 5.2 技術制約の遵守

1. **Sub-agentでのTask()使用禁止**
2. **POML の Microsoft 仕様準拠**
3. **Claude Code の Anthropic 仕様準拠**

### 5.3 コンテキスト管理

1. **State-Driven Context Persistence**: ワークフロー間の情報継承
2. **Context Bridging**: フェーズ間のコンテキスト橋渡し
3. **Dynamic Context Injection**: POMLテンプレートへの動的データ注入

## 6. 今後の発展方向

### 6.1 短期目標
- POML テンプレートシステムの完成
- Claude Code Sub-agent 定義の標準化
- オーケストレーション ロジックの実装

### 6.2 中期目標  
- MCP 統合による外部ツール連携強化
- 複数言語対応のテンプレート拡張
- 高度なコンテキスト管理システム

### 6.3 長期目標
- Microsoft POML と Anthropic Claude Code の公式統合
- エンタープライズ向け監視・分析機能
- コミュニティエコシステムの構築

## 7. まとめ

CC-Deck v2 では、POML、Claude Code Sub-agents、Orchestration を以下のように組み合わせます：

- **POML**: プロンプトエンジニアリングとテンプレート管理
- **Sub-agents**: 専門化されたタスク実行
- **Orchestration**: ワークフロー管理とエージェント調整

この分離により、各技術の強みを活かしながら、保守性と拡張性を確保した AI 駆動開発プラットフォームを実現します。