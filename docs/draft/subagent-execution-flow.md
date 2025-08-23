# サブエージェント実行フロー仕様

## 1. 概要

このドキュメントは、CC-Deck v2 のサブエージェントの実行フローを詳細に説明します。サブエージェントは orchestrator から指示を受け、POML behavior テンプレートを適用して特定のタスクを実行する専門エージェントです。

## 2. サブエージェント実行フロー全体像

### 2.1 フロー図

```
[Orchestrator指示]
    ↓
[サブエージェント起動]
    ↓
[POML Behavior読み込み] ← optional
    ↓
[プロジェクトコンテキスト取得]
    ↓
[タスク解析・計画]
    ↓
[実行準備]
    ↓
[タスク実行]
    ↓
[結果生成]
    ↓
[状態更新] ← optional
    ↓
[結果返却]
```

### 2.2 主要コンポーネント

| コンポーネント | ファイルパス | 役割 |
|-------------|-------------|------|
| サブエージェント | `.claude/agents/{agent}.md` | エントリーポイント、基本処理定義 |
| Behavior POML | `poml/agents/{category}/{behavior}.poml` | 挙動修正テンプレート |
| Project Context | `projects/{project}/project.json` | プロジェクト基本情報 |
| Spec Context | `projects/{project}/specs/{feature}/spec.json` | フィーチャー状態管理 |
| Steering Files | `steering/` | グローバル制約・規約 |

## 3. 詳細ステップ

### Step 1: サブエージェント起動

**Orchestratorからの指示例:**
```markdown
> Use the code-reviewer subagent to review authentication module
> for fintech-app project with strict-security-review behavior applied
> Load: poml/agents/review-styles/strict-security-review.poml
> Project: fintech-app
> Feature: user-authentication
> Target: src/auth/AuthService.ts
```

**受信パラメータ:**
- `subagent_type`: code-reviewer
- `behavior_poml`: poml/agents/review-styles/strict-security-review.poml
- `project`: fintech-app
- `feature`: user-authentication
- `target`: src/auth/AuthService.ts

### Step 2: POML Behavior読み込み（Optional）

#### 2.1 Behavior指定ありの場合

**strict-security-review.poml の読み込み:**
```xml
<poml>
  <priorities>
    1. Security vulnerability detection (highest priority)
    2. Authentication/authorization flaws
    3. Input validation issues
    4. Cryptographic implementation review
  </priorities>
  
  <constraints>
    - Zero tolerance for security issues
    - OWASP Top 10 compliance required
    - Threat modeling approach mandatory
  </constraints>
  
  <approach>
    - Static code analysis with security focus
    - Manual review of authentication flows
    - Vulnerability pattern matching
    - Best practices compliance check
  </approach>
  
  <output-format>
    Generate security audit report with:
    - Risk level classification (Critical/High/Medium/Low)
    - CVSS scoring where applicable
    - Concrete remediation steps
    - Code examples for fixes
  </output-format>
</poml>
```

#### 2.2 Behavior指定なしの場合

**デフォルト挙動適用:**
```markdown
## Default Behavior (if no POML)
- Check code quality basics
- Look for common issues
- Provide simple feedback
```

### Step 3: プロジェクトコンテキスト取得

#### 3.1 プロジェクト基本情報

**project.json 読み込み:**
```json
// projects/fintech-app/project.json
{
  "project": "fintech-app",
  "description": "Fintech application with secure authentication",
  "tech_stack": ["TypeScript", "Next.js", "PostgreSQL"],
  "security_requirements": "PCI DSS Level 1",
  "created_at": "2025-01-16T09:00:00Z",
  "steering_files": ["coding-standards.md", "security-guidelines.md"]
}
```

#### 3.2 フィーチャー状態情報

**spec.json 読み込み:**
```json
// projects/fintech-app/specs/user-authentication/spec.json
{
  "project": "fintech-app",
  "feature": "user-authentication",
  "phase": "implementation-in-progress",
  "approvals": {
    "requirements": { "generated": true, "approved": true },
    "design": { "generated": true, "approved": true },
    "tasks": { "generated": true, "approved": true }
  },
  "progress": {
    "requirements": 100,
    "design": 100,
    "tasks": 75,
    "implementation": 60
  }
}
```

#### 3.3 ステアリングファイル読み込み

**coding-standards.md, security-guidelines.md など:**
```markdown
# Security Guidelines for Fintech App
- All authentication must use bcrypt with minimum 12 rounds
- JWT tokens must expire within 15 minutes
- Rate limiting: 5 failed attempts per minute
- All user inputs must be validated and sanitized
```

### Step 4: タスク解析・計画

#### 4.1 POML適用済み挙動分析

**Behavior適用後の実行計画:**
```markdown
## 実行計画 (strict-security-review applied)

### 優先度1: セキュリティ脆弱性検出
- SQL injection チェック
- XSS vulnerability チェック  
- Authentication bypass チェック
- Authorization flaws チェック

### 優先度2: 暗号化実装レビュー
- Password hashing アルゴリズム確認
- JWT token セキュリティ確認
- Session management セキュリティ確認

### 優先度3: 入力検証問題
- User input validation 確認
- Data sanitization 確認
- Type safety 確認
```

#### 4.2 対象ファイル特定

**Globパターンでファイル検索:**
```bash
Target: src/auth/AuthService.ts
Related files: src/auth/*.ts, src/middleware/auth.ts, src/types/auth.ts
```

### Step 5: 実行準備

#### 5.1 ツール選択

**必要なツールの決定:**
```markdown
Required tools based on behavior:
- Read: ソースコード読み込み
- Grep: セキュリティパターン検索
- Glob: 関連ファイル発見
```

#### 5.2 検索パターン準備

**セキュリティ重視レビューのパターン:**
```regex
Security patterns to search:
- SQL injection: /\$\{.*\}|concat|exec|eval/
- XSS: /innerHTML|dangerouslySetInnerHTML/
- Auth bypass: /== null|== undefined|!auth/
- Weak crypto: /md5|sha1|des/
```

### Step 6: タスク実行

#### 6.1 ソースコード読み込み

```typescript
// src/auth/AuthService.ts
export class AuthService {
  async login(email: string, password: string) {
    // 🔴 Critical: Plain text password comparison
    const user = await User.findOne({ email, password });
    
    if (user) {
      // 🟡 High: Weak JWT secret
      const token = jwt.sign({ id: user.id }, 'secret123');
      return { token };
    }
    
    throw new Error('Invalid credentials');
  }
}
```

#### 6.2 セキュリティパターン分析

**検出された問題:**
```markdown
🔴 Critical Issues Found:
1. Plain text password storage/comparison (Line 4)
2. Weak JWT secret key (Line 8)

🟡 High Issues Found:  
1. No rate limiting implementation
2. Error message information disclosure (Line 12)

🟠 Medium Issues Found:
1. No input validation for email format
2. Missing password complexity requirements
```

#### 6.3 OWASP Top 10 チェック

**コンプライアンス確認:**
```markdown
OWASP Top 10 Compliance Check:
❌ A01: Broken Access Control - Missing authorization checks
❌ A02: Cryptographic Failures - Plain text passwords  
❌ A03: Injection - No input validation
❌ A07: Identification and Authentication Failures - Weak implementation
```

### Step 7: 結果生成

#### 7.1 POML定義の出力フォーマット適用

**セキュリティ監査レポート生成:**
```markdown
# セキュリティ監査レポート

## 基本情報
- **プロジェクト**: fintech-app
- **フィーチャー**: user-authentication  
- **対象ファイル**: src/auth/AuthService.ts
- **レビュー種別**: strict-security-review
- **実行日時**: 2025-01-20 15:30:00

## 検出された問題

### 🔴 Critical (2件)
1. **Plain Text Password Storage**
   - **場所**: src/auth/AuthService.ts:4
   - **CVSS Score**: 9.8
   - **説明**: パスワードがプレーンテキストで保存・比較されている
   - **修正方法**: bcryptを使用してハッシュ化する
   ```typescript
   // Before (危険)
   const user = await User.findOne({ email, password });
   
   // After (安全)
   const user = await User.findOne({ email });
   const isValid = await bcrypt.compare(password, user.hashedPassword);
   ```

2. **Weak JWT Secret**
   - **場所**: src/auth/AuthService.ts:8
   - **CVSS Score**: 8.5
   - **説明**: 脆弱なJWTシークレットキー
   - **修正方法**: 環境変数で強力な秘密鍵を設定
   ```typescript
   const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET);
   ```

### 🟡 High (2件)
1. **Missing Rate Limiting**
   - **影響**: ブルートフォース攻撃リスク
   - **修正**: express-rate-limit実装

2. **Information Disclosure**
   - **場所**: src/auth/AuthService.ts:12
   - **修正**: 汎用エラーメッセージに変更

## 推奨事項

### 即座に修正すべき項目
1. bcrypt によるパスワードハッシュ化（12ラウンド以上）
2. 強力なJWT秘密鍵の設定
3. Rate limiting の実装

### セキュリティベストプラクティス
1. Input validation の実装
2. HTTPS強制の設定
3. セキュリティヘッダーの設定
4. ログ監視の実装

## 次のアクション
1. Critical問題の即座修正
2. 修正後の再レビュー実行
3. セキュリティテストの実行
4. コードレビューでの承認取得

## コンプライアンス状況
- **PCI DSS**: ❌ 不適合（Critical問題により）
- **OWASP Top 10**: ❌ 4項目で不適合
- **セキュリティ基準**: ❌ 要修正

**推奨次コマンド**: /orchestrator implement security fixes for fintech-app user-auth
```

### Step 8: 状態更新（Optional）

#### 8.1 spec.json更新

**レビュー履歴の記録:**
```json
{
  "lastReview": {
    "date": "2025-01-20T15:30:00Z",
    "type": "security",
    "behavior": "strict-security-review",
    "agent": "code-reviewer",
    "issues": {
      "critical": 2,
      "high": 2,
      "medium": 0,
      "low": 0
    },
    "compliance": {
      "pci_dss": false,
      "owasp_top10": false
    }
  }
}
```

#### 8.2 プロジェクト全体への影響

**project.json への記録:**
```json
{
  "security_status": {
    "last_audit": "2025-01-20T15:30:00Z",
    "critical_issues": 2,
    "compliance_status": "non-compliant",
    "next_review_due": "2025-01-21T15:30:00Z"
  }
}
```

### Step 9: 結果返却

**Orchestratorへの報告:**
```markdown
## Code Review完了

**実行サマリー:**
- サブエージェント: code-reviewer
- Behavior: strict-security-review
- プロジェクト: fintech-app
- 対象: src/auth/AuthService.ts
- 実行時間: 45秒

**結果:**
- Critical問題: 2件検出
- セキュリティコンプライアンス: 不適合
- 即座修正推奨

**詳細レポート:** [上記セキュリティ監査レポート]

**推奨次アクション:**
/orchestrator implement security fixes for fintech-app user-auth
```

## 4. サブエージェント種別と特殊化

### 4.1 code-reviewer

**特殊化内容:**
- コード品質分析
- セキュリティ脆弱性検出
- パフォーマンス問題識別
- ベストプラクティス準拠チェック

**利用可能Behavior:**
- strict-security-review
- performance-focused
- accessibility-audit
- code-quality-check

### 4.2 implementation-agent

**特殊化内容:**
- TDD実装サイクル
- アーキテクチャ準拠実装
- 品質ゲート確認
- テストカバレッジ確保

**利用可能Behavior:**
- rapid-prototyping
- enterprise-development
- test-driven-development
- microservices-implementation

### 4.3 spec-generator

**特殊化内容:**
- EARS+要件生成
- 技術設計文書作成
- タスク分解と優先順位付け
- 承認ゲート管理

**利用可能Behavior:**
- agile-specification
- enterprise-requirements
- user-story-driven
- technical-architecture

## 5. Behavior POML適用パターン

### 5.1 単一Behavior適用

```xml
<poml>
  <priorities>セキュリティ重視</priorities>
  <constraints>ゼロトレランス</constraints>
  <approach>静的解析 + 手動レビュー</approach>
</poml>
```

### 5.2 複合Behavior適用（将来）

```xml
<poml>
  <!-- セキュリティ + パフォーマンス -->
  <include src="security-focused.poml" />
  <include src="performance-optimization.poml" />
  
  <priority-override>
    security > performance > maintainability
  </priority-override>
</poml>
```

### 5.3 プロジェクト固有Behavior（将来）

```xml
<poml>
  <!-- プロジェクト固有の制約 -->
  <project-constraints src="projects/fintech-app/security-requirements.poml" />
  
  <!-- 標準Behaviorを基盤として使用 -->
  <extends src="strict-security-review.poml" />
  
  <!-- プロジェクト固有の追加制約 -->
  <additional-constraints>
    - PCI DSS Level 1 compliance required
    - SOX compliance for financial data
    - GDPR compliance for EU users
  </additional-constraints>
</poml>
```

## 6. エラーハンドリング

### 6.1 POML読み込みエラー

```markdown
入力: behavior = "unknown-behavior"
処理: POML file not found
出力: "Behavior template 'unknown-behavior' not found. Using default behavior."
実行: デフォルト挙動で継続
```

### 6.2 プロジェクトコンテキストエラー

```markdown
入力: project = "nonexistent-project"
処理: project.json not found
出力: "Project 'nonexistent-project' not found. Please verify project name."
実行: エラー終了、Orchestratorに報告
```

### 6.3 ツール不足エラー

```markdown
入力: サブエージェントにRead toolが未割り当て
処理: Required tool missing
出力: "Missing required tool: Read. Please check agent configuration."
実行: エラー終了、設定見直し要求
```

### 6.4 対象ファイル不存在

```markdown
入力: target = "src/nonexistent.ts"
処理: Target file not found
出力: "Target file 'src/nonexistent.ts' not found. Searching for similar files..."
実行: Glob patternで類似ファイル検索、候補提示
```

## 7. パフォーマンス最適化

### 7.1 コンテキスト読み込み最適化

**必要最小限のファイル読み込み:**
```markdown
1. project.json (always)
2. spec.json (if feature specified)
3. behavior.poml (if behavior specified)
4. steering files (only relevant ones)
```

### 7.2 並列処理

**独立処理の並列実行:**
```markdown
Parallel execution:
- File reading (Read tool)
- Pattern searching (Grep tool)
- Related file discovery (Glob tool)
```

### 7.3 キャッシュ戦略

**コンテキストキャッシュ:**
```markdown
Cache項目:
- project.json contents (5 minutes)
- steering files (10 minutes)  
- behavior.poml templates (15 minutes)
```

## 8. 品質保証

### 8.1 実行前検証

```markdown
Pre-execution validation:
1. Required tools availability check
2. Target file/directory existence check  
3. POML template syntax validation
4. Project context integrity check
```

### 8.2 実行中監視

```markdown
Runtime monitoring:
1. Tool execution timeout monitoring
2. Memory usage tracking
3. Error rate monitoring
4. Progress reporting
```

### 8.3 実行後検証

```markdown
Post-execution validation:
1. Output format compliance check
2. Required sections presence check
3. Action item completeness check
4. State update accuracy check
```

## 9. セキュリティ考慮事項

### 9.1 アクセス制御

```markdown
Access control:
- Read-only access to source code
- No write access to project files (except state updates)
- No external network access
- Sandboxed execution environment
```

### 9.2 機密情報保護

```markdown
Sensitive data protection:
- No API keys or secrets in logs
- Sanitized error messages
- Secure temporary file handling
- Memory cleanup after execution
```

### 9.3 監査ログ

```markdown
Audit logging:
- Agent execution start/end times
- Files accessed during execution
- Commands executed and results
- State changes made
```

## 10. トラブルシューティング

### 10.1 一般的な問題

| 問題 | 原因 | 解決方法 |
|------|------|---------|
| POML読み込み失敗 | ファイル不存在/構文エラー | パス確認、POML構文検証 |
| ツール実行エラー | 権限不足/ファイル不存在 | 権限確認、対象確認 |
| 出力フォーマット不正 | Behavior定義不備 | POML template見直し |
| 状態更新失敗 | JSON形式エラー | spec.json構文確認 |

### 10.2 診断コマンド

```bash
# サブエージェント設定確認
/orchestrator diagnose subagent code-reviewer

# Behavior template確認  
/orchestrator validate behavior strict-security-review

# プロジェクトコンテキスト確認
/orchestrator check context fintech-app user-auth
```

## 11. まとめ

サブエージェント実行フローは以下の特徴を持ちます：

1. **Behavior駆動**: POML templateで動的に挙動を変更
2. **コンテキスト認識**: プロジェクト固有の制約と要件を考慮
3. **品質重視**: 実行前後の検証で結果品質を保証
4. **拡張可能**: 新しいBehaviorやサブエージェントの追加に対応
5. **エラー耐性**: 適切なエラーハンドリングと回復機能

この設計により、柔軟で堅牢なタスク実行が可能になり、プロジェクトの特性に応じた最適化された処理を提供できます。