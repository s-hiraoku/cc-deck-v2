# CC-Deck v2 3層アーキテクチャ実装指針

## 概要

CC-Deck v2は、Claude Codeの機能を活用した3層アーキテクチャによってオーケストレーション機能を実現します。本ドキュメントは、任意のプロジェクトに適用可能な汎用的な実装指針を提供します。

## 🏗️ 3層アーキテクチャ構成

```
┌─────────────────────────────────────────┐
│ Layer 1: Orchestrator                  │
│ - POMLファイルによる動作制御             │
│ - マークダウンファイル読み込み＆実行      │
│ - 全体フロー管理                        │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 2: Custom Slash Commands         │
│ - マークダウンファイル（.md）として定義  │
│ - 各専門領域の処理ロジック               │
│ - サブエージェント選択・呼び出し         │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 3: Sub-Agents                    │
│ - Taskツールで実行される専門エージェント │
│ - 実際の作業実行（挨拶、物語、感情表現等）│
│ - POMLファイルで動作定義                │
└─────────────────────────────────────────┘
```

## 📁 ファイル構成パターン

### Layer 1: Orchestrator ファイル
```
.claude/commands/{namespace}/
└── {orchestrator-name}.md      # オーケストレーターコマンド定義

poml/commands/{namespace}/
└── {orchestrator-name}.poml    # オーケストレーター動作制御POML
```

### Layer 2: カスタムスラッシュコマンド ファイル
```
.claude/commands/{namespace}/
├── {command-name-1}.md        # 専門コマンド1実行指示
└── {command-name-2}.md        # 専門コマンド2実行指示

poml/commands/{namespace}/
├── {command-name-1}.poml      # 専門コマンド1動作制御POML
└── {command-name-2}.poml      # 専門コマンド2動作制御POML
```

### Layer 3: サブエージェント ファイル
```
.claude/agents/{namespace}/
├── {subagent-type-1}.md               # 専門サブエージェント1定義
├── {subagent-type-2}.md               # 専門サブエージェント2定義
└── {subagent-type-3}.md               # 専門サブエージェント3定義

poml/agents/{namespace}/
├── {subagent-type-1}-behavior.poml    # 専門サブエージェント1動作定義
├── {subagent-type-2}-behavior.poml    # 専門サブエージェント2動作定義
└── {subagent-type-3}-behavior.poml    # 専門サブエージェント3動作定義
```

## 🗂️ POMLファイル体系的配置

POMLファイルはルートの`poml/`ディレクトリに体系的に配置します：

```
poml/
├── commands/{namespace}/           # 第1層・第2層のコマンド動作定義
│   ├── {orchestrator-name}.poml   # オーケストレーター動作制御
│   ├── {command-name-1}.poml      # 専門コマンド1動作制御
│   └── {command-name-2}.poml      # 専門コマンド2動作制御
├── agents/{namespace}/             # 第3層のサブエージェント動作定義
│   ├── {subagent-type-1}-behavior.poml
│   ├── {subagent-type-2}-behavior.poml
│   └── {subagent-type-3}-behavior.poml
└── context/{namespace}/            # コンテキスト管理
    └── {context-type}.poml
```

**命名規則**:
- `{namespace}`: ワークフロー分類の名前空間（kebab-case）
  - 例: `development`, `testing`, `deployment`, `code-review`, `spec-generation`
- `{orchestrator-name}`: オーケストレーターの役割を表す名前
- `{command-name}`: 専門領域を表すコマンド名
- `{subagent-type}`: サブエージェントの専門分野を表す名前
- `-behavior`: サブエージェントの動作定義であることを示すサフィックス

## 🔧 実装方法

### 1. Orchestrator層の実装

#### POML設計原則
```xml
<poml>
  <role>{orchestrator-domain}の統合管理</role>
  
  <task>
    実行すべきファイルと順序のみを記載：
    <list>
      <item>`.claude/commands/{namespace}/{command-name-1}.md`を読み込んで実行</item>
      <item>`.claude/commands/{namespace}/{command-name-2}.md`を読み込んで実行</item>
      <item>実行結果を統合して表示</item>
    </list>
  </task>
  
  <p>実行手順：</p>
  <list>
    <item>1. 第1専門コマンドを読み込んで、その内容を直接実行</item>
    <item>2. 第2専門コマンドを読み込んで、その内容を直接実行</item>
    <item>3. 実行結果を表示</item>
  </list>
  
  <!-- 必須制約 -->
  <p>重要：読み込んだマークダウンファイルの内容は、Taskツールで実行してはいけません。ファイルの内容を読み取って、その指示に従って直接実行してください。</p>
  
  <!-- 出力フォーマット定義 -->
  <output-format>
    ## 🎯 {Domain} 実行結果
    
    ### 📋 実行サマリー
    - Layer 1: {Orchestrator Name}
    - Layer 2: {Specialized Commands}
    - Layer 3: {Domain-Specific Sub-Agents}
    
    ### 🟢 各コマンド実行結果
    [各専門コマンドの実行結果を表示]
    
    ### ✅ 実行完了確認
    [実行状況の要約]
  </output-format>
</poml>
```

#### 責務分離のルール
- ✅ **含めるべき内容**: ファイルパス、実行順序、期待される結果レベル
- ❌ **含めてはいけない内容**: 第2層の実装詳細、使用ツール名、サブエージェント種類

### 2. Custom Slash Commands層の実装

#### マークダウンファイル設計
```markdown
---
name: {command-name}
description: {domain-specific}な処理を実行するコマンド
tools: [Read, Bash, Task]
---

# {Command Name} - Layer 2

{domain}領域の専門処理を担当するコマンド

## My Task

1. `poml/commands/{namespace}/{command-name}.poml`ファイルを読み込んで動作を決定
2. POML内容に基づいて適切なサブエージェントをTaskツールで呼び出し
3. サブエージェントの実行結果を統合して返す

## Execution Process

POMLファイルで定義された動作に従って、専門サブエージェントを順次実行し、
結果を統合したアウトプットを生成する。
具体的なサブエージェント種別や実行詳細はPOMLファイルで動的に決定される。
```

**設計原則**:
- 具体的なサブエージェント名を直接記載しない
- POMLファイルから動的に実行内容を決定する構造
- ドメイン固有の処理に集中し、汎用性を保持

### 3. Sub-Agents層の実装

#### サブエージェント登録方法

`.claude/agents/{namespace}/{subagent-type}.md` ファイル：
```markdown
---
name: {subagent-type}
description: {domain-specific}な{specialized-function}を担当するサブエージェント
model: {claude-model}  # sonnet, opus, haiku
tools: [{required-tools}]  # Read, Bash, Edit, etc.
---

# {Subagent Name} - Layer 3

{domain-specific}な{specialized-function}を専門に担当するサブエージェント

## My Role

{subagent-persona}として{specialized-domain}の処理を実行します

## Task Execution

対応するPOMLファイル（`poml/agents/{namespace}/{subagent-type}-behavior.poml`）の指示に従って、
{domain-specific}なタスクを実行し、期待される形式で結果を返します
```

#### POML動作定義パターン
```xml
<poml>
  <role>{subagent-persona}として{specialized-domain}を担当</role>
  
  <task>
    {domain-specific}なタスクの実行：
    <list>
      <item>{task-requirement-1}</item>
      <item>{task-requirement-2}</item>
      <item>{output-requirement}</item>
    </list>
  </task>
  
  <example>
    Input: {example-input}
    Output: {example-output}
  </example>
  
  <output-format>
    {specific-output-format-requirements}
  </output-format>
</poml>
```

**サブエージェント設計指針**:
- 単一責任原則: 1つのサブエージェントは1つの専門機能に集中
- 再利用性: 複数のコマンドから呼び出し可能な汎用的な設計
- 明確な入出力: 期待する入力と出力形式を明確に定義

## ⚡ 実行フロー

### 汎用実行パターン

1. **Orchestrator起動**
   ```bash
   /{namespace}:{orchestrator-name}
   ```

2. **Layer 2実行** (Orchestrator内で実行)
   ```javascript
   // Readツールで専門コマンドのマークダウンファイル読み込み
   // ファイル内容の指示に従って直接実行
   // 第2層の具体的な処理内容は各マークダウンファイルで定義
   ```

3. **Layer 3実行** (Layer 2から実行)
   ```javascript
   // Taskツールで専門サブエージェント呼び出し
   Task({
     subagent_type: "{specialized-agent-type}",
     description: "{domain-specific-description}",
     prompt: "{context-aware-execution-instruction}"
   })
   ```

### 実行フロー設計原則

1. **動的実行内容決定**: POMLファイルで実行時に動作を決定
2. **層間責務分離**: 各層が独立して機能し、疎結合を維持
3. **コンテキスト伝播**: 上位層から下位層へ適切にコンテキストを伝達
4. **結果統合**: 各層で適切に結果を統合して次の層に渡す

## 🚫 実装時の注意事項

### 実行してはいけない方法
- ❌ `claude -p "/{namespace}:{command-name}"` (出力がTerminalに表示されない)
- ❌ マークダウンファイル（第2層）をTaskツールで実行
- ❌ カスタムスラッシュコマンドをTaskツールで実行
- ❌ Orchestrator層に第2層の実装詳細を記載

### 正しい実行方法
- ✅ マークダウンファイルをReadツールで読み込み、内容を直接実行
- ✅ サブエージェント（第3層）のみTaskツールで実行
- ✅ 各層の責務を明確に分離し、疎結合を維持
- ✅ POMLファイルで動的に動作を決定する構造

### 責務分離の原則
- **Layer 1**: ファイル読み込み＆実行順序管理のみ
- **Layer 2**: 専門領域処理＆サブエージェント選択のみ  
- **Layer 3**: 具体的なタスク実行のみ

## 📊 汎用出力形式パターン

プロジェクトの要件に応じて、以下の出力形式パターンを参考に設計してください：

```xml
<output-format>
  ## 🎯 {Domain} {Workflow-Type} 実行結果
  
  ### 📋 実行サマリー
  - Layer 1: {Orchestrator-Name} ({orchestrator-role})
  - Layer 2: {Domain-Commands} ({specialized-functions})
  - Layer 3: {Domain-Sub-Agents} ({specific-capabilities})
  
  ### 🟢 各専門コマンド実行結果
  #### {Command-1} 実行結果:
  ```
  [{Command-1-Output}]
  ```
  
  #### {Command-2} 実行結果:
  ```
  [{Command-2-Output}]
  ```
  
  ### ✅ {Workflow-Type} 完了確認
  | Layer | 機能 | 状態 | 実行コンポーネント数 |
  |-------|------|------|---------------------|
  | Layer 1 | {Orchestrator-Function} | ✅ 正常 | - |
  | Layer 2 | {Specialized-Commands} | ✅ 正常 | {N}コマンド |
  | Layer 3 | {Domain-Sub-Agents} | ✅ 正常 | {M}エージェント |
  
  **総合結果: {Domain} {Workflow-Type} が完了しました**
</output-format>
```

## 🎯 設計の成功要因

汎用的な3層アーキテクチャ実装指針として重要な要素：

1. **層間疎結合**: 各層が独立しており、一方の変更が他方に影響しない
2. **動的動作決定**: POMLファイルによる実行時動作決定で柔軟性確保
3. **責務明確化**: 各層が単一責任を持ち、メンテナンス性が高い
4. **再利用性**: テンプレート化により、異なるドメインで再利用可能
5. **拡張性**: 新しいサブエージェントやコマンドの追加が容易

## 🔄 適用プロセス

この実装指針を新しいワークフローに適用する手順：

1. **ワークフロー分類定義**: 対象ワークフロー（development, testing, deployment等）を特定
2. **名前空間決定**: `{namespace}`をワークフロー種別に基づいて決定
3. **ファイル構成**: 適切なディレクトリ構造を作成
4. **Layer 3設計**: 必要なサブエージェントとその機能を定義
5. **Layer 2設計**: 専門コマンドとPOMLファイルを作成
6. **Layer 1設計**: オーケストレーターとその動作を定義
7. **検証・調整**: 実際の実行で動作確認し、必要に応じて調整

## 📝 実装指針サマリー

CC-Deck v2の3層アーキテクチャは、Claude Codeの機能を活用した汎用的なオーケストレーション基盤です。ワークフロー分類による名前空間管理、層間の疎結合、動的な動作決定、明確な責務分離により、様々な開発ワークフローに適用可能な拡張性の高いアーキテクチャを実現できます。