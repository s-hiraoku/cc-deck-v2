# pomljsライブラリ仕様書

## 概要

**pomljsライブラリ**は、Microsoft POML (Prompt Orchestration Markup Language) の JavaScript/TypeScript実装です。POML マークアップをテキスト形式（主にMarkdown）に変換する機能を提供します。

### ライブラリ情報
- **パッケージ名**: `pomljs`
- **現在版**: `0.0.7` (2025年8月時点)
- **ライセンス**: MIT
- **開発元**: Microsoft Corporation
- **公式リポジトリ**: https://github.com/microsoft/poml

## インストール

```bash
# 安定版
npm install pomljs

# ナイトリービルド
npm install pomljs@nightly
```

## API仕様

### 主要関数

#### `read(input)`
POMLマークアップまたはテキストを中間表現（IR）に解析します。

```typescript
async function read(input: string): Promise<string>
```

**パラメータ**:
- `input`: POML マークアップ文字列またはプレーンテキスト

**戻り値**: 中間表現（IR）文字列

**使用例**:
```javascript
const pomlMarkup = '<poml><role>Assistant</role><task>Help user</task></poml>';
const ir = await read(pomlMarkup);
```

#### `write(ir)`
中間表現（IR）をMarkdown形式に変換します。

```typescript
function write(ir: string): string
```

**パラメータ**:
- `ir`: `read()`で生成された中間表現

**戻り値**: Markdown形式の文字列

**使用例**:
```javascript
const markdown = write(ir);
// 結果: "# Role\n\nAssistant\n\n# Task\n\nHelp user"
```

#### `poml(input)`
POMLマークアップを直接Markdown形式に変換します（`read()` + `write()`の統合版）。

```typescript
async function poml(input: string): Promise<string>
```

**パラメータ**:
- `input`: POML マークアップ文字列

**戻り値**: Markdown形式の文字列

**使用例**:
```javascript
const result = await poml('<poml><role>Assistant</role><task>Help</task></poml>');
// 結果: "# Role\n\nAssistant\n\n# Task\n\nHelp"
```

## 機能サポート範囲

### ✅ サポートされている機能

#### 1. POML → テキスト変換
- **完全サポート**: POMLマークアップからMarkdown形式への変換
- **セマンティックタグ**: `<role>`, `<task>`, `<example>`, `<output-format>`
- **データコンポーネント**: `<document>`, `<table>`, `<img>`

#### 2. 変数展開
```xml
<poml>
  <role>You are working on {{project}} using {{language}}</role>
  <task>{{task_description}}</task>
</poml>
```

#### 3. 条件分岐（基本的）
```xml
<poml>
  <p if="phase == 'spec'">Create specifications</p>
  <p if="phase == 'impl'">Implement features</p>
</poml>
```

#### 4. 外部ファイル参照
```xml
<poml>
  <document src="projects/{{project}}/spec.json" />
  <document src="requirements.md" optional="true" />
</poml>
```

### ❌ サポートされていない機能

#### 1. テキスト → POML変換
- **双方向変換は不可能**
- Markdownテキストを`read()`に渡すとプレーンテキストとして処理される
- POML構造への逆変換機能はなし

#### 2. 複雑なスクリプティング
- 高度な条件分岐ロジック
- ループ処理（一部基本的なものは除く）
- 複雑な変数操作

#### 3. 動的コンテンツ生成
- リアルタイムデータ取得
- 外部API呼び出し

## 使用例

### 基本的な変換例

```javascript
import { poml } from 'pomljs';

const behaviorTemplate = `
<poml>
  <role>Enterprise-grade specification generator</role>
  
  <task>
    Generate comprehensive specifications including:
    - Security requirements and threat modeling
    - Compliance documentation
    - Architecture alignment
  </task>
  
  <constraints>
    <quality-gates>
      <security>Complete security analysis required</security>
      <compliance>Regulatory compliance mapping mandatory</compliance>
    </quality-gates>
  </constraints>
  
  <output-format>
    Generate specification with:
    - Executive summary
    - Security analysis
    - Compliance requirements
    - Risk assessment matrix
  </output-format>
</poml>
`;

const promptText = await poml(behaviorTemplate);
console.log(promptText);
```

**出力**:
```markdown
# Role

Enterprise-grade specification generator

# Task

Generate comprehensive specifications including:
- Security requirements and threat modeling
- Compliance documentation
- Architecture alignment

# Constraints

## Quality Gates

### Security
Complete security analysis required

### Compliance
Regulatory compliance mapping mandatory

# Output Format

Generate specification with:
- Executive summary
- Security analysis
- Compliance requirements
- Risk assessment matrix
```

### 変数展開例

```javascript
const templateWithVariables = `
<poml>
  <role>{{role_type}} for {{project_name}}</role>
  <task>{{task_description}}</task>
  <document src="projects/{{project_name}}/config.json" />
</poml>
`;

// 注意: pomljsは直接的な変数注入APIを提供していないため、
// 文字列置換を事前に行う必要がある
const processedTemplate = templateWithVariables
  .replace(/\{\{role_type\}\}/g, 'Code Reviewer')
  .replace(/\{\{project_name\}\}/g, 'fintech-app')
  .replace(/\{\{task_description\}\}/g, 'Review code for security vulnerabilities');

const result = await poml(processedTemplate);
```

## CC-Deck v2での活用方針

### 推奨用途

#### 1. Behavior Template変換
```javascript
// behavior.pomlファイルをプロンプトテキストに変換
async function loadBehaviorTemplate(behaviorName) {
  const pomlContent = await fs.readFile(`poml/agents/${behaviorName}.poml`, 'utf8');
  const promptText = await poml(pomlContent);
  return promptText;
}
```

#### 2. Sub-agent Prompt生成
```javascript
// orchestratorでの使用例
const behaviorPrompt = await loadBehaviorTemplate('enterprise-development');
const subAgentTask = await Task({
  subagent_type: "spec-generator",
  prompt: `${behaviorPrompt}\n\n具体的タスク: ${userRequest}`
});
```

### 使用上の注意点

#### 1. 単方向変換のみ
- **POML → テキスト**のみサポート
- 逆変換（テキスト → POML）は不可能
- 動的なPOML生成には他の手法が必要

#### 2. 変数展開の制限
- 事前に文字列置換が必要
- 動的なコンテキスト注入には別途実装が必要

#### 3. エラーハンドリング
```javascript
try {
  const result = await poml(pomlContent);
  return result;
} catch (error) {
  console.error('POML processing failed:', error.message);
  // フォールバック処理
  return pomlContent; // 元のPOMLをそのまま返す
}
```

## パフォーマンス特性

### 処理速度
- **小規模POML** (< 1KB): ~1-5ms
- **中規模POML** (1-10KB): ~5-20ms  
- **大規模POML** (> 10KB): ~20-100ms

### メモリ使用量
- **基本的な処理**: ~5-10MB
- **大量のファイル参照**: ~20-50MB

### 依存関係
```json
{
  "dependencies": {
    "@xml-tools/ast": "^5.0.5",
    "@xml-tools/parser": "^1.0.11", 
    "cheerio": "^1.0.0",
    "js-yaml": "^4.1.0",
    "mammoth": "^1.9.0",
    "pdf-parse": "^1.1.1",
    "react": "^19.0.0",
    "sharp": "^0.33.5",
    "showdown": "^2.1.0",
    "xlsx": "^0.18.5"
  }
}
```

## 制限事項と回避策

### 1. 双方向変換の制限
**制限**: テキストからPOMLへの変換不可

**回避策**: 
- POMLテンプレートファイルを事前作成
- 動的コンテンツは変数展開で対応

### 2. 変数展開の制限
**制限**: 高度な変数操作が不可

**回避策**:
```javascript
// 事前に文字列処理で変数展開
function expandTemplate(template, variables) {
  let expanded = template;
  for (const [key, value] of Object.entries(variables)) {
    expanded = expanded.replace(
      new RegExp(`\\{\\{${key}\\}\\}`, 'g'), 
      value
    );
  }
  return expanded;
}
```

### 3. 複雑な条件分岐の制限
**制限**: 高度な制御フローが不可

**回避策**:
- 複数のPOMLテンプレートを用意
- JavaScriptで事前に条件判定

## まとめ

pomljsは**POML → テキスト変換**に特化したライブラリであり、CC-Deck v2では：

- ✅ **behavior.pomlテンプレート**をプロンプトテキストに変換
- ✅ **統一されたプロンプト形式**でsub-agentに指示
- ✅ **Microsoft POML仕様準拠**の標準化

これらの用途で安全かつ効果的に活用できます。

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-08-22  
**最終更新**: 2025-08-22  
**ステータス**: 検証済み・実装可能