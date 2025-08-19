# サブエージェント責務仕様書

## 概要

CC-Deck v2におけるサブエージェントは、**専門領域に特化した実行ユニット**として機能し、オーケストレーターからの指示に基づいて具体的なタスクを実行します。Anthropic仕様に完全準拠し、Task()ツールを使用せず、与えられたコンテキストで自律的に作業を完了する責務を持ちます。

## 基本設計原則

### 1. **専門特化型アーキテクチャ**
- **単一責務**: 明確に定義された専門領域に特化
- **自律実行**: 与えられたコンテキストで完全なタスク実行
- **結果責任**: 品質基準を満たす成果物の生成

### 2. **Anthropic仕様準拠**
- **Task()禁止**: 他のエージェントを呼び出さない
- **ツール制限**: 必要最小限のツールセットのみ使用
- **独立性**: オーケストレーター以外との直接通信なし

### 3. **品質重視の実行**
- **標準遵守**: 組織・プロジェクトの品質基準に準拠
- **検証責任**: 自身の出力品質を自己検証
- **改善志向**: フィードバックに基づく継続的品質向上

## エージェント分類と責務

### A. 仕様系エージェント (Specification Agents)

#### A1. spec-creation-expert
**専門領域**: 要求分析、仕様設計、技術文書作成

```typescript
interface SpecificationAgent {
  // 要求分析と仕様抽出
  analyzeRequirements(input: UserInput): RequirementsAnalysis
  
  // 技術仕様の設計
  designTechnicalSpecification(requirements: Requirements): TechnicalSpec
  
  // 実装タスクの分解
  breakdownImplementationTasks(spec: TechnicalSpec): TaskBreakdown
  
  // 受け入れ基準の定義
  defineAcceptanceCriteria(spec: TechnicalSpec): AcceptanceCriteria
}
```

**具体的責務**:
- **要求分析**: ユーザー入力から機能要件・非機能要件を抽出
- **技術設計**: アーキテクチャ、データモデル、API設計の作成
- **文書生成**: requirements.md, technical-design.md, tasks.md の生成
- **品質保証**: EARS+記法、設計原則への準拠確認

**成果物**:
```
specs/
├── requirements.md         # 機能・非機能要件
├── technical-design.md     # 技術設計・アーキテクチャ
├── tasks.md               # 実装タスク分解
└── acceptance-tests.md    # 受け入れテスト仕様
```

**品質基準**:
- **完全性**: 実装に必要なすべての情報を含む
- **明確性**: 曖昧さのない具体的な記述
- **実装可能性**: 技術的に実現可能な設計
- **テスタビリティ**: 検証可能な受け入れ基準

#### A2. requirements-analyst
**専門領域**: 要求工学、ステークホルダー分析、要件管理

```typescript
interface RequirementsAnalyst {
  // ステークホルダー分析
  analyzeStakeholders(context: ProjectContext): StakeholderAnalysis
  
  // 要求の優先順位付け
  prioritizeRequirements(requirements: Requirement[]): PrioritizedRequirements
  
  // 要求の依存関係分析
  analyzeDependencies(requirements: Requirement[]): DependencyGraph
  
  // 要求の変更管理
  manageRequirementsChange(changes: RequiementChange[]): ChangeImpactAnalysis
}
```

### B. 実装系エージェント (Implementation Agents)

#### B1. impl-agent (TDD Implementation)
**専門領域**: テスト駆動開発、コード実装、品質保証

```typescript
interface ImplementationAgent {
  // テスト仕様からテストコード生成
  generateTestCode(acceptance: AcceptanceCriteria): TestCode
  
  // TDDサイクルでの実装
  implementWithTDD(tests: TestCode, design: TechnicalSpec): Implementation
  
  // コード品質検証
  verifyCodeQuality(code: Implementation): QualityReport
  
  // 統合とデプロイ準備
  prepareIntegration(implementation: Implementation): IntegrationPackage
}
```

**具体的責務**:
- **テスト先行開発**: 受け入れ基準からテストコード生成
- **実装**: 設計仕様に基づく機能実装
- **品質検証**: 静的解析、カバレッジ測定、セキュリティチェック
- **文書化**: コード内ドキュメント、API仕様の生成

**成果物**:
```
src/
├── main.py                # メイン実装コード
├── models/               # データモデル
├── services/             # ビジネスロジック
├── api/                  # API エンドポイント
tests/
├── unit/                 # ユニットテスト
├── integration/          # 統合テスト
└── e2e/                  # E2Eテスト
```

**品質基準**:
- **テストカバレッジ**: 95%以上
- **静的解析**: リンタエラーゼロ
- **セキュリティ**: 脆弱性スキャン合格
- **パフォーマンス**: 設計仕様の性能要件達成

#### B2. frontend-specialist
**専門領域**: UI/UX実装、フロントエンド技術、アクセシビリティ

```typescript
interface FrontendSpecialist {
  // UI コンポーネント設計・実装
  implementUIComponents(design: UIDesign): ComponentImplementation
  
  // ユーザー体験最適化
  optimizeUserExperience(components: Component[]): UXOptimization
  
  // アクセシビリティ対応
  ensureAccessibility(implementation: FrontendCode): AccessibilityReport
  
  // パフォーマンス最適化
  optimizePerformance(app: FrontendApplication): PerformanceOptimization
}
```

### C. 検証系エージェント (Validation Agents)

#### C1. test-automation-expert
**専門領域**: テスト自動化、品質保証、継続的インテグレーション

```typescript
interface TestAutomationExpert {
  // テスト戦略の設計
  designTestStrategy(requirements: Requirements): TestStrategy
  
  // 自動テストスイートの構築
  buildAutomatedTestSuite(strategy: TestStrategy): TestSuite
  
  // CI/CDパイプラインの設計
  designCICDPipeline(project: ProjectConfig): PipelineDefinition
  
  // テスト結果分析とレポート
  analyzeTestResults(results: TestResults): QualityReport
}
```

**具体的責務**:
- **テスト計画**: ユニット、統合、E2E、パフォーマンステストの計画
- **自動化**: テスト実行の完全自動化
- **CI/CD統合**: 継続的インテグレーション・デプロイメントの設定
- **品質監視**: テスト結果の継続的監視とレポート

#### C2. security-auditor
**専門領域**: セキュリティ監査、脆弱性検査、セキュア実装検証

```typescript
interface SecurityAuditor {
  // セキュリティ要件の検証
  auditSecurityRequirements(spec: SecuritySpec): SecurityAudit
  
  // 脆弱性スキャンと分析
  performVulnerabilityAssessment(code: Implementation): VulnerabilityReport
  
  // セキュア実装ガイドライン適用
  enforceSecurityStandards(implementation: Implementation): ComplianceReport
  
  // セキュリティテストの実行
  executeSecurityTests(application: Application): SecurityTestResults
}
```

## 共通インターフェース仕様

### 入力インターフェース
```typescript
interface AgentInput {
  // オーケストレーターからの指示
  instruction: AgentInstruction
  
  // プロジェクトコンテキスト
  projectContext: ProjectContext
  
  // 前段階からの成果物
  previousArtifacts: Artifact[]
  
  // 品質要件・制約
  qualityRequirements: QualityStandards
}

interface AgentInstruction {
  taskType: TaskType
  priority: Priority
  deadline?: Timestamp
  specificRequirements: Requirement[]
}
```

### 出力インターフェース
```typescript
interface AgentOutput {
  // 実行ステータス
  status: ExecutionStatus
  
  // 生成された成果物
  artifacts: GeneratedArtifact[]
  
  // 品質メトリクス
  qualityMetrics: QualityMetrics
  
  // 次段階への引き継ぎ情報
  handoffContext: HandoffContext
  
  // 実行ログとメタデータ
  executionLog: ExecutionLog
}

interface GeneratedArtifact {
  type: ArtifactType
  path: FilePath
  content: string
  metadata: ArtifactMetadata
  qualityScore: QualityScore
}
```

### エラーハンドリング
```typescript
interface AgentError {
  // エラーの分類
  errorType: ErrorType
  
  // エラーの詳細
  errorDetails: ErrorDetails
  
  // 推奨復旧アクション
  recoveryActions: RecoveryAction[]
  
  // 部分的成果物（可能な場合）
  partialResults?: PartialArtifact[]
}
```

## 品質保証メカニズム

### A. 自己検証責務
各サブエージェントは自身の出力品質を検証する責務を持ちます。

```typescript
interface SelfValidation {
  // 成果物の完全性検証
  validateCompleteness(artifacts: Artifact[]): CompletenessReport
  
  // 品質基準への準拠確認
  validateQualityStandards(output: AgentOutput): StandardsComplianceReport
  
  // 一貫性チェック
  validateConsistency(artifacts: Artifact[]): ConsistencyReport
  
  // 実装可能性検証（仕様系エージェント）
  validateImplementability(specification: Specification): ImplementabilityReport
}
```

### B. 相互検証メカニズム
```typescript
interface CrossValidation {
  // 前段階成果物との整合性確認
  validateAlignmentWithPrevious(current: Artifact[], previous: Artifact[]): AlignmentReport
  
  // 要件トレーサビリティ確認
  validateRequirementTraceability(implementation: Implementation, requirements: Requirements): TraceabilityReport
  
  // アーキテクチャ準拠確認
  validateArchitecturalCompliance(code: Implementation, architecture: Architecture): ComplianceReport
}
```

## パフォーマンス要件

### 実行時間要件
- **仕様系エージェント**: 15分以内での仕様生成完了
- **実装系エージェント**: 1時間以内でのMVP実装完了
- **検証系エージェント**: 30分以内での全テスト実行完了

### 品質要件
- **正確性**: 95%以上の要件実装率
- **完全性**: 100%の必須成果物生成
- **一貫性**: ゼロの論理矛盾
- **保守性**: 90%以上のコード品質スコア

### リソース使用量
- **メモリ**: 4GB以内での実行
- **CPU**: 並列実行時の適切な負荷分散
- **ストレージ**: 効率的な一時ファイル管理

## 継続的改善メカニズム

### A. 学習データ収集
```typescript
interface LearningDataCollection {
  // 実行パフォーマンスデータ
  collectPerformanceMetrics(execution: AgentExecution): PerformanceData
  
  // 品質改善データ
  collectQualityImprovements(before: Artifact[], after: Artifact[]): ImprovementData
  
  // エラーパターンデータ
  collectErrorPatterns(errors: AgentError[]): ErrorPatternData
  
  // 成功事例データ
  collectSuccessPatterns(successes: SuccessfulExecution[]): SuccessPatternData
}
```

### B. 能力向上メカニズム
```typescript
interface CapabilityImprovement {
  // プロンプト最適化
  optimizePromptEffectiveness(feedback: QualityFeedback): PromptOptimization
  
  // 品質基準の更新
  updateQualityStandards(learnings: QualityLearning[]): StandardsUpdate
  
  // エラー防止策の強化
  strengthenErrorPrevention(patterns: ErrorPattern[]): PreventionStrategy
  
  // 効率性改善
  improveExecutionEfficiency(metrics: PerformanceMetrics[]): EfficiencyImprovement
}
```

## 実装ガイドライン

### A. エージェント開発標準

#### 1. ファイル構造
```
.claude/agents/
├── {domain}/
│   ├── {agent-name}.md              # エージェント定義
│   ├── {agent-name}-prompts/        # プロンプトテンプレート
│   │   ├── base-prompt.md          # 基本プロンプト
│   │   ├── quality-checklist.md   # 品質チェックリスト
│   │   └── error-handling.md      # エラー処理ガイド
│   └── {agent-name}-examples/      # 実行例とテストケース
```

#### 2. エージェント定義テンプレート
```markdown
---
name: {agent-name}
description: {specific-responsibility-description}
domain: {specification|implementation|validation}
tools: [Read, Write, Edit, LS]  # 必要最小限のみ
quality_standards: {applicable-standards}
---

# {Agent Name}

## Specialized Responsibility
[Single, clear responsibility definition]

## Input Requirements
[Expected input format and validation]

## Output Specifications  
[Required output format and quality criteria]

## Quality Assurance
[Self-validation procedures]

## Error Handling
[Error detection and recovery procedures]
```

#### 3. 品質基準適用
- **コード規約**: 組織標準への準拠
- **文書規約**: 統一されたフォーマット
- **命名規約**: 一貫した命名パターン
- **バージョン管理**: 成果物のバージョン追跡

### B. 統合テスト要件

#### 1. 単体テスト
```typescript
interface AgentUnitTest {
  // 正常系実行テスト
  testNormalExecution(input: ValidInput): TestResult
  
  // 異常系実行テスト  
  testErrorHandling(input: InvalidInput): ErrorHandlingResult
  
  // 品質基準遵守テスト
  testQualityCompliance(output: AgentOutput): ComplianceResult
  
  // パフォーマンステスト
  testPerformanceRequirements(workload: TestWorkload): PerformanceResult
}
```

#### 2. 統合テスト
```typescript
interface AgentIntegrationTest {
  // オーケストレーター連携テスト
  testOrchestratorIntegration(scenario: IntegrationScenario): IntegrationResult
  
  // エージェント間データ受け渡しテスト
  testDataHandoff(fromAgent: Agent, toAgent: Agent): HandoffResult
  
  // ワークフロー全体テスト
  testEndToEndWorkflow(workflow: WorkflowDefinition): E2EResult
}
```

## 拡張と将来性

### A. 新エージェント追加プロセス
1. **領域分析**: 新しい専門領域の特定
2. **責務定義**: 明確な責務境界の設定
3. **インターフェース設計**: 標準インターフェースへの準拠
4. **品質基準設定**: ドメイン固有の品質要件
5. **統合テスト**: 既存システムとの統合確認

### B. エージェント進化戦略
- **能力拡張**: 新技術・フレームワークへの対応
- **品質向上**: 継続的な出力品質の改善
- **効率化**: 実行速度とリソース使用量の最適化
- **インテリジェンス**: より高度な判断能力の獲得

### C. エコシステム統合
- **外部ツール連携**: IDE、CI/CD、監視ツールとの統合
- **業界標準準拠**: 各種業界標準・規格への対応
- **企業カスタマイズ**: 組織固有の要件への適応

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-01-17  
**ステータス**: 設計承認待ち  
**関連文書**: orchestrator-specification.md, hybrid-agent-architecture.md