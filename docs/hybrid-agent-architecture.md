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

### 1. エージェント実行時の統合

```javascript
// pomljsを使用したPOML処理フロー
import { compile } from 'pomljs';

async function execute_agent(agent_name, project_name) {
    // 1. 静的定義の読み込み
    const agent_def = await load_agent_definition(`.claude/agents/${agent_name}.md`);
    
    // 2. POMLテンプレートの読み込み
    const poml_template = await fs.readFile(`poml/${phase}-prompt.poml`, 'utf8');
    const variables = await load_json(`projects/${project_name}/.workflow/variables.json`);
    
    // 3. pomljsでPOMLを処理
    const compiled_prompt = compile(poml_template, {
        variables: variables,
        context: {
            PROJECT_NAME: project_name,
            PHASE: phase,
            TIMESTAMP: new Date().toISOString()
        }
    });
    
    // 4. エージェント実行
    const result = await call_agent(agent_name, compiled_prompt);
    
    // 5. 状態の更新
    await update_workflow_state(result);
}
```

### 2. pomljsによるPOML処理の詳細

```javascript
// POML処理の具体例
import { compile, validate } from 'pomljs';

class POMLProcessor {
    async processTemplate(templatePath, variables) {
        // 1. POMLファイルの読み込み
        const pomlContent = await fs.readFile(templatePath, 'utf8');
        
        // 2. POML構文の検証
        const validation = validate(pomlContent);
        if (!validation.valid) {
            throw new Error(`POML validation failed: ${validation.errors}`);
        }
        
        // 3. 変数とコンテキストの設定
        const context = {
            ...variables,
            // システム変数
            CURRENT_TIME: new Date().toISOString(),
            WORKING_DIR: process.cwd()
        };
        
        // 4. POML → プロンプト変換
        const compiled = compile(pomlContent, context);
        
        return {
            prompt: compiled.text,
            metadata: compiled.metadata,
            variables_used: compiled.variables
        };
    }
    
    async evaluateConditions(pomlContent, context) {
        // 条件分岐の評価
        return compile(pomlContent, context, {
            evaluateConditionals: true
        });
    }
}

### 3. オーケストレーターでの制御

```markdown
# orchestrator.md
---
name: hybrid-orchestrator
tools: [Task, Read, Write, Bash]
---

## Execution Steps

1. Load static agent definition from `.claude/agents/`
2. Process POML template using pomljs
3. Generate dynamic prompt with variable substitution
4. Call agent with compiled prompt

## POML Processing Command

Execute pomljs using Bash tool:
```bash
npx pomljs compile poml/spec-prompt.poml \
  --variables projects/test-project/.workflow/variables.json \
  --output .temp/compiled-prompt.md
```
```

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

### 1. pomljsの依存関係管理

```json
// package.json
{
  "dependencies": {
    "pomljs": "^latest",
    "@microsoft/poml": "^latest"
  },
  "scripts": {
    "poml:compile": "pomljs compile",
    "poml:validate": "pomljs validate"
  }
}
```

### 2. エージェント作成時
```bash
# 静的部分の作成
touch .claude/agents/new-agent.md
# → 基本的なロール、責任、実行パターンを定義

# 動的部分の準備
touch poml/spec-prompt.poml
# → フェーズ固有のプロンプトテンプレートを記述

# pomljsでの検証
npx pomljs validate poml/spec-prompt.poml
```

### 3. pomljsを使ったPOML処理パイプライン
```bash
# 1. POML構文検証
npx pomljs validate poml/spec-prompt.poml

# 2. 変数ファイルの準備
echo '{"PROJECT_NAME":"test-project","PHASE":"spec"}' > .temp/variables.json

# 3. POMLコンパイル
npx pomljs compile poml/spec-prompt.poml \
  --variables .temp/variables.json \
  --output .temp/compiled-prompt.md

# 4. 生成されたプロンプトの確認
cat .temp/compiled-prompt.md
```

### 3. 変数展開
```xml
<!-- variables.poml -->
<poml>
  <paths>
    <spec-dir>specs/</spec-dir>
    <impl-dir>src/</impl-dir>
    <test-dir>tests/</test-dir>
  </paths>
  
  <templates>
    <file-header>
      # Generated by CC-Deck v2
      # Project: {{project_name}}
      # Date: {{timestamp}}
    </file-header>
  </templates>
</poml>
```

## ベストプラクティス

### 1. 静的定義のガイドライン
- プロジェクトに依存しない共通ロジックのみ
- 明確な実行ステップとパターン
- TOMLファイルの参照パスを明記

### 2. 動的プロンプトのガイドライン
- 条件分岐はPOMLの`if`属性で実装
- 変数は`{{VARIABLE}}`形式で定義
- セマンティックタグで構造を明確化

### 3. 統合時の注意点
- POMLファイルの存在確認を必須に
- 変数展開の検証
- 条件分岐の適切な処理

## 移行計画

### Phase 1: 基本実装
1. 既存エージェントの.md部分を維持
2. POMLテンプレートファイルの導入
3. POML処理ロジックの追加

### Phase 2: 完全統合
1. すべてのプロンプトテンプレートをPOMLに移行
2. オーケストレーターのPOML対応
3. 動的プロンプト生成の実装

### Phase 3: 最適化
1. キャッシング機構の導入
2. 変数展開の高度化
3. バリデーション強化

## まとめ

ハイブリッドアーキテクチャにより：

- **開発効率の向上**: 静的と動的の明確な分離
- **保守性の向上**: 変更箇所の局所化
- **拡張性の確保**: 新しいプロジェクトへの適用が容易
- **可読性の向上**: POMLのセマンティックタグによる構造化

このアプローチは、Claude Codeの制約内で最大限の柔軟性を提供し、CC-Deck v2の目標である「時間のかかるワークフローを単一コマンドに」を実現する基盤となる。

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-01-17  
**ステータス**: 設計承認待ち