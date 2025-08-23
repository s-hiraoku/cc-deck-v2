# CC-Deck v2 基本骨格

## 🎯 CC-Deck v2とは？

**「時間のかかるワークフローを単一コマンドに変える」AI-native開発プラットフォーム**

```
従来: 何時間もかけて手動で行う開発ワークフロー
     ↓
CC-Deck v2: 一つのコマンドで自動化されたワークフロー
```

## 🏗️ 基本構造（3つの柱）

```mermaid
graph TB
    subgraph Interface["🎯 統一インターフェース"]
        User[👤 開発者] --> Orchestrator[orchestrator コマンド]
    end
    
    subgraph AIs["🤖 専門AI部隊"]
        SpecGen[📋 仕様作成AI]
        CodeReview[🔍 コードレビューAI] 
        Implementation[⚙️ 実装AI]
        Validation[✅ 検証AI]
    end
    
    subgraph StateSystem["📊 状態管理"]
        State[spec.json プロジェクト状態]
        Approval[👤 人間承認ポイント]
    end
    
    Orchestrator --> SpecGen
    Orchestrator --> CodeReview
    Orchestrator --> Implementation
    Orchestrator --> Validation
    
    SpecGen --> State
    CodeReview --> State
    Implementation --> State
    Validation --> State
    
    State --> Approval
    Approval --> State
```

## 🚀 基本的な使い方

### シンプルな例
```bash
# 1. 新しい機能の仕様を作成
/orchestrator create specification for my-app user-login

# 2. 仕様を承認（人間）
/orchestrator approve requirements for my-app user-login

# 3. 実装を開始
/orchestrator implement my-app user-login

# 4. テスト・検証
/orchestrator validate my-app user-login
```

## 🎪 3つの主要ワークフロー

```mermaid
flowchart LR
    subgraph SpecPhase["📋 SPEC 仕様"]
        Spec1[要件定義] --> Spec2[技術設計] --> Spec3[実装計画]
        Spec3 --> SpecApproval[👤 人間承認]
    end
    
    subgraph ImplPhase["⚙️ IMPL 実装"]
        Impl1[コード生成] --> Impl2[テスト作成] --> Impl3[品質確認]
        Impl3 --> QualityGate[✅ 品質ゲート]
    end
    
    subgraph ValidPhase["✅ VALID 検証"]
        Valid1[統合テスト] --> Valid2[性能測定] --> Valid3[最終確認]
        Valid3 --> FinalApproval[👤 最終承認]
    end
    
    SpecApproval --> Impl1
    QualityGate --> Valid1
```

## 🧠 4つの専門AI

```mermaid
graph LR
    subgraph AITeam["🧠 4つの専門AI"]
        SpecGen["📋 spec-generator<br/>要件理解・設計・計画"]
        CodeReview["🔍 code-reviewer<br/>セキュリティ・品質確認"]
        Implementation["⚙️ implementation<br/>コード生成・テスト作成"]
        Validation["✅ validation<br/>動作確認・性能測定"]
    end
    
    SpecGen --> CodeReview
    CodeReview --> Implementation
    Implementation --> Validation
```

## 📊 状態管理の仕組み

### spec.json（プロジェクトの頭脳）
```json
{
  "project": "my-app",
  "feature": "user-login", 
  "phase": "implementation",
  "approvals": {
    "requirements": { "approved": true },
    "design": { "approved": true },
    "implementation": { "approved": false }
  },
  "progress": {
    "requirements": 100,
    "design": 100, 
    "implementation": 45
  }
}
```

## 👤 人間の役割

```mermaid
graph LR
    subgraph AIRole["🤖 AIが得意なこと"]
        AI1[コード生成]
        AI2[テスト作成]
        AI3[品質チェック]
        AI4[文書作成]
    end
    
    subgraph HumanRole["👤 人間が必要なこと"]
        Human1[要件承認]
        Human2[設計承認]
        Human3[最終判断]
        Human4[戦略決定]
    end
    
    AI1 --> Human1
    AI2 --> Human2
    AI3 --> Human3
    AI4 --> Human4
```

## 🔄 基本フロー

```mermaid
sequenceDiagram
    participant User as 👤 開発者
    participant Orch as 🎯 Orchestrator
    participant AI as 🤖 専門AI
    participant State as 📊 状態管理
    
    User->>Orch: /orchestrator create spec for my-app login
    Orch->>AI: spec-generator実行
    AI->>State: 仕様書作成完了
    State->>User: 👤 承認お願いします
    User->>State: ✅ 承認
    State->>Orch: 次のフェーズ開始可能
    Orch->>User: implementation開始しますか？
```

## 💡 CC-Deck v2の価値

### Before（従来）
- ❌ 手動で何時間もかかる作業
- ❌ ステップを忘れやすい
- ❌ 品質にバラつきがある
- ❌ プロジェクト間で一貫性がない

### After（CC-Deck v2）
- ✅ 1つのコマンドで自動化
- ✅ 必要なステップを漏れなく実行
- ✅ 一定の品質を保証
- ✅ すべてのプロジェクトで統一された流れ

## 🎨 設計思想

### シンプルな原則
1. **統一インターフェース**: すべて `/orchestrator` から
2. **専門分業**: 各AIが得意分野に集中
3. **人間中心**: 重要な判断は必ず人間が行う
4. **状態透明性**: いつでも進捗・状況が確認できる
5. **中断・再開**: どこで止めても続きから始められる

---

**CC-Deck v2 = 1つのコマンドで、プロの開発チームと同じワークフローを自動実行**

---

**作成日**: 2025-08-22  
**バージョン**: 1.0.0  
**ステータス**: 基本骨格確定