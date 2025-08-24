# CC-Deck v2 テスト用コマンド

## 概要
CC-Deck v2の3層コマンド体系をテストするためのダミーコマンドシステムです。

## 構成

### 第1層: Orchestrator
- `dummy-orchestrator` - メインのオーケストレーターコマンド
- `dummy-orchestrator.poml` - オーケストレーターの動作定義

### 第2層: カスタムコマンド
- `command-zundamon` - ずんだもんキャラクターコマンド
- `command-rum-chan` - ラムちゃんキャラクターコマンド

### 第3層: POML定義
- `zundamon.poml` - ずんだもんの口調と性格定義
- `rum.poml` - ラムちゃんの口調と性格定義

## 実行方法

Claude Codeでカスタムスラッシュコマンドとして実行：

```bash
/dummy-orchestrator
```

## 期待される動作

1. `dummy-orchestrator` が実行される
2. `dummy-orchestrator.poml` が読み込まれる
3. `command-zundamon` が呼び出され、ずんだもんが話す
4. `command-rum-chan` が呼び出され、ラムちゃんが話す

両方のキャラクターの発言が表示されることで、3層構造の動作を確認できます。