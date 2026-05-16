# リーガルリサーチ自動化プロジェクト

Claude Code のマルチエージェント機能を使って、法務相談を受け取り、事実整理・論点抽出・法源調査・リスク分析の4段階を自動で実行するリーガルリサーチシステムです。

---

## このプロジェクトでできること

法務担当者や事業部からの相談文を入力すると、以下の4つの専門エージェントが順番に動作し、構造化された法務分析レポートを `output/` フォルダに生成します。

| ステップ | エージェント     | 出力ファイル            | 内容                                     |
| -------- | ---------------- | ----------------------- | ---------------------------------------- |
| 1        | fact-structuring | `output/01_facts.md`    | 事実関係の整理（関係主体・行為・不明点） |
| 2        | issue-spotting   | `output/02_issues.md`   | 法的論点の網羅的な抽出                   |
| 3        | source-research  | `output/03_sources.md`  | 関連条文・判例・ガイドラインの調査       |
| 4        | legal-analysis   | `output/04_analysis.md` | 評価軸ごとのリスク分析・判定表           |

---

## 環境構築

このプロジェクトは **uv**（高速な Python パッケージマネージャー）を使用しています。Python 3.10 以上が必要です。

### 1. uv のインストール

**macOS / Linux**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows（PowerShell）**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

インストール後、ターミナル（またはPowerShell）を再起動して `uv --version` でバージョンが表示されれば完了です。

### 2. 依存パッケージのインストール

プロジェクトルート（`legal_research/`）で以下を実行します。

```bash
uv sync
```

`uv.lock` の内容をもとに仮想環境（`.venv/`）が自動作成され、依存パッケージ（`requests` 等）がインストールされます。

### 3. 動作確認

```bash
uv run python main.py
# => Hello from legal-reserach!
```

上記が表示されれば環境構築は完了です。以降の Claude Code によるリサーチは、この仮想環境を自動的に使用します。

---

## 使い方：VS Code 拡張機能からの操作

### 1. 拡張機能のインストール

VS Code のマーケットプレイスで **Claude Code** を検索してインストールします。

```
拡張機能 ID: anthropic.claude-code
```

インストール後、VS Code を再起動するか、コマンドパレットから `Developer: Reload Window` を実行します。

- macOS：`Cmd+Shift+P`
- Windows：`Ctrl+Shift+P`

### 2. Claude Code パネルを開く

左サイドバーに Claude Code のアイコン（Anthropicロゴ）が表示されます。クリックしてパネルを開きます。

または、コマンドパレットから以下を選択します：

```
Claude Code: Open in Primary Editor
```

- macOS：`Cmd+Shift+P` → `Claude Code: Open in Primary Editor`
- Windows：`Ctrl+Shift+P` → `Claude Code: Open in Primary Editor`

これでエディタの中央にチャットパネルが表示されます。

### 3. リサーチ内容を入力する

チャットパネルに相談文を貼り付けて送信します。

**入力例：**

```
以下の件についてリーガルリサーチをお願いします。

【相談事項】
当社は取引先A社との間で、独占的な販売代理店契約を締結しています。
このたび、A社が当社の競合であるB社とも同様の代理店契約を締結しようとしていることが判明しました。
契約書には独占条項が含まれていますが、A社は「地域が異なるので問題ない」と主張しています。
当社としてどのような対応が可能か検討したいと思います。

【参照情報】
（あれば、関連資料の内容やURLを貼り付ける）
```

### 4. エージェントの実行と確認

Claude Code が4つのエージェントを順番に実行します。各エージェントの完了時に確認プロンプトが表示されるので、内容を確認して次のステップに進みます。

実行完了後、`output/` フォルダ内に分析レポートが生成されます。

---

## 出力ファイルの読み方

### output/01_facts.md — 事実整理

相談文から事実のみを抽出・整理したメモです。関係主体、行為の内容、相談者の主観評価、不明点が分離されており、「何がわかっていて何が足りないか」を把握できます。

### output/02_issues.md — 論点抽出

以下の6つの評価軸から法的論点を網羅的に洗い出したリストです：

- 契約違反の可能性
- 優越的地位の濫用
- 消費者保護
- 商慣習との適合性
- ガバナンス・コンプライアンス
- 業法上の適合性

### output/03_sources.md — 法源調査

各論点に関連する条文・判例・ガイドライン・行政資料の調査結果です。引用元が明示されています。

### output/04_analysis.md — リスク分析

法源と事実を照合した評価軸ごとのリスク判定表です。リスク程度（高・中・低）と判断理由が記載されています。

---

## プロジェクト構成

```
legal_research/
├── CLAUDE.md                  # Claude Code への動作指示（編集不要）
├── README.md                  # このファイル
├── .claude/
│   └── agents/
│       ├── fact-structuring.md   # 事実整理エージェントの定義
│       ├── issue-spotting.md     # 論点抽出エージェントの定義
│       ├── source-research.md    # 法源調査エージェントの定義
│       └── legal-analysis.md     # リスク分析エージェントの定義
└── output/                    # 生成されるレポート（実行ごとに上書き）
    ├── 01_facts.md
    ├── 02_issues.md
    ├── 03_sources.md
    └── 04_analysis.md
```

---

## 注意事項

- 新しいリサーチを開始すると、前回の `output/` フォルダは自動的に削除されます
- 過去のレポートを保存したい場合は、実行前に `output/` フォルダを別の場所にコピーしてください
- 本システムの出力はリサーチ補助を目的としており、最終的な法的判断は必ず弁護士等の専門家に確認してください
