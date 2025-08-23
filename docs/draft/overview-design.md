# CC-Deck v2 概要設計書

## 1. システム概要

### 1.1 プロジェクト名

CC-Deck v2 (Claude Code Development Platform v2)

### 1.2 目的

**仕様、実装、評価に特化した AI 駆動開発プラットフォーム**として、カスタムスラッシュコマンドとサブエージェントを効果的に活用し、高品質なドキュメント、コード、テスト環境を提供することで、AI による開発基盤を確立する。

**従来の cc-deck との違い**: CC-Deck v2 は新規作成・継続開発の両方において、「数時間の作業を 1 つのコマンドに変換」することで、開発プロセスを根本的に簡素化・自動化する。

### 1.3 設計思想

- **Specification Excellence**: AI 支援による高品質な仕様書・設計書・技術文書の生成
- **Implementation Quality**: 95%+ カバレッジを目標とした堅牢なコード実装
- **Comprehensive Evaluation**: ユニット・統合・E2E・パフォーマンステストの完全自動化
- **Custom Slash Commands**: 複雑なワークフローを簡潔なコマンドに変換
- **Specialized Sub-Agents**: ドメイン特化 AI エージェントによる高度作業の自動化
- **AI-Native Development**: Claude Code の能力を最大限活用した開発ワークフロー
- **Human-Centric Approval**: 重要な決定には人間の承認が必要
- **Multi-Language Support**: 言語に依存しない汎用プラットフォーム

### 1.4 対象技術スタック

- **Languages**: TypeScript, JavaScript, Python, Go, Rust, Java, C#, PHP, Ruby
- **Frameworks**: Next.js, React, Vue, FastAPI, Express, Spring Boot 等
- **Infrastructure**: Docker, Kubernetes, AWS, GCP, Azure 対応

## 2. アーキテクチャ概要

### 2.1 システム全体構成

```
┌─────────────────────────────────────────────────────────────┐
│                    CC-Deck v2 Platform                     │
├─────────────────────────────────────────────────────────────┤
│  User Interface Layer                                      │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ Unified     │ │ Natural     │ │ Interactive │         │
│  │ Orchestrator│ │ Language    │ │ Approval    │         │
│  │ /orchestrator│ │ Interface   │ │ Workflow    │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
├─────────────────────────────────────────────────────────────┤
│  Workflow Engine Layer (POML + YAML Hybrid)               │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ Agent       │ │ Template    │ │ Orchestrator│         │
│  │ Definitions │ │ System      │ │ Engine      │         │
│  │ (.poml)     │ │ (.poml)     │ │ (.poml)     │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ Quality     │ │ CI/CD       │ │ Project     │         │
│  │ Gates       │ │ Integration │ │ Defaults    │         │
│  │ (.yaml)     │ │ (.yaml)     │ │ (.yaml)     │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
├─────────────────────────────────────────────────────────────┤
│  Execution Layer                                          │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ CES         │ │ Implementation│ │ Validation  │         │
│  │ Specification│ │ Engine      │ │ Engine      │         │
│  │ System      │ │             │ │             │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐         │
│  │ MCP         │ │ State       │ │ Quality     │         │
│  │ Integration │ │ Management  │ │ Assurance   │         │
│  │             │ │             │ │             │         │
│  └─────────────┘ └─────────────┘ └─────────────┘         │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 コアコンポーネント

#### 2.2.1 Claude Code Enhanced Specification System (CES)

- **役割**: 仕様作成専門システム
- **機能**: EARS+記法、コンテキスト統合、ファイル参照連携
- **技術**: POML-based agent orchestration

#### 2.2.2 POML + YAML Hybrid Workflow Engine

- **POML 担当**: AI エージェント定義、動的ワークフロー、コンテキスト管理
- **YAML 担当**: インフラ設定、CI/CD 統合、品質ゲート
- **統合点**: Orchestrator.poml が YAML 設定を参照
- **コンテキスト継承**: State-Driven Context Persistence による workflow 間の情報継承

#### 2.2.3 Multi-Language Implementation Engine

- **対応言語**: TypeScript, Python, Go, Rust, Java 等
- **テンプレート**: 言語別プロジェクト生成
- **品質保証**: 言語固有の lint, test, build 設定

## 3. データ構造設計

`

### 3.1 プロジェクト構造

```
projects/{project-name}/
├── project.json              # プロジェクトメタデータ
├── specs/                    # CES仕様ディレクトリ
│   └── {feature-name}/
│       ├── spec.json         # 状態管理 (フェーズ、承認、進捗)
│       ├── requirements.md   # EARS+要件定義
│       ├── design.md         # AI支援技術設計
│       ├── tasks.md          # 実装タスク
│       ├── context.md        # 機能固有コンテキスト
│       ├── steering.md       # 適応的ステアリング
│       └── workflow.poml     # POML-based AIワークフロー
└── src/                      # 実装ファイル (言語依存)
```

### 3.2 ワークフロー構造

```
poml/
├── commands/                          # オーケストレーター用POMLテンプレート
│   └── orchestrator.poml              # 統一コマンドオーケストレーター（プロンプト解析・ルーティング）
└── agents/                            # エージェント挙動制御用POMLテンプレート（既存ファイル）
    ├── development-styles/
    │   ├── rapid-prototyping.poml     # 高速プロトタイピング
    │   ├── enterprise-development.poml# エンタープライズ開発
    │   └── test-driven-development.poml# TDD重視開発
    ├── review-styles/
    │   ├── strict-security-review.poml# 厳格セキュリティレビュー
    │   ├── performance-focused.poml   # パフォーマンス重視
    │   └── code-quality-review.poml   # コード品質レビュー
    └── testing-styles/
        ├── comprehensive-testing.poml # 包括的テスト
        └── unit-test-focused.poml     # ユニットテスト重視
```

## 4. 主要ワークフロー

### 4.1 Specification Workflow (SPEC)

```
Input: ユーザー要件/プロジェクトアイデア
↓
1. Requirements Analysis (CES + MCP研究)
↓
2. Technical Design (AI支援設計)
↓
3. Task Breakdown (TDD実装計画)
↓
Output: 完全な仕様 in specs/{project-name}/{feature-name}/
Approval: requirements.md, design.md, tasks.md に人間承認必須
```

### 4.2 Implementation Workflow (IMPL)

```
Input: 承認済み仕様
↓
1. Project Setup (言語別テンプレート)
↓
2. TDD Cycles (テスト駆動開発)
↓
3. Integration (コンポーネント統合)
↓
4. Documentation (自動ドキュメント生成)
↓
Output: 動作する実装 in projects/{project-name}/src/
Quality Gates: 95%+テストカバレッジ、lint準拠、型安全性
Approval: 品質ゲート通過とセキュリティ審査完了後に進行可
```

### 4.3 Validation Workflow (VALID)

```
Input: 完了した実装
↓
1. End-to-End Testing (重要ユーザージャーニー)
↓
2. Performance Validation (メトリクスと閾値)
↓
3. User Acceptance (ステークホルダー承認)
↓
Output: 検証済み、本番対応アプリケーション
Approval: ステークホルダーサインオフ必須
```

## 5. 技術仕様

### 5.1 必須準拠仕様

- **Anthropic Claude Code**: https://docs.anthropic.com/en/docs/claude-code/
- **Sub-Agents**: https://docs.anthropic.com/en/docs/claude-code/sub-agents
- **POML**: https://github.com/microsoft/poml

### 5.2 品質要件

- **テストカバレッジ**: 95%以上
- **パフォーマンス**: Core Web Vitals 準拠
- **アクセシビリティ**: WCAG 2.1 AA 準拠
- **セキュリティ**: 依存関係スキャン、シークレット検出

### 5.3 承認制御要件

- **spec.json状態管理**: 各フェーズの承認状況を正確に記録
- **承認ゲート**: 未承認成果物での次フェーズ進行を自動ブロック
- **承認コマンド**: `/orchestrator approve [type] for [project] [feature]`
- **承認表示**: 成果物に承認要件と承認コマンドを明示
- **制約実施**: orchestratorが承認状況を確認し、制約を実施

### 5.4 統合要件

- **MCP Servers**: Context7, DeepWiki, Brave Search
- **Version Control**: Git workflows with automated quality checks
- **CI/CD**: GitHub Actions, GitLab CI, Jenkins 対応

## 6. 実装フェーズ

### Phase 1: Core Infrastructure

- [ ] POML + YAML hybrid workflow engine
- [ ] CES specification system
- [ ] Basic project/feature management
- [ ] State-Driven Context Persistence system
- [ ] TypeScript/JavaScript support

### Phase 2: Multi-Language Support

- [ ] Python, Go, Rust template support
- [ ] Language-specific quality gates
- [ ] Universal testing frameworks
- [ ] Cross-language dependency management

### Phase 3: Advanced Features

- [ ] Advanced MCP integrations
- [ ] Enterprise features (monitoring, analytics)
- [ ] Custom plugin system
- [ ] Performance optimization

### Phase 4: Production Readiness

- [ ] Comprehensive documentation
- [ ] Security auditing
- [ ] Performance benchmarking
- [ ] Community ecosystem

## 7. 成功指標

### 7.1 開発速度

- 仕様から実装までの時間短縮
- バグレポートと手戻り削減
- 開発者満足度と生産性向上

### 7.2 品質指標

- テストカバレッジ率
- パフォーマンスベンチマーク準拠
- セキュリティ脆弱性件数
- アクセシビリティ準拠スコア

### 7.3 採用指標

- アクティブプロジェクト数
- 複数言語プロジェクト比率
- エンタープライズ採用率
- コミュニティ貢献度
