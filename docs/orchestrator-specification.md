# オーケストレーター責務仕様書

## 概要

CC-Deck v2におけるオーケストレーターは、**統一された自然言語インターフェース**として機能し、ユーザーの自然言語指示を解析してプロジェクトコンテキストに基づいて適切なサブエージェントにタスクを委譲する中核コンポーネントです。`/orchestrator`コマンドによる単一エントリーポイントで、Spec→Impl→Validの各フェーズを統合的に管理します。

## 基本設計原則

### 1. **統一インターフェース型アーキテクチャ**
- **単一エントリーポイント**: `/orchestrator`コマンドによる自然言語インターフェース
- **プロンプト解析**: 自然言語からのパラメータ抽出と意図理解
- **サブエージェント調整**: Claude Code Sub-agentの適切な選択と呼び出し

### 2. **プロジェクトコンテキスト駆動モデル**
- **必須プロジェクト特定**: すべての操作でプロジェクトコンテキストが必要
- **フェーズベース制御**: spec.jsonによる現在フェーズと承認状況の管理
- **状態整合性**: プロジェクトごとの独立した状態管理

### 3. **POML挙動制御統合**
- **既存テンプレート活用**: 事前定義されたbehavior.pomlファイルの使用
- **動的挙動適用**: サブエージェントへのPOML behavior指示
- **責任分離**: orchestrator（調整）+ sub-agent（実行）+ POML（挙動制御）

## 核心責務定義

### A. ワークフロー制御責務

#### A1. 自然言語プロンプト解析
```typescript
interface PromptAnalysis {
  // プロンプトからパラメータを抽出
  extractParameters(prompt: string): ExtractedParams
  
  // プロジェクト特定（必須）
  identifyProject(prompt: string): ProjectIdentification
  
  // コマンド種別の識別
  classifyCommand(prompt: string): CommandType
  
  // 挙動テンプレートの特定
  detectBehavior(prompt: string): BehaviorTemplate
}

interface ExtractedParams {
  project: string
  feature?: string
  command: 'review' | 'implement' | 'spec' | 'continue' | 'approve'
  target?: string
  behavior?: string
}
```

**実装要件**:
- orchestrator.pomlによるプロンプト解析ルール
- プロジェクト不明時の対話的確認
- キーワードベースのパラメータ抽出
- 曖昧さ解決のためのユーザー確認

#### A2. プロジェクトコンテキスト管理
```typescript
interface ProjectContext {
  // プロジェクト状態の読み込み
  loadProjectState(project: string, feature?: string): ProjectState
  
  // フェーズと承認状況の確認
  validatePhaseStatus(state: ProjectState): PhaseValidation
  
  // 実行制約の検証
  checkExecutionConstraints(command: CommandType, state: ProjectState): ConstraintCheck
  
  // 次アクションの提案
  suggestNextActions(state: ProjectState): ActionSuggestions
}

interface ProjectState {
  phase: string
  approvals: ApprovalStatus
  progress: ProgressMetrics
  context_references: string[]
  mcp_tools: string[]
}
```

**実装要件**:
- project.json と spec.json の読み込み
- フェーズベースの実行制約確認
- 承認ゲートの自動チェック
- ステアリングファイルの考慮

### B. エージェント管理責務

#### B1. サブエージェント選択と実行
```typescript
interface AgentOrchestration {
  // コマンドに基づくエージェント選択
  selectSubAgent(command: CommandType): SubAgentConfig
  
  // POML behavior templateの特定
  identifyBehaviorTemplate(behavior: string): BehaviorTemplate | null
  
  // サブエージェント呼び出し
  invokeSubAgent(agent: SubAgentConfig, context: ExecutionContext, behavior?: BehaviorTemplate): Promise<AgentResult>
  
  // 実行結果の統合
  integrateResults(results: AgentResult[]): IntegratedResult
}

interface ExecutionContext {
  project: string
  feature?: string
  target?: string
  projectState: ProjectState
  behaviorInstructions?: string
}
```

**実装要件**:
- Claude Code Sub-agentの適切な選択
- behavior.pomlファイルの読み込みと適用指示
- サブエージェントへのコンテキスト情報渡し
- 実行結果の品質確認と統合

#### B2. エラーハンドリングと復旧
```typescript
interface ErrorRecovery {
  // プロンプト解析エラーの処理
  handlePromptAnalysisError(prompt: string): PromptClarificationRequest
  
  // プロジェクト特定失敗時の対話
  handleProjectIdentificationFailure(): ProjectSelectionDialog
  
  // サブエージェント実行エラーの処理
  handleSubAgentError(error: SubAgentError): ErrorRecoveryStrategy
  
  // フェーズ制約違反の処理
  handlePhaseConstraintViolation(violation: ConstraintViolation): ConstraintGuidance
}
```

**実装要件**:
- 自然言語プロンプトの曖昧さ解決
- プロジェクト不明時の対話的確認
- サブエージェント失敗時の適切なフィードバック
- フェーズ制約エラーの明確な説明と次アクション提案

### C. 品質制御責務

#### C1. 状態更新と承認制御
```typescript
interface StateManagement {
  // プロジェクト状態の更新
  updateProjectState(project: string, feature: string, updates: StateUpdates): UpdateResult
  
  // spec.jsonの承認状況更新
  updateApprovalStatus(project: string, feature: string, phase: string, approved: boolean): UpdateResult
  
  // 承認ゲート検証
  validateApprovalGates(project: string, feature: string, targetPhase: string): ApprovalGateResult
  
  // 次アクションの提案
  suggestNextActions(currentState: ProjectState): ActionSuggestions
  
  // 進捗レポートの生成
  generateProgressReport(project: string): ProgressReport
}

interface ApprovalGateResult {
  canProceed: boolean
  missingApprovals: string[]
  requiredActions: string[]
}
```

**実装要件**:
- spec.jsonファイルの正確な読み書き
- 承認ゲートの自動チェックと進行制御
- 人間承認必須フェーズの識別と制約実施
- フェーズベースの次アクション提案
- ユーザーフレンドリーな進捗表示

**承認コマンド処理**:
```bash
# 承認コマンドの解析と処理
/orchestrator approve requirements for fintech-app user-auth
→ UpdateApprovalStatus("fintech-app", "user-auth", "requirements", true)
→ ValidateApprovalGates() → 次フェーズ進行可否判定
→ SuggestNextActions() → "design generation ready"
```

#### C2. フィードバック管理
```typescript
interface FeedbackManagement {
  // サブエージェント実行結果の統合
  integrateSubAgentResults(results: SubAgentResult[]): IntegratedResult
  
  // 実行サマリーの生成
  generateExecutionSummary(execution: ExecutionContext): ExecutionSummary
  
  // ユーザーへの結果報告
  reportResultsToUser(summary: ExecutionSummary): UserReport
  
  // 次回実行のための学習
  learnFromExecution(execution: CompletedExecution): LearningInsights
}
```

**実装要件**:
- サブエージェント結果の品質確認
- わかりやすい実行サマリー生成
- 次回の改善点の識別
- 実行履歴の蓄積

### D. コンテキスト管理責務

#### D1. 状態永続化と復元
```typescript
interface StateManagement {
  // ワークフロー状態の永続化
  persistWorkflowState(state: WorkflowState): PersistenceResult
  
  // セッション間での状態復元
  restoreWorkflowState(project: ProjectId): RestorationResult
  
  // コンテキストの版管理
  versionContextData(context: ProjectContext): VersionedContext
  
  // 状態の整合性検証
  validateStateConsistency(state: WorkflowState): ConsistencyReport
}
```

**実装要件**:
- JSONベースの状態ファイル管理
- セッション再開時の完全な状態復元
- コンテキスト変更の追跡可能な履歴管理
- 破損検出と自動修復メカニズム

#### D2. クロスプロジェクト学習
```typescript
interface CrossProjectLearning {
  // プロジェクト間での知識共有
  shareKnowledgeAcrossProjects(learnings: ProjectLearnings): SharingResult
  
  // 類似プロジェクトからの洞察活用
  leverageSimilarProjectInsights(currentProject: ProjectProfile): ApplicableInsights
  
  // 組織レベルのベストプラクティス更新
  updateOrganizationalBestPractices(aggregatedLearnings: LearningDatabase): UpdateResult
  
  // パターン認識と推奨
  recognizePatternsAndRecommend(projectContext: ProjectContext): RecommendationSet
}
```

**実装要件**:
- プロジェクトプロファイリングと類似性分析
- 知識グラフによる学習内容の構造化
- プライバシー保護された組織学習
- 推奨システムによる最適化提案

## 実装アーキテクチャ

### アーキテクチャ概要
```
┌─────────────────────────────────────────┐
│ User Interface Layer                    │
│ /orchestrator [自然言語指示]            │
└─────────────┬───────────────────────────┘
              │
┌─────────────▼───────────────────────────┐
│ Prompt Analysis Engine                  │
│ - Natural Language Parameter Extraction│
│ - Project Context Loading              │
│ - Command Classification               │
└─────────────┬───────────────────────────┘
              │
┌─────────────▼───────────────────────────┐
│ Routing & Execution Engine              │
│ - Sub-Agent Selection                   │
│ - Behavior Template Application        │
│ - Execution Coordination               │
└─────────────┬───────────────────────────┘
              │
┌─────────────▼───────────────────────────┐
│ State Management Layer                  │
│ - spec.json State Persistence          │
│ - Project Context Management           │
│ - Approval Status Tracking             │
└─────────────────────────────────────────┘
```

### 主要実行フロー

#### 統一orchestrator実行フロー（11ステップ）

1. **コマンド起動**: `/orchestrator [natural language instruction]`
2. **POML テンプレート読み込み**: `orchestrator.poml`の実行
3. **変数初期化**: ユーザープロンプトの受け取り
4. **プロンプト解析**: パラメータ抽出（project, command, target, behavior, feature）
5. **プロジェクト確認**: 不明時は対話的確認
6. **プロジェクトコンテキスト読み込み**: `spec.json`と`project.json`
7. **フェーズ検証**: 現在フェーズと実行制約の確認
8. **コマンドルーティング**: 適切なサブエージェントの選択
9. **挙動テンプレート適用**: `behavior.poml`の指定（該当時）
10. **サブエージェント実行**: 選択されたエージェントでタスク実行
11. **結果生成と状態更新**: 結果統合、`spec.json`更新、次アクション提案

#### 主要インターフェース

```typescript
// プロンプト解析 → ルーティング
interface PromptToRouting {
  extractedParams: ExtractedParams
  projectContext: ProjectContext
  executionConstraints: ConstraintCheck[]
}

// ルーティング → サブエージェント
interface RoutingToSubAgent {
  selectedAgent: SubAgentConfig
  behaviorTemplate?: BehaviorTemplate
  executionContext: ExecutionContext
}

// サブエージェント → 状態管理
interface SubAgentToState {
  executionResult: SubAgentResult
  stateUpdates: StateUpdates
  nextActionSuggestions: ActionSuggestions
}
```

## 品質・性能要件

### 機能的要件
- **プロンプト解析精度**: 95%以上の意図理解成功率
- **プロジェクト特定**: 必須プロジェクトコンテキストの100%確保
- **フェーズ整合性**: spec.jsonベースの状態管理100%準拠
- **エラーハンドリング**: わかりやすいエラーメッセージと次アクション提案

### 非機能的要件
- **応答性**: プロンプト解析3秒以内、サブエージェント起動10秒以内
- **使いやすさ**: 自然言語による直感的なインターフェース
- **拡張性**: 新しいbehavior.pomlテンプレートの追加対応
- **保守性**: orchestrator.pomlによる設定管理

### セキュリティ要件
- **認証**: ユーザー認証とプロジェクトアクセス制御
- **認可**: ロールベースのワークフロー操作権限
- **監査**: すべての操作の完全な監査ログ
- **暗号化**: 機密データの保存時・転送時暗号化

## 実装ロードマップ

### Phase 1: MVP Orchestrator (Week 1-2)
- [ ] 自然言語プロンプト解析機能
- [ ] orchestrator.pomlテンプレート実装
- [ ] 基本的なサブエージェント呼び出し
- [ ] spec.json状態管理

### Phase 2: 挙動制御統合 (Week 3-4)
- [ ] behavior.pomlテンプレート読み込み
- [ ] プロジェクトコンテキスト管理
- [ ] フェーズ制約チェック
- [ ] エラーハンドリングと対話機能

### Phase 3: 品質向上 (Week 5-6)
- [ ] プロンプト解析精度向上
- [ ] より多くのbehaviorテンプレート
- [ ] 実行結果の品質チェック
- [ ] ユーザーエクスペリエンス改善

### Phase 4: 拡張機能 (Week 7-8)
- [ ] 複数プロジェクト並列サポート
- [ ] 高度なコンテキスト管理
- [ ] パフォーマンス最適化
- [ ] 包括的なドキュメント完成

## 成功指標

### 開発効率指標
- **コマンド成功率**: 90%以上のワンショット成功率
- **プロンプト理解精度**: 95%以上の意図理解
- **プロジェクト特定精度**: 100%の必須コンテキスト確保
- **エラー解決時間**: 平均2分以内の問題解決

### 品質指標
- **状態整合性**: spec.jsonベースの100%正確な状態管理
- **フェーズ制約遵守**: 100%のワークフロー順序準拠
- **サブエージェント成功率**: 95%以上のタスク完了率
- **behavior適用精度**: 意図されたPOML挙動の正確な適用

### ユーザー体験指標
- **直感性**: 自然言語による簡単なコマンド実行
- **学習コスト**: 基本操作30分以内の習得
- **エラー理解**: わかりやすいエラーメッセージと修正ガイド
- **進捗可視性**: 明確な現在状態と次アクションの提示

## 拡張性・将来性

### 技術進化への対応
- **AIモデル更新**: 新しいClaude版への適応メカニズム
- **POML仕様変更**: テンプレートシステムの後方互換性
- **ツール生態系**: 新ツール統合のプラグインアーキテクチャ

### 機能拡張性
- **カスタムワークフロー**: 組織固有のワークフロー定義
- **業界特化**: ドメイン特化の品質基準とプラクティス
- **多言語対応**: 国際チーム向けの多言語インターフェース

### スケール対応
- **エンタープライズ規模**: 数千プロジェクトの同時管理
- **クラウドネイティブ**: Kubernetes環境での水平スケール
- **グローバル分散**: 地理的分散チームのサポート

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-01-17  
**ステータス**: 設計承認待ち  
**次回レビュー**: 実装Phase 1完了後