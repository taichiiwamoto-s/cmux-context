# cmux-context

[cmux](https://cmux.co) の全ワークスペースにおける Claude Code のコンテキスト使用率を可視化する CLI ツール。サイドバーへの常時表示にも対応。

![Screenshot](screenshot.png)

## 特徴

- 全ワークスペースのコンテキスト使用率（%）を一覧表示
- 色分けプログレスバー（緑 < 50% / 黄 50-79% / 赤 >= 80%）
- モデル名（Opus / Sonnet / Haiku）と行数を表示
- レートリミット（5h / 7d）の共有表示
- **サイドバーモード**: 各ワークスペースのサイドバーに使用率をリアルタイム表示
- **マルチペイン対応**: 1ワークスペース内の複数Claude Codeインスタンスを個別に検出・表示

## 動作要件

- [cmux](https://cmux.co)（macOS）
- Bash 4+
- 各ワークスペースで Claude Code のステータスラインが有効であること

## インストール

```bash
curl -fsSL https://raw.githubusercontent.com/taichiiwamoto-s/cmux-context/main/cmux-context -o /usr/local/bin/cmux-context
chmod +x /usr/local/bin/cmux-context
```

または clone してシンボリックリンク:

```bash
git clone https://github.com/taichiiwamoto-s/cmux-context.git
ln -s "$(pwd)/cmux-context/cmux-context" /usr/local/bin/cmux-context
```

## 使い方

### ワンショット（ターミナル出力）

```bash
cmux-context
```

### サイドバー常駐モード

```bash
cmux-context start                  # 起動（デフォルト10秒間隔）
cmux-context start --interval 5     # 5秒間隔で起動
cmux-context start --progress       # プログレスバーも表示
cmux-context status                 # デーモンの動作状態を確認
cmux-context stop                   # 停止 + サイドバーをクリア
```

`start` を実行すると、専用ワークスペース（`ctx-monitor`）が作成され、バックグラウンドで更新ループが動作します。各ワークスペースのサイドバーに以下が表示されます:

- **ステータスpill**: 🧠 XX%（使用率に応じて緑/黄/赤）
- **プログレスバー**: `--progress` オプションで追加表示可

`stop` を実行すると、デーモン停止・サイドバーのクリア・専用ワークスペースの削除がすべて自動で行われます。

### 出力例（ワンショット）

```
  cmux Context Monitor
  ────────────────────────────────────────────────────────

  Claude Code
  ████████░░░░░░░░░░░░  38%  Opus 4.6  +12/-3

  VI分析ツール
  ████████████████░░░░  78%  Sonnet 4.6  +5/-1

  ────────────────────────────────────────────────────────
  Rate Limits (shared)
  5h ▰▰▰▱▱▱▱▱▱▱
  7d ▰▱▱▱▱▱▱▱▱▱
```

## 仕組み

1. `cmux list-workspaces` で全ワークスペースを検出
2. `cmux list-panes` / `list-pane-surfaces` で各ワークスペース内の全サーフェスを列挙
3. `cmux read-screen --surface` で各Claude Codeサーフェスの画面を読み取り
4. ステータスライン（`XX% | +N/-N`）からコンテキスト%・モデル名・行数をパース
5. **ワンショット**: ターミナルに色分けプログレスバーを描画
6. **サイドバーモード**: `cmux set-status` でステータスpillを更新（複数インスタンスは個別表示）

### 色の閾値

| 使用率 | 色 | カラーコード |
|--------|------|-----------|
| < 50%  | 緑   | `#97C9C3` |
| 50-79% | 黄   | `#E5C07B` |
| >= 80% | 赤   | `#E06C75` |

## ライセンス

[MIT](LICENSE)
