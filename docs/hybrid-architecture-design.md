# ハイブリッドアーキテクチャ設計: 固定インフラ + POML挙動制御

## 1. 概要

CC-Deck v2では、**固定インフラストラクチャ**と**POML挙動制御**を組み合わせたハイブリッドアーキテクチャを採用します。これにより、安定性・高速性と柔軟性・拡張性を両立した AI 駆動開発プラットフォームを実現します。

## 2. アーキテクチャの基本原理

### 2.1 設計思想

```
固定インフラ（安定性・最適化） + POML挙動制御（柔軟性・拡張性） = 最適バランス
```

### 2.2 3層構造

```
┌─────────────────────────────────────────────────────────────┐
│ Layer 1: Fixed Infrastructure (安定・最適化)                │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Custom Slash    │ │ Specialized     │ │ Core Workflow │   │
│ │ Commands        │ │ Sub-agents      │ │ Logic         │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ Layer 2: POML Behavior Control (動的・柔軟)                │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Behavior        │ │ Context         │ │ Output Format │   │
│ │ Templates       │ │ Definitions     │ │ Controls      │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
├─────────────────────────────────────────────────────────────┤
│ Layer 3: Execution Engine (統合・実行)                     │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐   │
│ │ Command         │ │ Agent           │ │ Result        │   │
│ │ Dispatcher      │ │ Orchestrator    │ │ Integration   │   │
│ └─────────────────┘ └─────────────────┘ └───────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## 3. 固定インフラストラクチャ設計

### 3.1 カスタムスラッシュコマンド

#### 基本コマンドセット
```bash
# プロジェクト管理
/project-init [project-name]                    # プロジェクト初期化
/project-switch [project-name]                  # プロジェクト切り替え
/project-list                                   # プロジェクト一覧

# CES仕様ワークフロー
/spec-init [project] [feature]                  # 仕様生成開始
/spec-requirements [project] [feature]          # 要件定義生成
/spec-design [project] [feature]                # 技術設計生成
/spec-tasks [project] [feature]                 # 実装タスク生成

# 実装ワークフロー
/implement [project] [feature]                  # 実装実行
/review [target] [--behavior=template]          # コードレビュー
/test [project] [feature] [--behavior=template] # テスト実行
/validate [project] [feature]                   # 検証実行

# 承認・状態管理
/approve-requirements [project] [feature]       # 要件承認
/approve-design [project] [feature]             # 設計承認
/approve-tasks [project] [feature]              # タスク承認

# 高度なワークフロー
/deploy [project] [--env=environment]           # デプロイ実行
/dashboard [project]                             # プロジェクトダッシュボード
```

#### コマンド実行時の POML 統合
```bash
# 基本形式
/command [args] [--behavior=poml-template]

# 実例
/review auth-module --behavior=strict-security-review
/implement user-auth --behavior=rapid-prototyping
/test payment-flow --behavior=enterprise-validation
```

### 3.2 専門サブエージェント

#### Core Sub-agents
```
.claude/agents/
├── spec-generator.md       # CES仕様生成専門エージェント
├── implementation.md       # TDD実装専門エージェント
├── code-reviewer.md        # コードレビュー専門エージェント
├── test-runner.md          # テスト実行専門エージェント
├── validator.md            # 検証・QA専門エージェント
├── deployment.md           # デプロイメント専門エージェント
└── architect.md            # アーキテクチャ設計専門エージェント
```

#### サブエージェント標準構造
```yaml
---
name: [agent-name]
description: [specific expertise and when to use]
tools: [optimized tool set for this agent]
model: [appropriate model for the task]
color: [visual identifier]
---

You are a [domain] specialist for CC-Deck v2 platform.

Core Responsibilities:
- [Primary responsibility 1]
- [Primary responsibility 2]
- [Primary responsibility 3]

When invoked:
1. [Standard process step 1]
2. [Standard process step 2]
3. [Standard process step 3]

POML Behavior Integration:
- Load behavior template when --behavior parameter is provided
- Follow POML-defined behavioral modifications
- Maintain core expertise while adapting approach
- Override default patterns with POML instructions

Quality Standards:
- [Quality requirement 1]
- [Quality requirement 2]
- [Quality requirement 3]

Always ensure outputs meet CC-Deck v2 quality standards while following any POML behavioral modifications.
```

## 4. POML挙動制御システム

### 4.1 ディレクトリ構造

```
poml/
├── commands/                            # スラッシュコマンド用POMLテンプレート
│   └── orchestrator.poml                # 統一コマンドオーケストレーター
└── agents/                              # エージェント挙動制御用POMLテンプレート
    ├── development-styles/
    │   ├── rapid-prototyping.poml       # 高速プロトタイピング
    │   ├── enterprise-development.poml  # エンタープライズ開発
    │   ├── performance-focused.poml     # パフォーマンス重視
    │   └── accessibility-first.poml     # アクセシビリティ優先
    ├── review-styles/
    │   ├── strict-security-review.poml  # 厳格セキュリティレビュー
    │   ├── architecture-review.poml     # アーキテクチャレビュー
    │   ├── performance-review.poml      # パフォーマンスレビュー
    │   └── code-quality-review.poml     # コード品質レビュー
    ├── testing-styles/
    │   ├── comprehensive-testing.poml   # 包括的テスト
    │   ├── unit-test-focused.poml      # ユニットテスト重視
    │   ├── integration-testing.poml    # 統合テスト重視
    │   └── e2e-testing.poml            # E2Eテスト重視
    └── domain-specific/
        ├── fintech-compliance.poml      # フィンテック準拠
        ├── healthcare-security.poml     # ヘルスケアセキュリティ
        ├── e-commerce-optimization.poml # ECサイト最適化
        └── iot-device-development.poml  # IoTデバイス開発
```

### 4.2 POML テンプレート標準構造

#### 基本テンプレート形式
```xml
<poml>
  <metadata>
    <template-name>{{TEMPLATE_NAME}}</template-name>
    <version>1.0.0</version>
    <description>{{TEMPLATE_DESCRIPTION}}</description>
    <compatible-agents>{{AGENT_LIST}}</compatible-agents>
  </metadata>

  <behavior-modification>
    <priorities>
      <!-- 優先順位の定義 -->
    </priorities>
    
    <constraints>
      <!-- 制約条件の定義 -->
    </constraints>
    
    <approach>
      <!-- アプローチ方法の定義 -->
    </approach>
  </behavior-modification>

  <execution-parameters>
    <quality-threshold>{{QUALITY_LEVEL}}</quality-threshold>
    <speed-preference>{{SPEED_LEVEL}}</speed-preference>
    <risk-tolerance>{{RISK_LEVEL}}</risk-tolerance>
  </execution-parameters>

  <output-requirements>
    <format>{{OUTPUT_FORMAT}}</format>
    <detail-level>{{DETAIL_LEVEL}}</detail-level>
    <additional-artifacts>{{ARTIFACTS_LIST}}</additional-artifacts>
  </output-requirements>

  <context-integration>
    <required-files>{{FILE_LIST}}</required-files>
    <external-references>{{REFERENCE_LIST}}</external-references>
    <domain-knowledge>{{DOMAIN_CONTEXT}}</domain-knowledge>
  </context-integration>
</poml>
```

### 4.3 具体的なPOMLテンプレート例

#### `strict-security-review.poml`
```xml
<poml>
  <metadata>
    <template-name>strict-security-review</template-name>
    <version>1.0.0</version>
    <description>厳格なセキュリティレビューを実行するための挙動制御</description>
    <compatible-agents>code-reviewer, validator</compatible-agents>
  </metadata>

  <behavior-modification>
    <priorities>
      - Security vulnerability detection
      - OWASP Top 10 compliance verification
      - Authentication/Authorization validation
      - Data protection compliance
      - Cryptographic implementation review
    </priorities>
    
    <constraints>
      <failure-tolerance>zero</failure-tolerance>
      <review-depth>maximum</review-depth>
      <compliance-requirements>
        - SOC 2 Type II
        - PCI DSS (if applicable)
        - GDPR compliance
        - HIPAA (if applicable)
      </compliance-requirements>
    </constraints>
    
    <approach>
      <methodology>threat-modeling</methodology>
      <analysis-type>static-and-dynamic</analysis-type>
      <verification-method>manual-and-automated</verification-method>
    </approach>
  </behavior-modification>

  <execution-parameters>
    <quality-threshold>critical-security-standard</quality-threshold>
    <speed-preference>thorough-over-fast</speed-preference>
    <risk-tolerance>minimal</risk-tolerance>
  </execution-parameters>

  <output-requirements>
    <format>security-audit-report</format>
    <detail-level>comprehensive</detail-level>
    <additional-artifacts>
      - Threat model diagram
      - Security test cases
      - Remediation action plan
      - Compliance checklist
    </additional-artifacts>
    <severity-classification>critical|high|medium|low|info</severity-classification>
  </output-requirements>

  <context-integration>
    <required-files>
      - Security policies documentation
      - Previous security audit reports
      - Compliance requirements documentation
    </required-files>
    <external-references>
      - OWASP guidelines
      - Industry-specific security standards
      - Regulatory compliance requirements
    </external-references>
    <domain-knowledge>security-engineering</domain-knowledge>
  </context-integration>
</poml>
```

#### `rapid-prototyping.poml`
```xml
<poml>
  <metadata>
    <template-name>rapid-prototyping</template-name>
    <version>1.0.0</version>
    <description>高速プロトタイピングのための開発挙動制御</description>
    <compatible-agents>implementation, spec-generator</compatible-agents>
  </metadata>

  <behavior-modification>
    <priorities>
      - Speed of implementation
      - Quick feedback cycles
      - Minimal viable functionality
      - Rapid iteration capability
      - User validation readiness
    </priorities>
    
    <constraints>
      <code-quality-threshold>minimal-viable</code-quality-threshold>
      <testing-requirements>basic-functionality-only</testing-requirements>
      <documentation-level>inline-comments-only</documentation-level>
      <architecture-complexity>simple-monolithic</architecture-complexity>
    </constraints>
    
    <approach>
      <development-method>iterative-rapid</development-method>
      <validation-frequency>continuous-user-feedback</validation-frequency>
      <refactoring-strategy>defer-until-stable</refactoring-strategy>
    </approach>
  </behavior-modification>

  <execution-parameters>
    <quality-threshold>prototype-grade</quality-threshold>
    <speed-preference>maximum</speed-preference>
    <risk-tolerance>high-for-speed</risk-tolerance>
  </execution-parameters>

  <output-requirements>
    <format>working-prototype</format>
    <detail-level>minimal</detail-level>
    <additional-artifacts>
      - Demo script
      - User feedback collection plan
      - Next iteration roadmap
    </additional-artifacts>
  </output-requirements>

  <context-integration>
    <required-files>
      - User story definitions
      - Basic wireframes or mockups
      - Core feature requirements
    </required-files>
    <external-references>
      - UI/UX guidelines
      - Target user personas
      - Business validation criteria
    </external-references>
    <domain-knowledge>prototype-development</domain-knowledge>
  </context-integration>
</poml>
```

## 5. 実行エンジンとワークフロー

### 5.1 コマンド実行フロー

```
1. Command Parsing
   ├── Extract base command (/review)
   ├── Parse target (auth-module)
   └── Identify behavior template (--behavior=strict-security-review)

2. Infrastructure Loading
   ├── Load appropriate sub-agent (code-reviewer)
   ├── Initialize base capabilities
   └── Prepare standard workflow

3. POML Behavior Integration
   ├── Load specified POML template
   ├── Parse behavior modifications
   ├── Apply constraints and priorities
   └── Override default patterns

4. Execution
   ├── Execute with modified behavior
   ├── Apply POML-defined constraints
   ├── Generate outputs per POML requirements
   └── Maintain quality standards

5. Result Integration
   ├── Validate outputs against both base and POML standards
   ├── Generate additional artifacts if specified
   └── Update project state
```

### 5.2 Agent-POML Integration Pattern

#### サブエージェント内でのPOML処理
```markdown
You are a [domain] specialist for CC-Deck v2 platform.

POML Behavior Integration Protocol:
1. When --behavior parameter is provided:
   a. Load the specified POML template
   b. Parse <behavior-modification> section
   c. Apply priorities, constraints, and approach modifications
   d. Override default patterns with POML instructions

2. Maintain core expertise while adapting:
   a. Keep domain knowledge and capabilities
   b. Modify approach based on POML priorities
   c. Apply POML constraints strictly
   d. Generate outputs per POML requirements

3. Quality assurance:
   a. Ensure outputs meet both base and POML standards
   b. Validate against POML-specified criteria
   c. Generate additional artifacts if required
   d. Maintain CC-Deck v2 quality gates

Example Integration:
```
Input: /review auth-module --behavior=strict-security-review
Process:
1. Load code-reviewer agent
2. Load strict-security-review.poml
3. Apply security-focused priorities and constraints
4. Execute review with enhanced security analysis
5. Generate security audit report format
```
```

## 6. 使用例とベストプラクティス

### 6.1 典型的な使用パターン

#### Development Workflow
```bash
# 1. プロジェクト初期化
/project-init fintech-app

# 2. セキュリティ重視の仕様生成
/spec-init fintech-app user-auth --behavior=fintech-compliance

# 3. エンタープライズ品質の実装
/implement fintech-app user-auth --behavior=enterprise-development

# 4. 厳格なセキュリティレビュー
/review user-auth --behavior=strict-security-review

# 5. 包括的テスト実行
/test fintech-app user-auth --behavior=comprehensive-testing

# 6. コンプライアンス検証
/validate fintech-app user-auth --behavior=fintech-compliance
```

#### Rapid Prototyping Workflow
```bash
# 1. プロトタイプ プロジェクト初期化
/project-init mvp-demo

# 2. 高速仕様生成
/spec-init mvp-demo core-features --behavior=rapid-prototyping

# 3. 最小実装
/implement mvp-demo core-features --behavior=rapid-prototyping

# 4. 基本テスト
/test mvp-demo core-features --behavior=unit-test-focused

# 5. ユーザビリティ検証
/validate mvp-demo core-features --behavior=user-experience-focused
```

### 6.2 POML テンプレート作成ガイドライン

#### 1. 明確な目的定義
- 具体的な使用場面を明記
- 対象ドメインや制約を明確化
- 期待される成果物を定義

#### 2. 適切な制約設定
- 現実的で測定可能な制約
- エージェントの能力と整合性
- プロジェクト要件との整合性

#### 3. 再利用可能性
- パラメーター化による柔軟性
- 複数プロジェクトでの適用可能性
- 段階的な詳細度調整

#### 4. 品質保証
- 出力品質の明確な基準
- 検証可能な成果物
- エラーハンドリングの考慮

## 7. 実装ロードマップ

### Phase 1: Core Infrastructure (2-3週間)
- [ ] 基本スラッシュコマンド実装
- [ ] Core Sub-agents 作成 (5-7個)
- [ ] 基本ワークフロー構築
- [ ] POML読み込み機能

### Phase 2: POML Integration (2-3週間)
- [ ] POML パーサー実装
- [ ] Agent-POML統合機能
- [ ] 基本POMLテンプレート作成 (10-15個)
- [ ] 動的挙動制御システム

### Phase 3: Enhanced Features (3-4週間)
- [ ] 高度なPOMLテンプレート作成
- [ ] ドメイン特化テンプレート
- [ ] テンプレート管理システム
- [ ] 品質保証機能強化

### Phase 4: Production Readiness (2-3週間)
- [ ] パフォーマンス最適化
- [ ] エラーハンドリング強化
- [ ] ドキュメント完成
- [ ] ユーザーテスト・フィードバック統合

## 8. 技術的考慮事項

### 8.1 パフォーマンス最適化
- POMLテンプレートのキャッシュ機能
- エージェント初期化の最適化
- 大規模プロジェクトでのスケーラビリティ

### 8.2 エラーハンドリング
- 不正なPOMLテンプレートの処理
- エージェント実行エラーの回復
- ワークフロー中断時の状態保存

### 8.3 セキュリティ
- POMLテンプレートの検証
- 実行権限の管理
- 機密情報の適切な処理

### 8.4 拡張性
- プラグインシステムの設計
- カスタムエージェントの追加方法
- サードパーティPOMLテンプレートの統合

## 9. まとめ

このハイブリッドアーキテクチャにより、CC-Deck v2は以下を実現します：

### 利点
1. **安定性**: 固定インフラによる信頼性の高い基本機能
2. **柔軟性**: POMLによる動的な挙動制御
3. **拡張性**: 新しい要件への迅速な対応
4. **保守性**: 明確な責任分離による管理のしやすさ
5. **再利用性**: POMLテンプレートの共有と再利用

### 成功指標
- 開発効率の向上 (50%+ の作業時間短縮)
- コード品質の向上 (95%+ テストカバレッジ)
- 柔軟性の確保 (新要件への1日以内対応)
- 学習コストの削減 (1週間以内のプラットフォーム習得)

この設計により、「数時間の作業を1つのコマンドに変換」するというCC-Deck v2の目標を達成しながら、多様なプロジェクト要件に対応できるプラットフォームを構築できます。