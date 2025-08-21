# CC-Deck v2 実装メモ

## 設計思想の確認

### 1. 統一Orchestratorアーキテクチャ

現在のCC-Deck v2は、**単一の`/orchestrator`コマンド**による自然言語インターフェースを採用。従来の個別コマンド（`/spec-init`, `/impl-start`等）から、自然言語プロンプト解析による統一制御に移行済み。

### 2. POML + Sub-Agentの役割分離

- **カスタムスラッシュコマンド**: 廃止 → `/orchestrator`に統一
- **サブエージェント**: 専門領域タスクの実行 (Claude Code Sub-agent準拠)
- **POML**: 既存behavior.pomlファイルによる動的挙動制御

### 3. 設計フェーズの実装方針

Claude Codeでの設計フェーズ実装における課題と対応：

#### 課題認識
- Claude Codeは実装・テスト・レビューに最適化
- 設計・仕様策定には他のエージェントも検討価値あり

#### 現在の対応方針
- **CES (Claude Code Enhanced Specification System)**: Claude Code内での仕様強化
- **MCP統合**: Context7, DeepWiki等による外部知識活用
- **段階的改善**: Phase 1でClaude Code実装、Phase 2以降で他エージェント検討

### 4. コンテキスト管理戦略

#### 現在の実装
- **成果物**: ファイルベース引き渡し（specs/*.md, src/*.*, tests/*.*）
- **状態管理**: spec.jsonによる進捗・承認状況管理
- **コンテキスト剪定**: 必要最小限の情報を構造化して受け渡し

#### コンテキストサイズ制御
```
優先度高: 前フェーズの重要決定、技術制約、品質要件
優先度中: プロジェクト設定、依存関係情報
優先度低: 詳細ログ、中間状態（ファイル参照で代替）
```

## 実装上の整合性確認

### 1. 現在アーキテクチャとの一致
- ✅ `/orchestrator`統一コマンド
- ✅ 自然言語プロンプト解析
- ✅ 4コアSub-agent (spec-generator, code-reviewer, implementation, validation)
- ✅ 既存behavior.pomlテンプレート活用

### 2. 技術選定の妥当性
- ✅ Claude Code: 実装・テスト・統合に最適
- ⚠️ 設計フェーズ: Claude Code + MCP で強化、将来的に他エージェント検討
- ✅ POML: Microsoft仕様準拠で動的挙動制御
- ✅ ファイルベース受け渡し: コンテキストサイズ制御
