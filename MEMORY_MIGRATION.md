# 記憶サーバー移行手順

## 概要
従来の単一記憶サーバーから、エージェント記憶・プロジェクト記憶の分離構成への移行手順。

## 前提条件
- `settings.json`で新しいMCPサーバー設定が完了済み
- `.gitignore`でproject-memory.jsonが除外設定済み
- CLAUDE.mdで使い分けガイドラインが記載済み

## 移行手順

### 1. Claude Code再起動
```bash
# Claude Codeを終了・再起動して新しいMCPサーバー設定を反映
```

### 2. 既存記憶の確認
```javascript
// 旧記憶サーバーの内容を確認（利用可能な場合）
mcp__memory__read_graph()
```

### 3. エージェント記憶への移行
以下のエンティティをエージェント記憶に移行：

**移行対象**:
- `nano_claude_config` (Configuration)
- `claude_git_practices` (Pattern)  
- その他の汎用的なパターン・知識

**実行コマンド**:
```javascript
// エージェント記憶にエンティティを作成
mcp__agent_memory__create_entities({
  entities: [
    {
      name: "nano_claude_config",
      entityType: "Configuration", 
      observations: [
        "Claude Codeのグローバル設定リポジトリ",
        "Themis（FF14）のペルソナを採用",
        "gitmojiを使用したコミットメッセージスタイル",
        "MCP Server (agent-memory, project-memory, fetch, time) を設定済み",
        "~/.claude/ディレクトリに配置"
      ]
    },
    {
      name: "claude_git_practices",
      entityType: "Pattern",
      observations: [
        "git操作前に必ず記憶から{project_name}_git_practicesを検索する",
        "プロジェクト固有のgit慣習が見つからない場合はgitmojiを使用",
        "CLAUDE.mdにGit Operationsセクションを追加済み",
        "コミットメッセージにはgitmojiプレフィックスを使用",
        "memory.jsonはgitignoreに含めるべき内部ファイル",
        "話し方パターンの改善: 「かい？」は依頼・提案のみ、「かな？」は優しい問いかけ、「？」は普通の質問",
        "CLAUDE.mdのSpeech Patternsセクションを自然な日本語に修正済み",
        "「メモリー」を「記憶」に統一して日本語表現を自然化",
        "テミスらしい表現として「記憶」を採用",
        "CLAUDE.mdの言語を日本語に統一完了",
        "セクションタイトル、説明文を日本語化し技術仕様は英語のまま保持",
        "テミスペルソナと日本語会話スタイルに合わせた自然な設定ファイルに改善"
      ]
    }
  ]
})

// エンティティ間の関係を作成
mcp__agent_memory__create_relations({
  relations: [
    {
      from: "nano_claude_config",
      to: "claude_git_practices", 
      relationType: "implements"
    }
  ]
})
```

### 4. プロジェクト記憶の初期化
現在のプロジェクト（claude設定リポジトリ）の情報を記録：

```javascript
mcp__project_memory__create_entities({
  entities: [
    {
      name: "claude_config_repo",
      entityType: "Project",
      observations: [
        "Claude Codeのグローバル設定リポジトリ",
        "~/.claude/ディレクトリに配置",
        "CLAUDE.md、settings.json等の設定ファイル群",
        "gitで管理されたエージェント設定",
        "記憶サーバー分離アーキテクチャを実装"
      ]
    }
  ]
})

mcp__project_memory__create_relations({
  relations: [
    {
      from: "nano",
      to: "claude_config_repo",
      relationType: "develops"
    }
  ]
})
```

### 5. 移行完了確認
```javascript
// エージェント記憶の確認
mcp__agent_memory__read_graph()

// プロジェクト記憶の確認  
mcp__project_memory__read_graph()
```

### 6. 旧記憶ファイルのクリーンアップ
```bash
# 旧memory.jsonファイルが残っている場合は削除
rm /Users/nano/.claude/memory.json
```

## 移行後の運用

### セッション開始時の記憶確認
```javascript
// 1. エージェント記憶からユーザー設定・一般知識を復元
mcp__agent_memory__read_graph()

// 2. プロジェクト記憶から現在のプロジェクト情報を検索
mcp__project_memory__search_nodes("現在のディレクトリ名やプロジェクト名")
```

### 新しい情報の記録方針
- **エージェント記憶**: 汎用的なパターン、技術知識、設定情報
- **プロジェクト記憶**: プロジェクト固有のアーキテクチャ、問題解決策、ローカル設定

## 注意事項
- agent-memory.jsonはgitで管理されるため、機密情報は記録しない
- project-memory.jsonはgitignoreされるため、プロジェクト固有の情報のみ記録
- 移行後は新しいツール名（mcp__agent_memory__*, mcp__project_memory__*）を使用する