# サブエージェント責務仕様書

## 概要

CC-Deck v2におけるサブエージェントは、**専門領域に特化した実行ユニット**として機能し、orchestratorから指示を受けて具体的なタスクを実行します。Claude Code Sub-agent仕様に完全準拠し、Task()ツールを使用せず、orchestratorから渡されたコンテキストとPOML behaviorテンプレートに基づいて自律的に作業を完了する責務を持ちます。

## 基本設計原則

### 1. **専門特化型アーキテクチャ**
- **単一責務**: 明確に定義された専門領域に特化
- **自律実行**: 与えられたコンテキストで完全なタスク実行
- **結果責任**: 品質基準を満たす成果物の生成

### 2. **Claude Code仕様準拠**
- **Task()禁止**: 他のエージェントを呼び出さない（Anthropic仕様）
- **ツール制限**: 専門領域に必要な最小限のツールセットのみ使用
- **独立性**: orchestrator以外との直接通信なし
- **POML behavior統合**: 既存behavior.pomlテンプレートの読み込みと適用

### 3. **品質重視の実行**
- **標準遵守**: 組織・プロジェクトの品質基準に準拠
- **検証責任**: 自身の出力品質を自己検証
- **改善志向**: フィードバックに基づく継続的品質向上

## 現在のサブエージェント構成

CC-Deck v2では、簡潔で効果的な設計として、**必要最小限のコアサブエージェント**を提供します。

### Core Sub-agents

#### 1. spec-generator
**専門領域**: CES（Claude Code Enhanced Specification）仕様生成
**ファイルパス**: `.claude/agents/spec-generator.md`

**責務**:
- **要求分析**: ユーザー入力からEARS+記法での要件抽出
- **技術設計**: アーキテクチャ、データモデル、API設計の作成
- **タスク分解**: TDD実装タスクの詳細分解
- **品質保証**: CES仕様標準への準拠確認

**POML behavior統合**:
- デフォルト: 標準CES仕様生成
- `enterprise-development.poml`: エンタープライズ品質重視
- `rapid-prototyping.poml`: 高速プロトタイピング重視

**成果物**:
```
specs/{feature-name}/
├── spec.json              # 状態管理
├── requirements.md        # EARS+要件定義
├── design.md             # AI支援技術設計
├── tasks.md              # 実装タスク分解
└── context.md            # 機能固有コンテキスト
```

#### 2. code-reviewer
**専門領域**: コードレビュー、品質分析、セキュリティ監査
**ファイルパス**: `.claude/agents/code-reviewer.md`

**責務**:
- **コード品質分析**: 可読性、保守性、パフォーマンスの確認
- **セキュリティ監査**: 脆弱性検出、セキュリティベストプラクティス確認
- **標準準拠**: コーディング規約、アーキテクチャ原則への準拠確認
- **改善提案**: 具体的な修正提案と実装例の提供

**POML behavior統合**:
- デフォルト: 標準コード品質レビュー
- `strict-security-review.poml`: セキュリティ重視の厳格監査
- `performance-focused.poml`: パフォーマンス最適化重視
- `code-quality-review.poml`: コード品質・保守性重視

#### 3. implementation
**専門領域**: TDD実装、コード生成、統合
**ファイルパス**: `.claude/agents/implementation.md`

**責務**:
- **TDD実装**: Red→Green→Refactorサイクルでの実装
- **プロジェクト構造**: 言語別テンプレートでの構造生成
- **依存関係管理**: パッケージ管理、設定ファイル生成
- **統合作業**: コンポーネント統合、API接続

**POML behavior統合**:
- デフォルト: 標準TDD実装
- `rapid-prototyping.poml`: 高速プロトタイピング
- `enterprise-development.poml`: エンタープライズ品質実装
- `test-driven-development.poml`: TDD厳格遵守

#### 4. validation
**専門領域**: テスト実行、品質検証、パフォーマンス測定
**ファイルパス**: `.claude/agents/validation.md`

**責務**:
- **テスト実行**: ユニット、統合、E2Eテストの実行
- **品質測定**: カバレッジ、品質指標の測定
- **パフォーマンステスト**: 負荷テスト、レスポンス測定
- **検証レポート**: 包括的な検証結果レポート生成

**POML behavior統合**:
- デフォルト: 標準品質検証
- `comprehensive-testing.poml`: 包括的テスト実行
- `unit-test-focused.poml`: ユニットテスト重視
- `performance-validation.poml`: パフォーマンス検証重視

## POML Behavior統合仕様

### 基本統合パターン

すべてのサブエージェントは以下のパターンでPOML behaviorを統合します：

```markdown
## Process
1. Always load POML behavior template if provided: poml/agents/{behavior}.poml
2. Apply behavior modifications from POML
3. Execute tasks based on POML priorities and constraints
4. Generate output in POML-specified format

## Default Behavior (if no POML)
- 標準的な専門領域の処理を実行
- 基本的な品質基準を適用
- 汎用的な出力フォーマットを使用

## POML Integration
When behavior template is provided:
- Follow POML priorities exactly
- Apply POML constraints strictly  
- Use POML-defined approach
- Generate POML-specified output format
```

### Behavior Template適用方法

1. **Orchestratorからの指示**: どのbehavior.pomlを読み込むかを指定
2. **ファイル読み込み**: 指定されたPOMLファイルの内容を解析
3. **挙動修正**: 優先順位、制約、アプローチを実際の処理に適用
4. **出力調整**: POML指定のフォーマットで結果を生成

## サブエージェント実行例

### code-reviewer での strict-security-review 適用例

```markdown
Input from Orchestrator:
> Use the code-reviewer subagent to review authentication module
> for fintech-app project with strict-security-review behavior applied
> Load: poml/agents/review-styles/strict-security-review.poml

Process:
1. Load strict-security-review.poml
2. Apply security-focused priorities
3. Execute security audit with zero-tolerance constraints
4. Generate security audit report format

Output:
## セキュリティ監査レポート
プロジェクト: fintech-app
挙動: strict-security-review

### 検出された問題
🔴 Critical (2件): SQL injection, Weak password hashing
🟡 High (3件): Missing rate limiting, Session issues, CSRF protection

### 推奨事項
1. bcrypt 12ラウンド以上実装
2. Rate limiting 実装
3. CSRF token 実装

### 次のアクション
/orchestrator implement security fixes for fintech-app user-auth
```

## 品質基準

### 全サブエージェント共通
- **Claude Code仕様準拠**: Task()ツール使用禁止
- **独立実行**: orchestrator以外との通信なし
- **POML統合**: behavior templateの正確な適用
- **品質責任**: 出力品質の自己検証
- **承認対応**: 人間承認が必要な成果物の明確な識別

### エージェント固有品質基準

#### spec-generator
- **EARS+記法準拠**: 検証可能な要件記述
- **実装可能性**: 技術的実現性の確認
- **承認対象成果物**: requirements.md, design.md, tasks.md
- **承認前確認**: 完全性、一貫性、実装可能性の自己検証

#### code-reviewer  
- **セキュリティ基準**: 脆弱性の包括的検出
- **コード品質標準**: 保守性、可読性、パフォーマンス基準
- **承認推奨**: 重大な問題発見時の実装承認保留提案

#### implementation
- **95%+テストカバレッジ**: 品質ゲート必須
- **TDD遵守**: Red-Green-Refactorサイクル実行
- **品質ゲート通過**: lint、型チェック、セキュリティスキャン合格

#### validation
- **包括的テスト実行**: ユニット、統合、E2E、パフォーマンステスト
- **品質指標測定**: カバレッジ、性能、セキュリティ指標
- **最終承認準備**: ステークホルダー承認のための検証結果整理

## 将来の拡張

### 追加予定エージェント
- **deployment**: デプロイメント、CI/CD管理
- **documentation**: 技術文書、API仕様生成
- **architect**: アーキテクチャ設計、技術選定

### 拡張性確保
- 新しいbehavior.pomlテンプレートの追加対応
- 多言語プロジェクトサポート
- カスタムツールセット対応

---

**文書バージョン**: 1.0.0  
**作成日**: 2025-01-17  
**ステータス**: 設計承認待ち  
**関連文書**: orchestrator-specification.md, workflow-specification.md