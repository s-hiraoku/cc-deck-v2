# サブエージェント呼び出し制御アーキテクチャ検討資料

## 核心的課題

**Anthropic仕様の制約**: サブエージェント内でTask()ツール使用禁止

この制約により、以下の設計が不可能：
- エージェントAがエージェントBを呼び出す階層構造
- エージェント間の直接的な連携
- 分散型のワークフロー制御

## 解決アプローチ：中央オーケストレーター方式

### 基本原則
```
オーケストレーター（Task()使用可）
    ├── Specエージェント
    ├── Implエージェント  
    └── Validエージェント
```

**全てのエージェント呼び出しはオーケストレーター経由**

### 実装パターン

```typescript
// オーケストレーター内部
async function orchestrate(project: string, feature: string) {
  const state = loadState(project, feature);
  
  // 状態に基づくエージェント選択
  const agent = determineAgent(state);
  
  // Task()ツールでエージェント呼び出し
  const result = await callTask(agent, {
    prompt: generatePrompt(state),
    context: loadContext(state)
  });
  
  // 結果保存と状態更新
  saveResult(result);
  updateState(state, result);
}
```

## エージェント間のコンテキスト共有

### ファイルベース共有（Anthropic推奨）
```
.workflow/
├── state.json           # 現在の実行状態
└── results/
    ├── spec-001.json    # Specエージェント結果
    ├── impl-001.json    # Implエージェント結果
    └── valid-001.json   # Validエージェント結果
```

### コンテキスト受け渡しフロー
1. エージェントA実行 → 結果をファイル保存
2. オーケストレーターが次のエージェントB決定
3. エージェントBへ前の結果ファイルパスを渡す
4. エージェントBがファイル読み込みでコンテキスト取得

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

## 実装優先順位

### Phase 1: 最小実装
1. 単一オーケストレーター作成
2. 順次実行のみサポート
3. 基本的なエラーハンドリング

### Phase 2: 状態管理強化
1. 状態永続化メカニズム
2. コンテキスト受け渡し最適化
3. リトライメカニズム

### Phase 3: 高度な制御
1. 条件分岐ロジック
2. 品質ゲート統合
3. 監視・ログ強化

## 技術的判断

### 採用すべき
- **中央オーケストレーター**: Anthropic仕様準拠の唯一の方法
- **ファイルベースコンテキスト**: シンプルで確実
- **順次実行**: 複雑性を避け、デバッグ容易

### 避けるべき
- **エージェント間直接通信**: 仕様違反
- **複雑な並列制御**: Task()ツールの制約で非効率
- **メモリベース共有**: 永続性とデバッグ性に難

## リスクと対策

| リスク | 対策 |
|--------|------|
| オーケストレーター単一障害点 | チェックポイント機能で復旧可能に |
| コンテキストファイル肥大化 | 定期的クリーンアップと圧縮 |
| Task()ツール制約の変更 | 抽象化レイヤーで影響局所化 |

## 結論

**推奨アーキテクチャ**: 中央オーケストレーター + ファイルベースコンテキスト共有

この方式により：
- Anthropic仕様完全準拠
- シンプルで理解しやすい制御フロー
- デバッグとメンテナンスが容易
- 段階的な機能拡張が可能

**次のステップ**: プロトタイプ実装による検証

---

**文書バージョン**: 2.0.0  
**作成日**: 2025-01-17  
**ステータス**: 検討中