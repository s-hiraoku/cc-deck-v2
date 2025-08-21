# サブエージェント呼び出し制御アーキテクチャ検討資料

## 核心的課題

**Anthropic仕様の制約**: サブエージェント内でTask()ツール使用禁止

この制約により、以下の設計が不可能：
- エージェントAがエージェントBを呼び出す階層構造
- エージェント間の直接的な連携
- 分散型のワークフロー制御

## 解決アプローチ：統一オーケストレーター方式（現在のアーキテクチャ）

### 現在の実装済み基本原則
```
/orchestrator (統一自然言語インターフェース)
    ├── spec-generator (CES仕様生成)
    ├── code-reviewer (品質分析・セキュリティ監査)
    ├── implementation (TDD実装・統合)
    └── validation (テスト実行・品質検証)
```

**現在のアーキテクチャ**: 全コマンドが`/orchestrator`経由で実行、自然言語プロンプト解析によるルーティング

### 現在の実装パターン（統一orchestrator）

```typescript
// 現在の統一orchestrator実行フロー
/orchestrator [natural language instruction]
  ↓
1. プロンプト解析: ExtractParameters(prompt) → {project, command, behavior, feature}
2. プロジェクト確認: IdentifyProject() → プロジェクトコンテキスト読み込み
3. 状態検証: ValidatePhaseStatus() → spec.json確認
4. ルーティング: SelectSubAgent(command) → 適切なsub-agent選択
5. behavior適用: LoadBehaviorTemplate(behavior) → POML読み込み（該当時）
6. 実行: InvokeSubAgent() → Task()でsub-agent呼び出し
7. 統合: IntegrateResults() → 結果統合、状態更新、次アクション提案
```

## エージェント間のコンテキスト共有

### 現在のファイルベース共有（spec.json状態管理）
```
projects/{project-name}/specs/{feature-name}/
├── spec.json                    # 状態管理（フェーズ、承認、進捗）
├── requirements.md              # EARS+要件定義
├── design.md                   # AI支援技術設計
├── tasks.md                    # 実装タスク分解
├── context.md                  # 機能固有コンテキスト
└── workflow.poml               # POML-based AIワークフロー定義
```

### 現在のコンテキスト受け渡しフロー（spec.json中心）
1. **orchestrator**: spec.json読み込み → 現在フェーズと状態確認
2. **sub-agent実行**: 指定されたエージェントでタスク実行
3. **結果統合**: sub-agent結果をファイル保存 + spec.json状態更新
4. **次フェーズ準備**: 次のworkflow phaseへのコンテキスト継承

## 並列実行の考慮

### 並列可能タスクの識別
```yaml
parallel_tasks:
  - [unit_test_creation, implementation]  # 同時実行可
  - [requirements, existing_code_analysis] # 同時実行可
```

### 実装上の課題
- Task()ツールは同期的実行のみ
- 真の並列実行は不可能
- 擬似的な並列化は複雑性を増す

**推奨**: 初期実装では順次実行に徹する

## エラーハンドリング

### リトライ戦略
```typescript
const MAX_RETRIES = 3;

async function callAgentWithRetry(agent: string, task: any) {
  for (let i = 0; i < MAX_RETRIES; i++) {
    try {
      return await callTask(agent, task);
    } catch (error) {
      if (i === MAX_RETRIES - 1) throw error;
      await sleep(Math.pow(2, i) * 1000); // Exponential backoff
    }
  }
}
```

### 失敗時の状態復旧
- 各エージェント実行前に状態スナップショット保存
- 失敗時は前のスナップショットから復旧
- 部分的成功の結果は保持

## 現在の実装状況と今後の優先順位

### ✅ 実装済み（Phase 1完了）
1. **統一orchestrator**: `/orchestrator`による自然言語インターフェース
2. **4コアsub-agent**: spec-generator, code-reviewer, implementation, validation
3. **spec.json状態管理**: フェーズ、承認、進捗の永続化
4. **基本エラーハンドリング**: プロンプト解析失敗時の対話的確認

### 🔄 進行中（Phase 2）
1. **POML behavior統合**: 既存behavior.pomlテンプレートとsub-agentの連携
2. **コンテキスト永続化**: State-Driven Context Persistence architecture
3. **品質ゲート**: spec.json承認フローの実装

### 📋 今後の計画（Phase 3）
1. **高度なプロンプト解析**: より柔軟な自然言語理解
2. **複数プロジェクト並列**: プロジェクト横断的な状態管理
3. **エンタープライズ機能**: 監視、分析、セキュリティ強化

## 技術的判断

### ✅ 採用済み（現在のアーキテクチャ）
- **統一orchestrator**: Anthropic仕様準拠、自然言語インターフェース
- **spec.json状態管理**: シンプルで確実なプロジェクト状態永続化
- **順次実行**: 複雑性を避け、デバッグ容易
- **POML behavior制御**: 既存ファイルによる動的挙動変更

### ❌ 避けるべき（設計で排除済み）
- **エージェント間直接通信**: 仕様違反（Task()ツール制限）
- **複雑な並列制御**: Task()ツールの制約で非効率
- **メモリベース共有**: 永続性とデバッグ性に問題
- **個別スラッシュコマンド**: `/orchestrator`統一により廃止

## リスクと対策

| リスク | 対策 |
|--------|------|
| オーケストレーター単一障害点 | チェックポイント機能で復旧可能に |
| コンテキストファイル肥大化 | 定期的クリーンアップと圧縮 |
| Task()ツール制約の変更 | 抽象化レイヤーで影響局所化 |

## 結論

**現在のアーキテクチャ**: 統一orchestrator + spec.json状態管理 + POML behavior制御

### 実装済みの成果
- **Anthropic仕様完全準拠**: Task()ツール制限に対応した設計
- **統一インターフェース**: `/orchestrator`による直感的な操作
- **柔軟な挙動制御**: 既存behavior.pomlによる動的制御
- **確実な状態管理**: spec.jsonによるプロジェクト状態永続化

### アーキテクチャの検証結果
✅ **成功している点**:
- Task()制約を回避した中央制御
- 自然言語による直感的なインターフェース  
- ファイルベースの確実なコンテキスト共有
- POML仕様準拠による標準化

✅ **今後の発展方向**:
- State-Driven Context Persistenceによるワークフロー間連携強化
- より多くのbehavior.pomlテンプレート追加
- エンタープライズ機能（監視、分析）の実装

---

**文書バージョン**: 2.0.0  
**作成日**: 2025-01-17  
**ステータス**: 実装済み・検証完了